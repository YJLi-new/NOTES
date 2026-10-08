# MP5 Diffusion Notebook

> Notebook converted to Markdown (outputs stripped; see the report PDF for results).

# MP5: Training Your Diffusion Model!

## Setup environment

```python
# Import essential modules. Feel free to add whatever you need.
import matplotlib.pyplot as plt
import torch # added
from torch import optim # added
from torch import nn
import torch.nn.functional as F
from torch.utils.data import DataLoader
from torchvision.datasets import MNIST
from torchvision.transforms import ToTensor
```

## Visualization helper function

```python
def visualize_images_with_titles(images: torch.Tensor, column_names: list[str]):
    """
    Visualize images as a grid and title the columns with the provided names.

    Args:
        images: (N, C, H, W) tensor of images, where N is (number of rows * number of columns)
        column_names: List of column names for the titles.

    Example usage:
    visualize_images_with_titles(torch.randn(16, 1, 32, 32), ['1', '2', '3', '4'])
    """
    num_images, num_columns = images.shape[0], len(column_names)
    assert num_images % num_columns == 0, 'Number of images must be a multiple of the number of columns.'

    num_rows = num_images // num_columns
    fig, axes = plt.subplots(num_rows, num_columns, figsize=(num_columns * 1, num_rows * 1))

    for i, ax in enumerate(axes.flat):
        img = images[i].permute(1, 2, 0).cpu().numpy()
        ax.imshow(img, cmap='gray')
        ax.axis('off')
        if i < num_columns:
            ax.set_title(column_names[i % num_columns])

    plt.tight_layout()
    plt.show()
```

# Part 1: Training a Single-step Denoising UNet


## Implementing Simple and Composed Ops

```python
class Conv(nn.Module):
    def __init__(self, in_channels: int, out_channels: int):
        super().__init__()
        self.conv = nn.Sequential(
            nn.Conv2d(in_channels, out_channels, kernel_size=3, padding=1), # padding=1 for same H,W
            nn.BatchNorm2d(out_channels),
            nn.GELU()
        )

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.conv(x)


class DownConv(nn.Module):
    def __init__(self, in_channels: int, out_channels: int):
        super().__init__()
        self.downconv = nn.Sequential(
            nn.Conv2d(in_channels, out_channels, kernel_size=3, stride=2, padding=1), # stride=2 for H/2, W/2
            nn.BatchNorm2d(out_channels),
            nn.GELU()
        )

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.downconv(x)


class UpConv(nn.Module):
    def __init__(self, in_channels: int, out_channels: int):
        super().__init__()
        self.upconv = nn.Sequential(
        nn.ConvTranspose2d(in_channels, out_channels, 2, 2, 0, bias=False),
        nn.BatchNorm2d(out_channels),
        nn.GELU())

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.upconv(x)


class Flatten(nn.Module):
    def __init__(self):
        super().__init__()
        self.flatten = nn.AvgPool2d(kernel_size=7)

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        assert x.shape[-2:] == (7,7)
        return self.flatten(x)


class Unflatten(nn.Module):
    def __init__(self, in_channels: int):
        super().__init__()
        self.unflatten = nn.ConvTranspose2d(in_channels, in_channels, 7, stride=7)

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.unflatten(x)


class ConvBlock(nn.Module):
    def __init__(self, in_channels: int, out_channels: int):
        super().__init__()
        self.convblock = nn.Sequential(
            Conv(in_channels, out_channels),
            Conv(out_channels, out_channels)
        )

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.convblock(x)


class DownBlock(nn.Module):
    def __init__(self, in_channels: int, out_channels: int):
        super().__init__()
        self.downblock = nn.Sequential(
            DownConv(in_channels, out_channels),
            ConvBlock(out_channels, out_channels)
        )

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.downblock(x)


class UpBlock(nn.Module):
    def __init__(self, in_channels: int, out_channels: int):
        super().__init__()
        self.upblock = nn.Sequential(
            UpConv(in_channels, out_channels),
            ConvBlock(out_channels, out_channels)
        )

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.upblock(x)
```

## Implementing Unconditional UNet

