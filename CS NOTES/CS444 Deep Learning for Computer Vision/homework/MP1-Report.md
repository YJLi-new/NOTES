**Name(s): Yanjin Li**

**NetID(s): yanjin2**

**Team name on Kaggle leaderboard:** **Yanjin Li**

<span class="mark">**For each of the sections below, your reported test accuracy should approximately match the accuracy reported on Kaggle**.</span>

<span class="mark"></span>

**<span class="mark">Perceptron</span>**

*For MNIST: lr is searched uniformly in \[0.5, 0.8\]; epochs is searched one by one between 5 and 20; decay_rate is searched uniformly in \[1e-4, 1e-1\].*

*For RICE: lr test range: \[0.1, 0.3, 0.5, 0.7, 1.0\], grid search; epoch test range: \[5, 10, 15, 20\], grid search*

*After testing different learning rate decay values, it is found that the accuracy is lower than the current one when the decay rate is 0.0, which proves that learning rate decay is slightly useful.*

<span class="mark">RICE DATASET</span>

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr>
<td><p><mark>Optimal hyperparameters:</mark></p></td>
<td><p>lr = 0.5</p>
<p>n_epochs = 10</p>
<p>decay_rate = 0.0007098068775694665</p></td>
</tr>
<tr>
<td><p><mark>Training accuracy:</mark></p></td>
<td><p>99.468426</p></td>
</tr>
<tr>
<td><p><mark>Validation accuracy:</mark></p></td>
<td><p>99.477591</p></td>
</tr>
<tr>
<td><p><mark>Test accuracy:</mark></p></td>
<td><p>99.422601</p></td>
</tr>
</tbody>
</table>

<span class="mark">Fashion-MNIST DATASET</span>

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr>
<td><p><mark>Optimal hyperparameters:</mark></p></td>
<td><p>lr = 0.5883478742594644</p>
<p>n_epochs = 11</p>
<p>decay_rate = 0.0007098068775694665</p></td>
</tr>
<tr>
<td><p><mark>Training accuracy:</mark></p></td>
<td><p>83.286000</p></td>
</tr>
<tr>
<td><p><mark>Validation accuracy:</mark></p></td>
<td><p>81.090000</p></td>
</tr>
<tr>
<td><p><mark>Test accuracy:</mark></p></td>
<td><p>80.380000</p></td>
</tr>
</tbody>
</table>

**<span class="mark">SVM</span>**

*For MNIST: lr is searched uniformly in \[1e-3,1.0\]; epochs is searched one by one between 5 and 20; reg_const is searched uniformly in \[1e-7,5.0\]; batch_size is selected one by one between 64, 128, 256; decay_rate is searched uniformly in \[1e-3,1e-1\]*

*For RICE: lr is searched uniformly in \[1e-3,1.0\]; epochs is searched one by one between 5 and 200; reg_const is searched uniformly in \[1e-4,10.0\]*

*When mini-batch is not used and decay_rate is 0, the accuracy decreases. Therefore, these two methods are useful.*

<span class="mark">RICE DATASET</span>

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr>
<td><p><mark>Optimal hyperparameters:</mark></p></td>
<td><p>lr = 0.057068171943404314</p>
<p>n_epochs = 172</p>
<p>reg_const = 0.0003122299278112629</p>
<p>batch_size = 128</p>
<p>decay_rate = 0.002091426077065719</p></td>
</tr>
<tr>
<td><p><mark>Training accuracy:</mark></p></td>
<td><p>99.926679</p></td>
</tr>
<tr>
<td><p><mark>Validation accuracy:</mark></p></td>
<td><p>99.862524</p></td>
</tr>
<tr>
<td><p><mark>Test accuracy:</mark></p></td>
<td><p>99.890019</p></td>
</tr>
</tbody>
</table>

