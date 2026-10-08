Time Complexity

|                | Worst             | Average   | Best      |
|----------------|-------------------|-----------|-----------|
| Insertion Sort | O(n^2)            | O(n^2)    | O(n)      |
| Merge Sort     | O(n logn)         | O(n logn) | O(n logn) |
| Quick Sort     | O(n^2) (unlikely) | O(n logn) | O(n logn) |
| Shell Sort     |                   |           |           |
| Counting Sort  |                   |           |           |
| Bucket Sort    |                   |           |           |
| Radix Sort     |                   |           |           |

<img src="Midterm-Review_files/media/image1.png" style="width:4.58395in;height:3.27488in" />

L3 arrays

Pro & Con of arrays:

Pro: If you know the index (where), you can find the object (content) with one basic operation (efficient); Efficient search if array is sorted

Con: The capacity is fixed, so if you insert an object in a place between two objects in an array, you have to move the objects first

Consecutive locations: 数组是一种线性数据结构，它托管存储在计算机内存中连续位置(Consecutive locations)的数据元素的集合

ADT: Abstract Data Type

**Array ADT**:

**createArray(n)**

> <img src="Midterm-Review_files/media/image2.png" style="width:2.50523in;height:0.7448in" alt="文本 描述已自动生成" />
>
> Initialize an array of size n

**retrieve(arr, i)**

> arr\[i\]
>
> Return the item stored in the i-th position of the array

**store(arr, i, itemToStore)**

> Store itemToStore to the i-th position of the array
>
> arr\[i\] = itemToStore

<img src="Midterm-Review_files/media/image3.png" style="width:3.30211in;height:1.04167in" alt="图形用户界面, 文本 描述已自动生成" />

<img src="Midterm-Review_files/media/image4.png" style="width:3.9844in;height:2.80731in" />

<img src="Midterm-Review_files/media/image5.png" style="width:3.94795in;height:2.38543in" />

<img src="Midterm-Review_files/media/image6.png" style="width:4.14066in;height:1.16668in" alt="图形用户界面, 文本, 应用程序 描述已自动生成" />

L4 insertion sort and merge sort

(Use array to solve the sorting problem)

Insertion sort

> <img src="Midterm-Review_files/media/image7.png" style="width:3.58442in;height:1.18719in" /><img src="Midterm-Review_files/media/image8.png" style="width:4.25003in;height:1.78126in" />

<img src="Midterm-Review_files/media/image9.png" style="width:4.75003in;height:2.99481in" />

<u>the **correctness** of the algorithm</u>

> **Initialization**: true before the begin of loop
>
> **Maintenance**: if true before an iteration, then also true after it
>
> **Termination**: when the loop stops, use the invariant to show the algorithm is correct

<img src="Midterm-Review_files/media/image10.png" style="width:3.99482in;height:2.85419in" />

<img src="Midterm-Review_files/media/image11.png" style="width:3.2969in;height:2.02606in" alt="文本 描述已自动生成" />

<img src="Midterm-Review_files/media/image12.png" style="width:4.02086in;height:1.91147in" alt="文本, 信件 描述已自动生成" />

<img src="Midterm-Review_files/media/image13.png" style="width:4.03649in;height:1.73439in" />

<img src="Midterm-Review_files/media/image14.png" style="width:4.26045in;height:2.44273in" />

Merge sort

**based on <u>divide-and-conquer</u>**

<img src="Midterm-Review_files/media/image15.png" style="width:3.12205in;height:0.86364in" />

<img src="Midterm-Review_files/media/image16.png" style="width:3.94795in;height:2.28127in" />

<img src="Midterm-Review_files/media/image17.png" style="width:3.96357in;height:2.58335in" />

<img src="Midterm-Review_files/media/image18.png" style="width:3.57815in;height:2.43231in" />

<img src="Midterm-Review_files/media/image19.png" style="width:4.14066in;height:2.50523in" alt="文本 描述已自动生成" />

<img src="Midterm-Review_files/media/image20.png" style="width:3.62621in;height:3.49289in" alt="文本 描述已自动生成" /><img src="Midterm-Review_files/media/image21.png" style="width:0.77641in;height:3.51375in" alt="形状 中度可信度描述已自动生成" />