```python
class UnconditionalUNet(nn.Module):
    def __init__(
        self,
        in_channels: int,
        num_hiddens: int,
    ):
        super().__init__()
        self.down1 = DownBlock(in_channels, num_hiddens)  # downsampling path
        self.down2 = DownBlock(num_hiddens, num_hiddens * 2)

        self.flatten = Flatten()
        self.unflatten = Unflatten(num_hiddens * 2)

        self.up1 = UpBlock(in_channels=(num_hiddens * 4), out_channels=(num_hiddens * 2))  # upsampling path
        self.up2 = UpBlock(in_channels=(num_hiddens * 2 + num_hiddens), out_channels=num_hiddens)

        self.outconv = nn.Conv2d(num_hiddens, in_channels, kernel_size=1)

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        assert x.shape[-2:] == (28, 28), "Expect input shape to be (28, 28)."

        skip1 = self.down1(x) # (N,  D, 14, 14)
        skip2 = self.down2(skip1) # (N, 2D,  7,  7)

        h = self.flatten(skip2) # (N, 2D, 1, 1)
        h = self.unflatten(h) # (N, 4D, 7, 7)

        h = torch.cat([h, skip2], dim=1)   # (N,6D,7,7)
        h = self.up1(h)                    # (N,2D,14,14)
        h = torch.cat([h, skip1], dim=1)   # (N,3D,14,14)
        h = self.up2(h)                    # (N, D,28,28)

        return self.outconv(h) # (N, 1, 28, 28)
```

## Visualizing the noising process

```python
dataset = MNIST(root="data", download=True, transform=ToTensor(), train=True)

num_samples = 5
sigmas = [0.0, 0.2, 0.4, 0.5, 0.6, 0.8, 1.0]

sample_imgs_orig = torch.stack([dataset[i][0] for i in range(num_samples)])

grid_imgs = []
column_titles = ['Original'] + [f'Noise σ={s:.1f}' for s in sigmas]

for i in range(num_samples):
    grid_imgs.append(sample_imgs_orig[i])
    for sigma in sigmas:
        noise = torch.randn_like(sample_imgs_orig[i]) * sigma
        noisy_img = sample_imgs_orig[i] + noise
        grid_imgs.append(torch.clamp(noisy_img, 0.0, 1.0))

visualize_images_with_titles(
    torch.stack(grid_imgs),
    column_titles
)
```

## Training a Single-Step Unconditional UNet

- Plot the loss curve
- Sample results on the test set

```python
dataset = MNIST(root='data', download=True, transform=ToTensor(), train=True)
dataloader = DataLoader(dataset, batch_size=256, shuffle=True)

device = torch.device('cuda')
eps = 5
lr = 1e-4
sigma = 0.5
num_levels = 128

model = UnconditionalUNet(1, num_levels).to(device)
optimizer = optim.Adam(model.parameters(), lr=lr)
crt = nn.MSELoss()

test_loader  = DataLoader(MNIST('data', train=False, transform=ToTensor()), batch_size=8, shuffle=False)
fixed_test, j = next(iter(test_loader))
fixed_test = fixed_test.to(device)

train_losses = []

for ep in range(eps):
    model.train()
    running = 0.0
    for clean, j in dataloader:
        clean = clean.to(device)
        noisy = clean + torch.randn_like(clean) * sigma

        loss = crt(model(noisy), clean)
        optimizer.zero_grad(); loss.backward(); optimizer.step()
        running += loss.item() * clean.size(0)

    train_losses.append(running / len(dataloader.dataset))
    print(f"epoch {ep+1}: {train_losses[-1]:.4f}")

    if ep in (0, eps-1):
        model.eval()
        with torch.no_grad():
            noisy = fixed_test + torch.randn_like(fixed_test)*sigma
            deno = torch.clamp(model(noisy), 0, 1).cpu()

        gallery = torch.cat([fixed_test.cpu(), noisy.cpu(), deno], dim=0)
        visualize_images_with_titles(
            gallery,
            [f'Sample {i+1}' for i in range(fixed_test.size(0))]
        )

plt.figure(figsize=(6,3))
plt.plot(range(1, eps+1), train_losses, marker='o')
plt.xlabel('Epoch'); plt.ylabel('MSE'); plt.title('Training Loss'); plt.grid(True)
plt.show()
```

