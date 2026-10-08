Name(s): Yanjin Li

NetID(s): yanjin2

**Part 1: Single-Step Denoising UNet**

1.  A visualization of the noising process using 𝜎 = \[0.0, 0.2, 0.4, 0.5, 0.6, 0.8, 1.0\] (Figure 3)

> <img src="MP5-Report_files/media/image1.png" style="width:6.5in;height:4.00139in" alt="墙上挂着一幅画 AI 生成的内容可能不正确。" />

2.  A training loss curve plot every few iterations during the whole training process (Figure 4)

> <img src="MP5-Report_files/media/image2.png" style="width:5.76042in;height:3.29167in" alt="图表, 折线图 AI 生成的内容可能不正确。" />

3.  Sample results on the test set after the first and the 5th epoch. (Figures 5 and 6)

> 1<sup>st</sup>:
>
> <img src="MP5-Report_files/media/image3.png" style="width:5.36364in;height:1.98386in" alt="手机屏幕截图 AI 生成的内容可能不正确。" />
>
> 5<sup>th</sup>:
>
> <img src="MP5-Report_files/media/image4.png" style="width:5.36319in;height:1.98369in" alt="手机屏幕截图 AI 生成的内容可能不正确。" />

4.  Sample results on the test set with out-of-distribution noise levels after the model is trained. Keep the same image and vary 𝜎 = \[0.0, 0.2, 0.4, 0.5, 0.6, 0.8, 1.0\] (Figure 7)

> 𝜎 = 0.0
>
> <img src="MP5-Report_files/media/image5.png" style="width:6.5in;height:2.40417in" alt="图片包含 文本 AI 生成的内容可能不正确。" />
>
> 𝜎 = 0.2
>
> <img src="MP5-Report_files/media/image6.png" style="width:6.5in;height:2.40417in" alt="图片包含 文本 AI 生成的内容可能不正确。" />
>
> 𝜎 = 0.4
>
> <img src="MP5-Report_files/media/image7.png" style="width:6.5in;height:2.40417in" alt="手机屏幕截图 AI 生成的内容可能不正确。" />
>
> 𝜎 = 0.5
>
> <img src="MP5-Report_files/media/image8.png" style="width:6.5in;height:2.40417in" alt="手机屏幕截图 AI 生成的内容可能不正确。" />
>
> 𝜎 = 0.6
>
> <img src="MP5-Report_files/media/image9.png" style="width:6.5in;height:2.40417in" alt="手机屏幕截图 AI 生成的内容可能不正确。" />
>
> 𝜎 = 0.8
>
> <img src="MP5-Report_files/media/image10.png" style="width:6.5in;height:2.40417in" alt="手机屏幕截图 AI 生成的内容可能不正确。" />
>
> 𝜎 = 1.0
>
> <img src="MP5-Report_files/media/image11.png" style="width:6.5in;height:2.40417in" alt="手机屏幕截图 AI 生成的内容可能不正确。" />

**Part 2.1-2.3: Time-conditioned UNet**

1.  A training loss curve plot for the time-conditioned UNet over the whole training process (Figure 10).

<img src="MP5-Report_files/media/image12.png" style="width:5.67708in;height:3.29167in" alt="图表, 折线图 AI 生成的内容可能不正确。" />

2.  Sampling results for the class-conditioned UNet after the 5th and the 20th epoch. Providing a GIF is optional and can be done as extra credit.

> 5<sup>th</sup>:
>
> <img src="MP5-Report_files/media/image13.png" style="width:6.5in;height:0.625in" />
>
> 20<sup>th</sup>:
>
> <img src="MP5-Report_files/media/image14.png" style="width:6.5in;height:0.625in" />

**Part 2.4-2.5: Class-conditioned UNet**

1.  A training loss curve plot for the class-conditioned UNet over the whole training process.

> <img src="MP5-Report_files/media/image15.png" style="width:5.76042in;height:3.29167in" alt="图表, 折线图 AI 生成的内容可能不正确。" />

2.  Sampling results for the class-conditioned UNet after the 5th and the 20th epoch. Generate 4 instances of each digit as shown on the assignment page. Providing a GIF is optional and can be done as extra credit.

> 5<sup>th</sup>:
>
> <img src="MP5-Report_files/media/image16.png" style="width:6.5in;height:2.57917in" alt="文本 AI 生成的内容可能不正确。" />
>
> 20<sup>th</sup>:
>
> <img src="MP5-Report_files/media/image17.png" style="width:6.5in;height:2.57917in" alt="图片包含 日历 AI 生成的内容可能不正确。" />

**Extra credit:**