<img src="Midterm-Review_files/media/image22.png" style="width:2.63544in;height:2.38023in" alt="文本 描述已自动生成" />

（？？C log(N) ??）

<img src="Midterm-Review_files/media/image23.png" style="width:3.82295in;height:1.59897in" alt="瓶子上写着字 低可信度描述已自动生成" />

<img src="Midterm-Review_files/media/image24.png" style="width:3.6872in;height:1.90762in" />

**L5 asymptotic notations**

<img src="Midterm-Review_files/media/image25.png" style="width:2.50711in;height:0.23668in" />

<img src="Midterm-Review_files/media/image26.png" style="width:3.04739in;height:0.8007in" alt="手机屏幕截图 描述已自动生成" />

<img src="Midterm-Review_files/media/image27.png" style="width:3.44794in;height:0.36979in" />

<img src="Midterm-Review_files/media/image28.png" style="width:3.43602in;height:0.72396in" alt="文本 描述已自动生成" />

<img src="Midterm-Review_files/media/image29.png" style="width:3.82295in;height:0.96355in" />

两个意义

<img src="Midterm-Review_files/media/image30.png" style="width:2.04168in;height:0.87501in" alt="瓶子上写着字 低可信度描述已自动生成" />

<img src="Midterm-Review_files/media/image31.png" style="width:3.3351in;height:2.30332in" />

<img src="Midterm-Review_files/media/image32.png" style="width:3.19271in;height:1.14865in" />

<img src="Midterm-Review_files/media/image33.png" style="width:1.66146in;height:0.72132in" alt="手机屏幕截图 描述已自动生成" />

<img src="Midterm-Review_files/media/image34.png" style="width:2.35938in;height:0.81003in" alt="文本 描述已自动生成" /> <span class="mark"></span><img src="Midterm-Review_files/media/image35.png" style="width:3.01042in;height:0.26191in" />

> <img src="Midterm-Review_files/media/image36.png" style="width:2.23958in;height:0.63895in" alt="文本 描述已自动生成" />

<img src="Midterm-Review_files/media/image37.png" style="width:3.27083in;height:1.46587in" alt="文本 描述已自动生成" />

<img src="Midterm-Review_files/media/image38.png" style="width:3.64583in;height:1.51134in" />

<img src="Midterm-Review_files/media/image39.png" style="width:3.35749in;height:1.58333in" alt="图片包含 文本 描述已自动生成" />

(???) <img src="Midterm-Review_files/media/image40.png" style="width:1.99479in;height:0.49263in" alt="文本, 信件 描述已自动生成" />

Big-Omega (notation)

<img src="Midterm-Review_files/media/image41.png" style="width:3.75259in;height:0.86458in" alt="文本 描述已自动生成" />

(c·f(n) grows slower than g(n))

<img src="Midterm-Review_files/media/image42.png" style="width:2.01446in;height:0.33854in" alt="图示 描述已自动生成" />

<img src="Midterm-Review_files/media/image43.png" style="width:3.21875in;height:1.64658in" />

<img src="Midterm-Review_files/media/image44.png" style="width:2.03125in;height:1.59256in" alt="文本 描述已自动生成" />

Big-Theta

<img src="Midterm-Review_files/media/image45.png" style="width:2.22396in;height:0.21539in" />

<img src="Midterm-Review_files/media/image46.png" style="width:2.1875in;height:0.23845in" />

**L11 Quick Sort**

Quick Sort

Deterministic algorithms (previously other sorting algorithms)

Randomized algorithms

<img src="Midterm-Review_files/media/image47.png" style="width:2.65625in;height:0.61442in" />

**L12**

……

<img src="Midterm-Review_files/media/image48.png" style="width:3.57346in;height:2.64348in" alt="图示 描述已自动生成" />

**L14**

<img src="Midterm-Review_files/media/image49.png" style="width:2.53125in;height:0.80022in" />

<img src="Midterm-Review_files/media/image50.png" style="width:2.53125in;height:0.79488in" alt="文本 描述已自动生成" />

<img src="Midterm-Review_files/media/image51.png" style="width:2.91667in;height:0.34577in" />

<img src="Midterm-Review_files/media/image52.png" style="width:3.01042in;height:0.351in" />