## Out-of-Distribution Testing

```python
ood_sigmas = [0.0, 0.2, 0.4, 0.5, 0.6, 0.8, 1.0]

model.eval()
for sigma in ood_sigmas:
    with torch.no_grad():
        noisy = fixed_test + torch.randn_like(fixed_test) * sigma
        denoised = torch.clamp(model(noisy), 0.0, 1.0).cpu()

    panel = torch.cat([fixed_test.cpu(), noisy.cpu(), denoised], dim=0)
    print(f"OOD σ = {sigma:.1f}")
    visualize_images_with_titles(
        panel,
        [f"Sample {i+1}" for i in range(fixed_test.size(0))]
    )
```

# Part 2: Training a Diffusion Model

## Implementing a Time-conditioned UNet

```python
class FCBlock(nn.Module):
    def __init__(self, in_channels: int, out_channels: int):
        super().__init__()
        self.fcblock = nn.Sequential(
            nn.Linear(in_channels, out_channels),
            nn.GELU(),
            nn.Linear(out_channels, out_channels),
            nn.GELU()
        )

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.fcblock(x)


class TimeConditionalUNet(nn.Module):
    def __init__(
        self,
        in_channels: int,
        num_classes: int,
        num_hiddens: int,
    ):
        super().__init__()

        self.down1 = DownBlock(in_channels, num_hiddens)
        self.down2 = DownBlock(num_hiddens, num_hiddens * 2)

        self.flatten = Flatten()
        self.unflatten_base = Unflatten(num_hiddens * 2)

        self.fc1 = FCBlock(1, num_hiddens * 2)
        self.fc2 = FCBlock(1, num_hiddens * 2)
        self.up1 = UpBlock(num_hiddens * 4, num_hiddens * 2)
        self.up2 = UpBlock(num_hiddens * 3, num_hiddens)

        self.outconv = nn.Conv2d(num_hiddens, in_channels, kernel_size=1)

    def forward(
        self,
        x: torch.Tensor,
        t: torch.Tensor,
    ) -> torch.Tensor:
        """
        Args:
            x: (N, C, H, W) input tensor.
            t: (N,) normalized time tensor.

        Returns:
            (N, C, H, W) output tensor.
        """
        assert x.shape[-2:] == (28, 28), "Expect input shape to be (28, 28)."

        N = x.size(0)

        t = t.view(N, 1)  # (N, 1)
        t1 = self.fc1(t).view(N, -1, 1, 1)  # (N, 4D, 1, 1)
        t2 = self.fc2(t).view(N, -1, 1, 1)  # (N, 2D, 1, 1)
        skip1 = self.down1(x)  # (N,  D, 14, 14)
        skip2 = self.down2(skip1)  # (N, 2D, 7, 7)

        h = self.flatten(skip2)  # (N,2D,1,1)
        h = self.unflatten_base(h) + t1  # (N,2D,7,7)

        h = torch.cat([h, skip2], dim=1)  # (N,4D,7,7)
        h = self.up1(h) + t2  # (N,2D,14,14)

        h = torch.cat([h, skip1], dim=1)  # (N,3D,14,14)
        h = self.up2(h)  # (N,D,28,28)

        return self.outconv(h)  # (N,C,28,28)
```

## Implementing DDPM Forward and Inverse Process for Time-conditioned Denoising

```python
def ddpm_schedule(beta1: float, beta2: float, num_ts: int) -> dict:
    """Constants for DDPM training and sampling.

    Arguments:
        beta1: float, starting beta value.
        beta2: float, ending beta value.
        num_ts: int, number of timesteps.

    Returns:
        dict with keys:
            betas: linear schedule of betas from beta1 to beta2.
            alphas: 1 - betas.
            alpha_bars: cumulative product of alphas.
    """
    assert beta1 < beta2 < 1.0, "Expect beta1 < beta2 < 1.0."

    betas = torch.linspace(beta1, beta2, num_ts)
    alphas = 1.0 - betas
    alpha_bars = torch.cumprod(alphas, dim=0)

    return {
        "betas": betas,
        "alphas": alphas,
        "alpha_bars": alpha_bars,
    }
```