<span class="mark">Fashion-MNIST DATASET</span>

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr>
<td><p><mark>Optimal hyperparameters:</mark></p></td>
<td><p>lr = 0.035983073221155626</p>
<p>n_epochs = 11</p>
<p>reg_const = 3.362309019476433e-05</p>
<p>batch_size = 128</p>
<p>decay_rate = 0.002091426077065719</p></td>
</tr>
<tr>
<td><p><mark>Training accuracy:</mark></p></td>
<td><p>83.472000</p></td>
</tr>
<tr>
<td><p><mark>Validation accuracy:</mark></p></td>
<td><p>82.290000</p></td>
</tr>
<tr>
<td><p><mark>Test accuracy:</mark></p></td>
<td><p>80.630000</p></td>
</tr>
</tbody>
</table>

**<span class="mark">Softmax</span>**

*For MNIST: lr is searched uniformly in \[1e-3,1.0\]; epochs is searched one by one between 5 and 20; reg_const is searched uniformly in \[1e-4,0.5\]; batch_size is selected one by one between 128, 256, 512; decay_rate is searched uniformly in \[1e-5,1e-1\]; temperature is searched uniformly in \[0.5,2.0\]*

*For RICE: lr test range: \[0.1, 0.3, 0.5, 0.7, 1.0\], grid search; epoch test range: \[5, 10, 15, 20\], grid search; reg_const test range: \[0.01, 0.03, 0.05, 0.1, 0.15\] , Grid Search*

*When mini-batch, temperature, and decay_rate are not used, the accuracy decreases. Therefore, these three methods are useful.*

<span class="mark">RICE DATASET</span>

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr>
<td><p><mark>Optimal hyperparameters:</mark></p></td>
<td><p>lr = 0.5</p>
<p>n_epochs = 10</p>
<p>reg_const = 0.05</p></td>
</tr>
<tr>
<td><p><mark>Training accuracy:</mark></p></td>
<td><p>98.625241</p></td>
</tr>
<tr>
<td><p><mark>Validation accuracy:</mark></p></td>
<td><p>98.680231</p></td>
</tr>
<tr>
<td><p><mark>Test accuracy:</mark></p></td>
<td><p>98.542755</p></td>
</tr>
</tbody>
</table>

<span class="mark">Fashion-MNIST DATASET</span>

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr>
<td><p><mark>Optimal hyperparameters:</mark></p></td>
<td><p>lr = 0.010872296945794026</p>
<p>n_epochs = 12</p>
<p>reg_const = 0.003457774444224259<br />
batch_size = 256</p>
<p>decay_rate = 0.012949184782211806</p>
<p>temperature = 0.7008162465836012</p></td>
</tr>
<tr>
<td><p><mark>Training accuracy:</mark></p></td>
<td><p>84.794000</p></td>
</tr>
<tr>
<td><p><mark>Validation accuracy:</mark></p></td>
<td><p>82.740000</p></td>
</tr>
<tr>
<td><p><mark>Test accuracy:</mark></p></td>
<td><p>81.920000</p></td>
</tr>
</tbody>
</table>

**<span class="mark">Logistic</span>**

*lr test range: \[0.1, 0.3, 0.5, 0.7, 1.0\], grid search; epoch test range: \[5, 10, 15, 20\], grid search; threshold test range: \[0.3, 0.4, 0.5, 0.6, 0.7\], grid search*

<span class="mark">RICE DATASET</span>

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr>
<td><p><mark>Optimal hyperparameters:</mark></p></td>
<td><p>learning_rate = 0.3</p>
<p>n_epochs = 15</p>
<p>threshold = 0.4</p></td>
</tr>
<tr>
<td><p><mark>Training accuracy:</mark></p></td>
<td><p>97.772890</p></td>
</tr>
<tr>
<td><p><mark>Validation accuracy:</mark></p></td>
<td><p>98.020346</p></td>
</tr>
<tr>
<td><p><mark>Test accuracy:</mark></p></td>
<td><p>97.745395</p></td>
</tr>
</tbody>
</table>