```python
def ddpm_forward(
    unet: TimeConditionalUNet,
    ddpm_schedule: dict,
    x_0: torch.Tensor,
    num_ts: int,
) -> torch.Tensor:
    """Algorithm 1 of the DDPM paper.

    Args:
        unet: TimeConditionalUNet
        ddpm_schedule: dict
        x_0: (N, C, H, W) input tensor.
        num_ts: int, number of timesteps.
    Returns:
        (,) diffusion loss.
    """
    unet.train()

    N, C, H, W = x_0.shape
    t_int = torch.randint(0, num_ts, (N,), device=x_0.device, dtype=torch.long)

    eps = torch.randn_like(x_0)
    sqrt_a_bar = torch.sqrt(ddpm_schedule['alpha_bars'][t_int]).reshape(N, 1, 1, 1)
    sqrt_1minus_a = torch.sqrt(1.0 - ddpm_schedule['alpha_bars'][t_int]).reshape(N, 1, 1, 1)
    x_t = sqrt_a_bar * x_0 + sqrt_1minus_a * eps

    t_norm_unet = t_int.float() / num_ts
    predicted_eps = unet(x_t, t_norm_unet)

    loss = F.mse_loss(predicted_eps, eps)
    return loss
```

```python
@torch.inference_mode()
def ddpm_sample(
    unet: TimeConditionalUNet,
    ddpm_schedule: dict,
    img_wh: tuple[int, int],
    num_ts: int,
    seed: int = 0,
) -> torch.Tensor:
    """Algorithm 2 of the DDPM paper with classifier-free guidance.

    Args:
        unet: TimeConditionalUNet
        ddpm_schedule: dict
        img_wh: (H, W) output image width and height.
        num_ts: int, number of timesteps.
        seed: int, random seed.

    Returns:
        (N, C, H, W) final sample.
    """
    unet.eval()

    torch.manual_seed(seed)
    device = ddpm_schedule['betas'].device
    batch_size = 16

    alphas = ddpm_schedule['alphas'].to(device)
    alpha_bars = ddpm_schedule['alpha_bars'].to(device)
    betas = ddpm_schedule['betas'].to(device)

    x_t = torch.randn(batch_size, 1, img_wh[0], img_wh[1], device=device)

    for t in range(num_ts - 1, -1, -1):
        alpha_t = alphas[t]
        alpha_bar_t = alpha_bars[t]
        beta_t = betas[t]

        t_norm_unet = torch.full((batch_size,), t / num_ts, device=device, dtype=torch.float32)
        eps_pred = unet(x_t, t_norm_unet)
        coeff_eps_pred = (1.0 - alpha_t) / torch.sqrt(1.0 - alpha_bar_t)
        x_prev_mean = (1.0 / torch.sqrt(alpha_t)) * (x_t - coeff_eps_pred * eps_pred)

        if t > 0:
            noise = torch.randn_like(x_t)
            sigma_t = torch.sqrt(beta_t)
            x_t = x_prev_mean + sigma_t * noise
        else:
            x_t = x_prev_mean

    return torch.clamp(x_t, 0.0, 1.0)
```

```python
class DDPM(nn.Module):
    def __init__(
        self,
        unet: TimeConditionalUNet,
        betas: tuple[float, float] = (1e-4, 0.02),
        num_ts: int = 300,
        p_uncond: float = 0.1,
    ):
        super().__init__()
        self.unet = unet
        self.num_ts = num_ts
        self.p_uncond = p_uncond

        sched = ddpm_schedule(betas[0], betas[1], num_ts)
        for name, tensor in sched.items():
            self.register_buffer(name, tensor)

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        """
        Args:
            x: (N, C, H, W) input tensor.

        Returns:
            (,) diffusion loss.
        """
        sch = {
            'betas':      self.betas,
            'alphas':     self.alphas,
            'alpha_bars': self.alpha_bars,
        }
        return ddpm_forward(self.unet, sch, x, self.num_ts)

    @torch.inference_mode()
    def sample(
        self,
        img_wh: tuple[int, int],
        seed: int = 0,
    ):
        sch = {
            'betas':      self.betas,
            'alphas':     self.alphas,
            'alpha_bars': self.alpha_bars,
        }
        return ddpm_sample(self.unet, sch, img_wh, self.num_ts, seed)
```

## Training the Time-conditioned UNet

- Plot the loss curve
- Sample results on the test set

```python
from torchvision.transforms import Compose, ToTensor, Lambda
#mnist_norm = Compose([ToTensor(), Lambda(lambda x: x * 2.0 - 1.0), ])

dataset = MNIST(root='data', download=True, transform=ToTensor(), train=True)
dataloader = DataLoader(dataset, batch_size=128, shuffle=True)

device = torch.device('cuda')
eps = 20
lr = 1e-3
hidden_dim = 64
num_timesteps = 300
print_1batch = 100
sample_1ep = 5

t_unet = TimeConditionalUNet(in_channels=1, num_classes=0, num_hiddens=hidden_dim).to(device)
ddpm = DDPM(t_unet, betas=(1e-4, 0.02), num_ts=num_timesteps).to(device)

optimizer = optim.Adam(ddpm.parameters(), lr=lr)
scheduler = torch.optim.lr_scheduler.ExponentialLR(optimizer, gamma=0.99)

loss_curve = []

for ep in range(eps):
    ddpm.train()
    running = 0.0

    for i, (imgs, j) in enumerate(dataloader):
        imgs = imgs.to(device)

        loss = ddpm(imgs)
        optimizer.zero_grad()
        loss.backward()
        torch.nn.utils.clip_grad_norm_(ddpm.parameters(), max_norm=1.0)
        optimizer.step()

        running += loss.item() * imgs.size(0)
        if (i+1) % print_1batch == 0:
            print(f"epoch {ep+1}/{eps}, batch {i+1}/{len(dataloader)}, loss {loss.item():.4f}")

    scheduler.step()
    epoch_loss = running / len(dataloader.dataset)
    loss_curve.append(epoch_loss)
    print(f"epoch {ep+1}: {epoch_loss:.4f}")

    if ((ep+1) % sample_1ep == 0 or ep == 0):
        ddpm.eval()
        with torch.no_grad():
            samples = ddpm.sample((28, 28), seed=ep)[:10]
        visualize_images_with_titles(samples.cpu(), [f"sample {i+1}" for i in range(samples.size(0))])

plt.figure(figsize=(6,3))
plt.plot(range(1, eps+1), loss_curve, marker='o')
plt.xlabel('epoch'); plt.ylabel('MSE'); plt.title('time-conditioned  UNet loss')
plt.grid(True); plt.show()
```

### Implementing class-conditioned UNet

```python
class ClassConditionalUNet(nn.Module):
    def __init__(
        self,
        in_channels: int,
        num_classes: int,
        num_hiddens: int,
    ):
        super().__init__()
        self.down1 = DownBlock(in_channels, num_hiddens)
        self.down2 = DownBlock(num_hiddens, num_hiddens * 2)
        self.flatten = Flatten()
        self.unflatten_base = Unflatten(num_hiddens * 2)
        self.conv_bottleneck = nn.Conv2d(num_hiddens * 2, num_hiddens * 4, kernel_size=1)

        self.fc1_t = FCBlock(1, num_hiddens * 4)  # for unflatten
        self.fc2_t = FCBlock(1, num_hiddens * 2)  # for up1
        self.fc1_c = FCBlock(num_classes, num_hiddens * 4)
        self.fc2_c = FCBlock(num_classes, num_hiddens * 2)
        self.num_classes = num_classes

        self.up1_base = UpBlock(num_hiddens * 4 + num_hiddens * 2, num_hiddens * 2)
        self.up2 = UpBlock(num_hiddens * 2 + num_hiddens, num_hiddens)
        self.outconv = nn.Conv2d(num_hiddens, in_channels, 1)

    def forward(
        self,
        x: torch.Tensor,
        c: torch.Tensor,
        t: torch.Tensor,
        mask: torch.Tensor | None = None,
    ) -> torch.Tensor:
        """
        Args:
            x: (N, C, H, W) input tensor.
            c: (N,) int64 condition tensor.
            t: (N,) normalized time tensor.
            mask: (N,) mask tensor. If not None, mask out condition when mask == 0.

        Returns:
            (N, C, H, W) output tensor.
        """
        assert x.shape[-2:] == (28, 28), "Expect input shape to be (28, 28)."

        n = x.size(0)
        c_1hot = F.one_hot(c, self.num_classes).float().to(x.device)
        if (mask is not None):
            c_1hot = c_1hot * mask.unsqueeze(1)

        t = t.view(n, 1)
        t1 = self.fc1_t(t).view(n, -1, 1, 1)
        t2 = self.fc2_t(t).view(n, -1, 1, 1)
        c1 = self.fc1_c(c_1hot).view(n, -1, 1, 1)
        c2 = self.fc2_c(c_1hot).view(n, -1, 1, 1)
        skip1 = self.down1(x)
        skip2 = self.down2(skip1)

        h = self.flatten(skip2)
        h = self.unflatten_base(h)
        h = self.conv_bottleneck(h)
        h = c1 * h + t1

        h = torch.cat([h, skip2], dim=1)
        h = self.up1_base(h)
        h = c2 * h + t2

        h = torch.cat([h, skip1], dim=1)
        h = self.up2(h)

        return self.outconv(h)
```

```python
def ddpm_forward(
    unet: ClassConditionalUNet,
    ddpm_schedule: dict,
    x_0: torch.Tensor,
    c: torch.Tensor,
    p_uncond: float,
    num_ts: int,
) -> torch.Tensor:
    """Algorithm 1 of the DDPM paper.

    Args:
        unet: ClassConditionalUNet
        ddpm_schedule: dict
        x_0: (N, C, H, W) input tensor.
        c: (N,) int64 condition tensor.
        p_uncond: float, probability of unconditioning the condition.
        num_ts: int, number of timesteps.

    Returns:
        (,) diffusion loss.
    """
    unet.train()

    n = x_0.size(0)
    t_int = torch.randint(0, num_ts, (n,), device=x_0.device)
    eps = torch.randn_like(x_0)

    sqrt_ab = torch.sqrt(ddpm_schedule['alpha_bars'][t_int]).view(n, 1, 1, 1)
    sqrt_1minus_ab = torch.sqrt(1.0 - ddpm_schedule['alpha_bars'][t_int]).view(n, 1, 1, 1)
    x_t = sqrt_ab * x_0 + sqrt_1minus_ab * eps

    t_norm = t_int.float() / num_ts
    cond_mask = (torch.rand(n, device=x_0.device) > p_uncond).float()
    eps_pred = unet(x_t, c, t_norm, mask=cond_mask)

    return F.mse_loss(eps_pred, eps)
```

```python
@torch.inference_mode()
def ddpm_sample(
    unet: ClassConditionalUNet,
    ddpm_schedule: dict,
    c: torch.Tensor,
    img_wh: tuple[int, int],
    num_ts: int,
    guidance_scale: float = 5.0,
    seed: int = 0,
) -> torch.Tensor:
    """Algorithm 2 of the DDPM paper with classifier-free guidance.

    Args:
        unet: ClassConditionalUNet
        ddpm_schedule: dict
        c: (N,) int64 condition tensor. Only for class-conditional
        img_wh: (H, W) output image width and height.
        num_ts: int, number of timesteps.
        guidance_scale: float, CFG scale.
        seed: int, random seed.

    Returns:
        (N, C, H, W) final sample.
        (N, T_animation, C, H, W) caches.
    """
    unet.eval()

    torch.manual_seed(seed)
    n, (H, W) = c.size(0), img_wh
    device = ddpm_schedule['betas'].device

    x_t = torch.randn(n, 1, H, W, device=device)

    for t in range(num_ts - 1, -1, -1):
        alpha_t = ddpm_schedule['alphas'][t]
        alpha_bar_t = ddpm_schedule['alpha_bars'][t]
        beta_t = ddpm_schedule['betas'][t]

        t_norm = torch.full((n,), t / num_ts, device=device)

        eps_cond = unet(x_t, c, t_norm, mask=torch.ones(n, device=device))
        eps_uncond = unet(x_t, c, t_norm, mask=torch.zeros(n, device=device))
        eps_hat= eps_uncond + guidance_scale * (eps_cond - eps_uncond)

        coef = (1.0 - alpha_t) / torch.sqrt(1.0 - alpha_bar_t)
        x_prev = (x_t - coef * eps_hat) / torch.sqrt(alpha_t)

        if t > 0:
            x_t = x_prev + torch.sqrt(beta_t) * torch.randn_like(x_t)
        else:
            x_t = x_prev

    return torch.clamp(x_t, 0.0, 1.0)
```

```python
class DDPM(nn.Module):
    def __init__(
        self,
        unet: ClassConditionalUNet,
        betas: tuple[float, float] = (1e-4, 0.02),
        num_ts: int = 300,
        p_uncond: float = 0.1,
    ):
        super().__init__()
        self.unet = unet
        self.betas_in = betas
        self.num_ts = num_ts
        self.p_uncond = p_uncond

        sched = ddpm_schedule(betas[0], betas[1], num_ts)
        for name, tensor in sched.items():
            self.register_buffer(name, tensor)

    def forward(self, x: torch.Tensor, c: torch.Tensor) -> torch.Tensor:
        """
        Args:
            x: (N, C, H, W) input tensor.
            c: (N,) int64 condition tensor.

        Returns:
            (,) diffusion loss.
        """
        sch = {
            'betas': self.betas_in,
            'alphas': self.alphas,
            'alpha_bars': self.alpha_bars,
        }
        return ddpm_forward(
            self.unet, sch, x, c, self.p_uncond, self.num_ts
        )

    @torch.inference_mode()
    def sample(
        self,
        c: torch.Tensor,
        img_wh: tuple[int, int],
        guidance_scale: float = 5.0,
        seed: int = 0,
    ):
        sch = {
            'betas': self.betas,
            'alphas': self.alphas,
            'alpha_bars': self.alpha_bars,
        }
        return ddpm_sample(
            self.unet, sch, c, img_wh, self.num_ts, guidance_scale, seed
        )
```

## Training the Class-conditioned UNet

- Plot the loss curve
- Sample results on the test set

```python
dataset = MNIST(root='data', download=True, transform=ToTensor(), train=True)
dataloader = DataLoader(dataset, batch_size=128, shuffle=True)

device = torch.device('cuda')
eps = 20
lr = 1e-4
hidden_dim = 64
num_timesteps = 300
p_uncond = 0.1
guidance_scale = 5.0
print_1batch = 100
sample_1ep = 5

c_unet = ClassConditionalUNet(in_channels=1, num_classes=10, num_hiddens=hidden_dim).to(device)
ddpm_c = DDPM(c_unet, betas=(1e-4, 0.02), num_ts=num_timesteps, p_uncond=p_uncond).to(device)
optimizer = optim.Adam(ddpm_c.parameters(), lr=lr)
scheduler = torch.optim.lr_scheduler.ExponentialLR(optimizer, gamma=0.99)

loss_curve = []

for ep in range(eps):
    ddpm_c.train()
    running = 0.0

    for i, (imgs, labels) in enumerate(dataloader):
        imgs, labels = imgs.to(device), labels.to(device)

        loss = ddpm_c(imgs, labels)
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()

        running += loss.item() * imgs.size(0)
        if (i + 1) % print_1batch == 0:
            print(f"epoch {ep+1}/{eps}, batch {i+1}/{len(dataloader)}, loss {loss.item():.4f}")

    scheduler.step()
    epoch_loss = running / len(dataloader.dataset)
    loss_curve.append(epoch_loss)
    print(f"epoch {ep+1} finished, mean loss {epoch_loss:.4f}")

    if (ep+1 == 5 or ep+1 == 20):
      ddpm_c.eval()
      with torch.no_grad():
          sample_labels = torch.arange(10, device=device).repeat(4)  # shape (40,)
          samples = ddpm_c.sample(
              sample_labels,
              img_wh=(28, 28),
              guidance_scale=guidance_scale,
              seed=ep
          )

      print(f"\nSamples at epoch {ep+1}:")
      visualize_images_with_titles(
          samples.cpu(),
          [str(i) for i in range(10)]
      )

plt.figure(figsize=(6,3))
plt.plot(range(1, eps+1), loss_curve, marker='o')
plt.xlabel('epoch'); plt.ylabel('MSE'); plt.title('class-conditioned UNet loss')
plt.grid(True); plt.show()
```
