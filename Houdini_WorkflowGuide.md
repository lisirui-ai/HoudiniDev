# VEX

VEX

- 类似C的面向过程语言，不支持对象，只能通过函数，不能对象.方法
- 脚本语言
  - 支持字符串使用`+`进行拼接
- 层级
  - 代码在哪个层级上运行或运行几次

## 数据类型

### 三种形式

C/C++风格变量

- 不会被继承

- 向量

  ```c
  vector2 v1={1,2};                   //二维向量；向量的分量是u、v
  vector v2={5,2,4};                  //三维向量；向量的分量是x、y、z、w
  vector4 v3={1,2,3,4};               //四维向量；向量的分量是x、y、z、w
  ```

- 矩阵

  ```c
  matrix2 m1={1,2,3,4};              //二阶矩阵
  matrix3 m2;                        //三阶矩阵
  matrix m3;                         //四阶矩阵
  ```

- 数组

  ```c
  类型 数组名[];
  ```

- 字符串

  ```c++
  string 字符串名="字符";                 //是C++中的字符串对象；s@和string都是一个类型，可以直接比较；重载了+号，可使用+拼接两个字符串
  ```

`ch`型变量

- 在 VEX 中使用用于读取参数值，若参数不存在则自动在参数面板上创建对应的属性框；在 HScript 中使用用于引用已有参数的值，不会自动创建参数

- 是右值，不是左值

- 向量

  ```c
  chu("v1");                          //二维向量滑块
  chv("v2");                          //三维向量滑块
  chp("v3");                          //四维向量滑块
  ```

- 矩阵

  ```c
  ch2("m1");                         //二阶矩阵滑块
  ch3("m2");                         //三阶矩阵滑块
  ch4("m3");                         //四阶矩阵滑块
  ```

- 字符串

  ```c
  chs("s");						//字符串文本框
  ```

@型变量

- 会被继承

- 向量

  ```c
  u@v1={1,2};                           //二维向量
  v@v2={1,2,3};                         //三维向量
  p@v3={1,2,3,4};                       //四维向量
  ```

- 矩阵

  ```c
  2@m1;                                //二阶矩阵
  3@m2;                                //三阶矩阵
  4@m3;                                //四阶矩阵
  ```

- 数组

  ```c
  // @属性数组只能用简写前缀，不能写完整类型名（如不能写 int[]@arr，只能写 i[]@arr）
  // 所有可用前缀：
  //   f[]@数组名  float    浮点数
  //   i[]@数组名  int      整型
  //   s[]@数组名  string   字符串
  //   u[]@数组名  vector2  二维向量（2 个浮点）
  //   v[]@数组名  vector   三维向量（3 个浮点）
  //   p[]@数组名  vector4  四维向量（4 个浮点）
  //   2[]@数组名  matrix2  2×2 矩阵
  //   3[]@数组名  matrix3  3×3 矩阵
  //   4[]@数组名  matrix   4×4 矩阵
  类型前缀[]@数组名;
  ```
  - 数组遍历

    ```c
    foreach(内置类型 i；@数组名){
    	函数体
    }
    ```

  - 排序

    ```c
    sort(@数组名)；
    ```

- 字符串

  ```c
  s@string;                              //是C++中的字符串对象；s@和string都是一个类型，可以直接比较；重载了+号，可使用+拼接两个字符串
  ```

## 数组

### 初始化

**局部数组**

```c
// 用花括号字面量初始化，编译期确定，不能含变量
int    a[] = {1, 2, 3};
float  b[] = {0.1, 0.2, 0.3};
vector c[] = {{1,0,0}, {0,1,0}, {0,0,1}};

// 用 array() 函数初始化，运行期构建，可以含变量
int x = 10;
// array() 接受任意数量同类型参数，返回对应类型的数组
int d[] = array(x, x+1, x+2);  // d == {10, 11, 12}

// 声明空数组，两种写法等价，VEX 默认初始化为空（不同于 C 语言）
int empty[];
int empty[] = {};
```

**`@` 属性数组**

```c
// 首次赋值时用类型前缀声明，右侧同样支持花括号或 array()
i[]@ids   = {0, 1, 2};
f[]@vals  = array(0.1, 0.2, 0.3);

// 声明空属性数组，两种写法等价
i[]@pts;
i[]@pts = {};
```


### 增删查改

> 以下示例同时给出**局部数组**（`int arr[]`）和 **`@` 属性数组**（`i[]@arr`）两种写法，操作函数完全相同。

**增**：`append(数组, 值)` —— 在数组末尾追加一个元素

```c
// 局部数组写法
int arr[] = {1, 2, 3};
append(arr, 99);           // arr 变为 {1, 2, 3, 99}

// @ 属性数组写法：i[]@ 前缀仅在首次声明时写明类型，之后传参只写 @数组名
i[]@arr = {1, 2, 3};      // 首次赋值，用 i[]@ 声明该属性为整型数组
append(@arr, 99);          // 传参时只写 @arr，@arr 变为 {1, 2, 3, 99}
```

**删**：`pop(数组)`、`removevalue(数组, 值)`、`removeindex(数组, 下标)`

```c
// 局部数组写法
int arr[] = {10, 20, 30, 40};
pop(arr);                  // 移除并返回最后一个元素，arr 变为 {10, 20, 30}
removevalue(arr, 20);      // 按值删除第一个匹配项，arr 变为 {10, 30}
removeindex(arr, 0);       // 按下标删除，arr 变为 {30}

// @ 属性数组写法：i[]@ 仅声明时用，传参只写 @arr
i[]@arr = {10, 20, 30, 40};
pop(@arr);                 // @arr 变为 {10, 20, 30}
removevalue(@arr, 20);     // @arr 变为 {10, 30}
removeindex(@arr, 0);      // @arr 变为 {30}
```

**查**：`[]`（按下标取值）、`find(数组, 值)`、`len`、`min`、`max`

```c
// 局部数组写法
int arr[] = {5, 3, 8, 1};
int val = arr[2];           // 按下标取值，val = 8
int idx = find(arr, 3);     // 查找值 3 的下标，idx = 1；未找到时返回负数（不一定是 -1）
int n   = len(arr);         // 数组长度，n = 4
int lo  = min(arr);         // 最小值，lo = 1
int hi  = max(arr);         // 最大值，hi = 8

// @ 属性数组写法：i[]@ 仅声明时用，传参和取下标只写 @arr
i[]@arr = {5, 3, 8, 1};
int val = @arr[2];          // 按下标取值，val = 8
int idx = find(@arr, 3);    // 查找值 3 的下标，idx = 1
int n   = len(@arr);        // 数组长度，n = 4
int lo  = min(@arr);        // 最小值，lo = 1
int hi  = max(@arr);        // 最大值，hi = 8
```

**改**：`[]`（按下标赋值）

```c
// 局部数组写法
int arr[] = {10, 20, 30};
arr[1] = 99;               // arr 变为 {10, 99, 30}

// @ 属性数组写法：i[]@ 仅声明时用，赋值时只写 @arr
i[]@arr = {10, 20, 30};
@arr[1] = 99;              // @arr 变为 {10, 99, 30}
```

## 选择与循环

**if / else if / else**

```c
// 声明整型变量 x，赋初值 5
int x = 5;

// 第一个分支：x > 10 时执行
if (x > 10) {
    // x 大于 10 时执行
// 第二个分支：第一个条件为假、且 x > 0 时执行
} else if (x > 0) {
    // x 在 0 到 10 之间时执行
// 兜底分支：以上条件均为假时执行
} else {
    // x 小于等于 0 时执行
}
```

**for 循环**（计数循环）

```c
// for (初始化; 循环条件; 每轮结束后的步进)
// i 从 0 开始，每次加 1，直到 i >= 5 时停止，共循环 5 次
for (int i = 0; i < 5; i++) {
    // 每次迭代执行的代码，i 依次为 0 1 2 3 4
}
```

**foreach 循环**（遍历数组）

```c
// 声明整型数组，含 3 个元素
int arr[] = {10, 20, 30};

// 只取值：分号左边声明接收值的变量，右边是被遍历的数组
foreach (int val; arr) {
    // val 依次为 10、20、30
}

// 同时取下标与值：逗号分隔 index 和 value，分号隔开数组
// 类似 Python 的 for idx, val in enumerate(arr)
foreach (int idx, int val; arr) {
    // idx 为当前下标（0、1、2），val 为对应元素值（10、20、30）
}
```

**while 循环**

```c
// 声明计数变量，初值为 0
int n = 0;

// 每次循环前检查条件，条件为假时退出循环
while (n < 5) {
    // 递增 n，防止死循环；循环结束后 n == 5
    n++;
}
```

**break / continue**

```c
// if 后只有单条语句时，{} 可省略（继承自 C 语言规则）
for (int i = 0; i < 10; i++) {
    // i==3 时跳过本次迭代，直接执行 i++，进入下一轮
    if (i == 3) continue;
    // i==7 时立即退出整个循环，后续迭代不再执行
    if (i == 7) break;
}
```

# @型变量

固有的或者可继承的变量

<img src="./image-20250221215557446.png" width="600" />

@调用变量

```c++
@Frame                                     //帧；1秒等于24帧；float类型；精度取决于时间轴最小单位
@Time                                     //秒；float类型
@pscale                                  //点的渲染大小属性；类型是float；可控制克隆体的大小；控制粒子的渲染大小；默认值是0.05；控制点云转换为体积时，单元的大小，影响体素个数；点云生成体积时必须有@pscale控制体积单元的大小；可以在aw节点中定义和控制，也可以在pyrosource节点/pointsfromvolume节点中定义和控制
@N                                       //点的法线属性;三维向量类型；控制法线的朝向;默认法线朝向是世界z轴
@up                                      //点的法线的旋转（朝向不变）；浮点数类型
3@transform                              //点的坐标系属性；类型是三维矩阵；控制点的坐标系；第一个分量固定是x轴，第二个分量固定是y轴，第三个分量固定是z轴；不是固有属性；使用时，需要自行定义；一般用于克隆节点控制克隆体的旋转；分量||向量的长度还控制克隆体在对应方向上的缩放
p@orient                                 //点的坐标系的旋转属性；类型是四维向量；需要搭配坐标系的三维矩阵和rotate函数（旋转坐标系）和quaternion函数使用
v@scale                                  //点的轴向的伸缩；类型是三维向量
@group_组名                               //分组属性；为1在组内，为0不在组内
@width                                   //线的宽度属性；渲染才可见
@uv                                      //uv属性；类型是vector三维向量
@name                                    //体积雾的面层级的属性；类型是字符串
@density                                 //fog体积的浓度属性；类型是float；不影响体素的个数和大小；影响显示效果/渲染效果；fog内部的@density的值都一样，为volumewrangle中设置的@density大小；fog外部的属性值为0;可以为点云赋予@density，相当于pyrosource，方便后续生成体积并解算 
@surface                                 //distance体积的距离属性；类型是float；体积表面的属性为0，内部距离为-，外部距离为＋；越往里，属性越小，当有内界时，属性减小到内界的属性；越往外，属性越大，当有外界时，属性增大到外界的属性
@P                                        //对于点是位置属性，且可写；对于体积是体素的位置属性，只可读；对于粒子是位置属性
s@shop_materialpath                        //面层级的材质属性；类型是字符串""；可写，例如s@shop_materialpath="/mat/materialbuilder"
@id                                       //解算粒子的id属性；每个点的id是固定的；类型是int；粒子死亡后该粒子的id不会被重新分配，id不可回收；@ptnum是不固定而且会重新分配
@life                                     //解算粒子的寿命属性；float类型；与popsource节点中的lifeexpectancy相同；是固定的；可以在dop系统的vex中使用
@age                                      //解算粒子的年龄属性；float类型；是不固定的；最大等于@life；可以在dop系统的vex中使用
@v                                        //解算粒子的速度属性；vector类型；有力就有速度；可以在进入popnetwork前在点层级先初始化该属性；速度越快，运动模糊越明显
@ptnum                                     //点的序号属性；int类型；dop下解算粒子的@ptnum不是固定的，但是可以对满足条件（利用@age等属性）的点使用带有@ptnum的vex函数
@Alpha                                     //点的透明显示属性，非渲染；int类型；为0为全透，为1为不透明
@dead                                       //粒子的生命周期控制属性；int类型；为1时立即结束粒子的生命周期，为0时不结束粒子的生命周期
@nage                                       //粒子的生命周期属性；float类型；等价于@age/@life
@hitnml                                     //粒子的属性，记录当前帧与粒子发生碰撞的刚体上的点的法线；vector类型
@hitnum                                      //粒子的属性，记录当前帧粒子是否与刚体发生碰撞；int类型；为0不发生碰撞，为1发生碰撞
@hittotal                                    //粒子的属性，记录截至当前帧粒子与刚体的碰撞次数总和；int类型
@hitpath                                     //粒子的属性，记录碰撞刚体的路径；string类型
@hitpos                                      //粒子的属性，记录当前帧粒子与刚体碰撞的位置；vector类型
@hitprim                                     //粒子的属性，记录当前帧与粒子发生碰撞的刚体上的位置所在的面的序号；int类型
@hituv                                        //粒子的属性，记录当前帧与粒子发生碰撞的刚体上的位置所在的面的uv属性；vector类型
@hittime                                      //粒子的属性，记录粒子与刚体发生碰撞的时间；float类型
@hitv                                          //粒子的属性，记录粒子与刚体发生碰撞时粒子的速度；vector类型
@temperaure                                    //点的属性；float类型；相当于pyrosource，方便后续生成体积并解算 
@constraint_name                               //面属性；约束的名称属性；string类型；刚体解算中的约束的名称；要与rbdbulletsolver节点advanced选项中对应约束的dataname一致
@active                                        //点属性；int类型；为0时是被动碰撞刚体，为1时是主动碰撞刚体；由于为0时是被动碰撞刚体相当于不会发生变化，固@active可以理解为激活与否，为1时激活，为0不激活；设置在低模上即可
@strength                                      //面属性；Glue约束的强度属性；float类型；和@constraint_name搭配使用，需要初始化设置Glue约束的强度；设置为-1代表约束强度不衰减，即物体不会破碎；一般设置为1000；随着解算的进行，约束强度衰减，约束力也衰减；强度量级在百位到千位
@stiffness                                     //面属性；Soft约束的强度属性；float类型；和@constraint_name搭配使用，需要初始化设置Soft约束的强度；设置为-1代表约束强度不衰减，即物体不会破碎；随着解算的进行，约束强度衰减，约束力也衰减；强度量级在小数点后一位到个位
@w                                             //粒子的属性；vector类型；粒子旋转的速度；依赖于popspin节点/popdragspin节点而存在
@deforming                                     //点属性；int类型；为1时将带动画的刚体参与解算；为0时刚体的动画不参与解算，不影响刚体的运动
@name                                           //面属性；string类型；碎块的名字；解算的关键属性，不能丢失；必须保证每一个碎块的@name属性不重复且每一个碎块的高模和低模的@name属性相同才能正确解算，可以自定义来保证不重复；对于rbd，@name属性记录了每一个碎块以及碎屑的名字
```

# HScript

在节点的属性面板的参数栏中用`HScript`写表达式

$调用固有变量

```c
$F                                     //当前帧号；类型为int；常用于文件名编号，如 file.$F.bgeo
$FF                                    //当前帧号；类型为float；精度更高；$FF/k可起到减缓时间的作用；文件名中补齐4位写法为 $F4（零填充语法，非独立变量）
$T                                     //当前时间；类型为float；单位为秒；等于 ($F-1)/$FPS；精度优于$FF，推荐用于长动画
$FPS                                   //播放帧率；类型为float；由时间轴控件设置
$FSTART                                //动画起始帧号；由时间轴控件设置
$FEND                                  //动画结束帧号；由时间轴控件设置
$RFSTART                               //playbar 中显示范围的起始帧号；playbar可只显示动画的一个子集
$RFEND                                 //playbar 中显示范围的结束帧号
$NFRAMES                               //动画总帧数；等于 $FEND - $FSTART + 1
```

HScript 拥有独立的表达式函数库，与 VEX 函数相互独立，部分函数同名（如 `sin`、`fit`、`rand`）

`ch()` 引用其他参数的值

```c
// ch("参数路径") 读取指定参数的值，自动判断类型，返回 float 或 string
// 路径可以是相对路径或绝对路径
ch("../box1/sizex")            // 引用上级 box1 节点的 sizex 参数值，该参数为 float 则返回 float
ch("tx")                       // 引用本节点自身的 tx 参数值，该参数为 float 则返回 float

// 让当前参数跟随另一个参数联动
$T * ch("speed")               // 乘以本节点名为 speed 的参数值，实现速度控制

// 不同类型的 ch 系列函数
ch("path")                     // 读取 float 或 string 类型参数，自动判断，返回 float 或 string
chf("path")                    // 明确读取 float 类型参数，返回 float
chi("path")                    // 读取 int 类型参数，返回 int
chs("path")                    // 读取 string 类型参数，返回 string
chu("path")                    // 读取 vector2 类型参数，返回 vector2
chv("path")                    // 读取 vector 类型参数，返回 vector
chp("path")                    // 读取 vector4 类型参数，返回 vector4
ch2("path")                    // 读取 matrix2 类型参数，返回 matrix2（2×2）
ch3("path")                    // 读取 matrix3 类型参数，返回 matrix3（3×3）
ch4("path")                    // 读取 matrix 类型参数，返回 matrix（4×4）
chramp("path", pos)            // 采样 ramp 参数，pos 为 0~1 的采样位置，返回 float
```

常用数学函数（与 VEX 同名，直接使用）

```c
sin($T)                        // 正弦，参数单位为弧度，返回 float
cos($T)                        // 余弦，参数单位为弧度，返回 float
fit($FF, 1, 24, 0, 1)         // 将值从一个区间线性映射到另一个区间，返回 float
clamp($FF, 0, 10)              // 将值限制在 [0, 10] 范围内，返回 float
rand($FF)                      // 基于输入值生成伪随机数，返回 0~1 的 float
int($FF)                       // 取整，返回 int
abs(-5)                        // 取绝对值，返回 float
```

# chramp

在`aw`中制作`ramp`图

```c
f@f=chramp("ramp图名称",@属性名或变量名);               //生成ramp图，ramp图将第二个传入参数从小到大映射为0到1的范围后作为横轴，纵轴自行设置大小，输出横轴为传入参数的值时纵轴的大小；可通过属性面板的editparameterinterface将ramptype设置为color，使得ramp输出颜色
```

# 函数

```c
clamp(变量，min，max)；            //传入参数为2/3/4维向量、int、float型；返回范围内的对应类型的变量；不在范围的变量会被映射为min和max；不对范围内的中间值进行缩放
anoise(@P*k,重复次数，粗糙度，1);    //噪波函数；重复次数为int型，增加纹理细节；粗糙度为float型，一般0.5；k为对纹理的缩放；返回值介于0到0.67（可以认为是0.5）；返回类型是向量或者浮点数；如果anoise优先进行乘法，自动返回浮点数,一般乘2返回黑白纹理；返回类型根据左值判断；纹理偏移为对@P进行移动,+set(x,y,z)；alligator噪波
fit(value,omin,omax,nmin,nmax);   //选择分区函数；传入和返回值参数类型为向量或者浮点型；小于omin的值被映射为nmin，大于omax的值被映射为nmax；不在范围的变量会被重新映射；返回值是映射后的浮点数；范围内的中间值会被缩放
lerp(value1,value2,amount);       //线性插值函数；amount靠近0返回值接近value1，靠近1接近value2；传入参数和返回值类型为向量或者浮点型
xyzdist(输入线序号，@P);            //返回主线上对于每个点距离序号对应几何体的最短距离；返回类型是浮点型
rand(@Ptnum+种子);                 //返回随机值；返回类型为float、vector；不是纹理随机，不连续；返回值介于0~1
set(x,y,z);                       //返回向量、矩阵，例如vector和matrix3
normalize(三维向量名);              //单位化三维向量
cross(a,b);                       //返回垂直于ab向量确定平面的向量；传入参数必须是单位向量
rotate(三维矩阵,旋转弧度,旋转轴);  //将三维矩阵/坐标系绕着旋转轴旋转一定角度；旋转弧度是浮点型；旋转轴是三维向量；没有返回值；旋转坐标系
quaternion(三维矩阵变量名);          //返回四维向量;p@orient=quaternion(三维矩阵);
onoise(@P*k,重复次数，粗糙度，1);     //噪波函数；重复次数为int型，增加纹理细节；粗糙度为float型，一般0.5；k为对纹理的缩放；返回值介于-1到1；返回类型是向量或者浮点数；如果anoise优先进行乘法，自动返回浮点数；返回类型根据左值判断；纹理偏移为对@P进行移动,+set(x,y,z)；original perlin噪波
ident();                           //返回xyz轴标准世界坐标系的三维矩阵；例如3@transform=ident()
npoints(输入端的序号);                //返回输入端对应的几何体的所有点的个数；返回类型为整型
attrib(输入端，"属性层级"，"属性名",属性序号);   //属性调用函数；输入端为直接相连的输入端的序号（从0开始）或者sop地址（string类型，需要双引号）；属性层级有point、prim、detail、vertex；属性名即@类型的变量（显示在gs视窗的变量）的名字；属性序号为gs视窗中的序号（从0开始），当调用detail层级的属性时，为0；返回对应类型的变量或者数组；输入端保险使用直连写输入端序号的方式，写地址可能失败
nearpoint(输入端序号，@P，限制搜索范围);        //得到输入端序号对应的模型上和主线模型的点的距离在搜索范围内且距离最近的点的序号；返回值为输入端序号对应的模型上和主线模型的点的距离最近的点的序号，不存在/超过限制搜索范围则返回-1,返回类型是整型数；限制搜索范围为浮点型
xyzdist(输入端序号，@P，已经存在的变量a，已经存在的变量b,限制搜索范围);  //返回主线上对于每个点距离序号对应几何体的最短距离；返回类型是浮点型；变量a和变量b是传入传出参数；变量a是int型，用来接收输入端序号对应的几何体上距离主线几何体的@P距离最近的面的序号；变量b是vector型，用来接收输入端序号对应的几何体上距离主线几何体的@P距离最近的面的uv；限制搜索范围是float型变量，超过搜索范围的距离会被映射为该值
primuv(输入端序号，想要获取的属性名，面序号，面uv);     //根据传入的面序号和面uv返回输入端序号对应的几何体上对应的面的属性；想要获取的属性名为""字符串，""内不用加@；面序号为传入参数，是int型；面uv是传入参数，是vector型；一般配合xyzdist函数
len(数组变量名);                                  //计算数组内元素的个数并返回
foreach(数组内的元素类型 value;数组名){
    函数体                                       //遍历数组；类似C++中的增强for循环
}
floor(浮点类型变量名);                             //向下取整函数；返回整型数      
nearpoints(输入端序号，@P，限制搜索范围);            //得到输入端序号对应的模型上和主线模型的点的距离在搜索范围内的点的序号；返回值为输入端序号对应的模型上和主线模型的点的距离在搜索范围内的点的序号的数组，返回类型是整型数组；限制搜索范围为浮点型
sort(数组变量名)；                                 //将数组排序；返回值为排序好的数组
append/push(数组变量名,值);                        //将值插入数组；引用方式
removevalue(数组变量名,值);                        //将值从数组移除；引用方式
removeindex(数组变量名，数组下标i);                  //将数组中数组下标为i的元素删除；引用方式
ceil(浮点类型变量名);                               //向上取整函数；返回整型数
rint(浮点类型变量名);                               //四舍五入函数；返回浮点数
frac(浮点类型变量名);                               //舍弃小数点后的数字；返回浮点数
min(数组变量名);                                   //得到数组中最小的数；返回浮点数
max(数组变量名);                                   //得到数组中最大的数；返回浮点数
pow(浮点变量名a，k);                               //计算a的k次并返回；返回浮点数
distance(vector变量1，vector变量2);                //计算两个向量之间的距离并返回；返回浮点数；distance函数的底层原理是两向量相减得到一个向量，计算这个向量的长度
intersect(输入端序号，@P，方向向量，vector向量a，vector向量b);   //类似ray函数将点投射到模型上，但不产生实际作用；输入端序号对应的几何体为碰撞体；@P为主线的点坐标；方向向量为三维向量，决定了投射方向和投射半径，不同于ray节点的是投射方向不能朝向y轴且不在投射半径内的点不会被投射；向量a是传入传出参数，需要提前定义，记录投射的点在输入端序号的几何体上的面的位置坐标；向量b是传入传出参数，需要提前定义，记录投射的点在输入端序号的几何体上的面的uvw值；返回值为整形数，为投射点被投射到碰撞体的面的序号，无法投射/不在投射半径的点的返回值为-1;可用于删除，类似removepoint
volumesample(体积的输入端，体积的名称，@P);           //把体积的属性读到主线的点云/体积中(需要接收返回值)；返回值是float类型，需要接收；体积的输入端可以是输入端序号也可以是sop路径；体积的名称是字符串""；在volumewrangle节点或者attributewrangle节点点层级使用
volumegradient(体积的输入端，体积的名称，@P);         //计算体积的梯度并读到主线的点云中（需要接收返回值）；返回值是vector类型，需要接收；一般用于distance体积；体积的输入端可以是输入端序号也可以是sop路径；体积的名称是字符串""
length(向量);                                     //返回向量的长度
wnoise(@P，seed，f1,f2);                          //生成wnoise噪波；没有返回值，传出参数接收计算的值；seed是传入传出参数，int型，每一个cell一个编号；f1是传入传出参数，float型，是噪波的第一个值；f2是传入传出参数，float型，是噪波的第二个值；边缘锐化的方法是f2-f1作为噪波的输出值
removeprim(输入端序号，面序号，模式);                 //删除面；模式为1时删除面上的点
sin(变量)；                                        //以输入变量为横轴，输出正弦函数的函数值；需要调幅和调相
getbbox_size(输入端地址);                           //得到输入端模型的boundingbox三个维度的长度；返回值是vector类型；与坐标系无关；返回向量的分量不可能有负值
getbbox_center(输入端地址);                         //得到输入端模型的boundingbox的中心位置；返回值是vector类型
flownoise(@P,@Time);                              //得到变化连续的flow纹理；范围值介于0~1；返回值是float/vector类型
itoa(int变量)；                                     //将一个int变量转换为string类型并返回
getbbox_max(输入端地址);                             //得到输入端模型的boundingbox的每个轴向上对应的上界和下界在对应轴上投影的坐标的最大值；返回值是vector；当投影的坐标为负时，取最大，有正有负，取正；与坐标系有关；返回向量的分量可能有负值
getbbox_min(输入端地址);                             //得到输入端模型的boundingbox的每个轴向上对应的上界和下界在对应轴上投影的坐标的最小值；返回值是vector；当投影的坐标为负时，取最小，有正有负，取负；与坐标系有关；返回向量的分量可能有负值
max(传入参数1，传入参数2，...);                        //得到传入参数的最大值并返回
argsort(数组变量名);                                  //排序并返回原始下标，从小到大排列，返回值为原来各元素对应的原始序号的数组，不改变原数组；返回类型是整型数组
reorder(数组变量名，下标数组);                         //重排序，根据{}中写的数组的数的对应的序号对应的数，任意顺序重排；返回值为重排后的数组
addpoint(输入端序号, 位置向量);                        //创建点；返回值是新创建点的序号；输入端序号一般为0（自身）；位置向量为向量类型
addprim(输入端序号, 面类型字符串, 点序号1, 点序号2...); //创建面；返回值为int整型面的序号；面类型一般用"poly"（也可用"polyline"创建线）；点序号按顺时针或逆时针顺序写，三个点创建三角面，四个点创建四边面
removepoint(输入端序号, 点序号, 是否移除周围的面);      //移除点；是否移除周围的面：1则一并移除，0则不移除
neighbours(输入端序号, 点序号);                        //输出物体上某个点附近（相邻）的点的序号；返回类型是整型数组；输入端序号一般为0或geoself()
pcfind(输入端序号, 搜索通道, 通道属性值, 搜索半径, 最大点数); //搜索找出属性范围内的点的序号；输出形式为数组；搜索通道如"P"，即通过位置来找；通道属性值如搜索中心点（向量）；最大点数为搜索的点数的上限个数
setpointattrib(输入端序号, 属性名, 点序号, 值, 格式);   //修改点的属性；格式为固定字符串"set"，可省略；若属性名为非内部属性（自命名），会自动在gs视窗创建该属性
pointprims(输入端序号, 点序号);                        //找出包含该点的面；返回值为面的序号的整型数组
primpoints(输入端序号, 面序号);                        //从面上获取点的序号；返回值为点序号的整型数组
snoise(向量);                                          //生成范围在-1.7到1.7的噪波值；与rand函数相同的写法，不过生成连续的随机数；常用snoise函数
```

# 快捷键

alt+[左右添加视窗，`alt`+]上下添加视窗

c快速建模

y键断开连接

理线

- alt+左键添加关节点

ctrl+鼠标中键：在任意参数调节框中恢复到默认值

右键参数栏（带滑块控制条）→ copy parameter / `paste`：将参数引用复制到其他参数栏和`aw`编程框，做到参数引用关联

# 颜色

`rgb`三个分量都相同，为黑白色系

0是黑色，1是白色

# 节点

## 线

**line节点**

**curvepolygon节点**

- `nurbscurve`模式绘制贝塞尔曲线
  - 需要`resample`转换为`polygon`，从而拥有正常的点

**resample节点**

- 使得线的点的序号规整
- `segments`可添加动态
- 勾选`curveuattribute`可为线添加位置映射属性`@curveu`

**carve节点**
- 对线裁剪，添加动态
  - `firstu`和`secondu`控制动态，实际基于`@ptnum`/(`@numpt`-1)
- 提取点
- 裁剪运动的终端点的提取
  - `cut`中取消勾选`keepinside`只显示终端点不显示线
    - 此时的终端点不是对象，没有属性
  - `extract`中的`extracttype`选择`extract3disoparametriccurve`提取终端点为对象

**wireframe节点**

- 将线扫描成圆柱（放样线为管道）
- 在属性视窗中可以设置是否封口
- 在属性视窗中可以设置是否圆角
- 与**polywire节点**功能类似

## 合并

**merge节点**

- 可以合并显示，也可以合并渲染
- 属性对齐
- 自动解算
  - 将不同的**solver节点**`merge`在一起，可以共同解算
  - 碰撞解算
    - `relationship`设置为`colliderelationship`
    - `affectorrelationship`设置为`mutual`

**objectmerge节点**

- 可以使用通配符读多个模型
- 读取相机位置
  - 读取**camera节点**的`camorigin`
  - `transform`选项设置为`intothisobject`
  
- 读取**dop节点**内的通道/属性

  - 指定路径读取通道/属性的写法

    ```c++
    /obj/geometry节点名/dop节点名:*/通道名
    ```

    - 读取`dopnetwork`中解算的烟雾的`density`通道，使得显示烟雾

      ```c++
      /obj/geometry节点名/dopnetwork节点名:*/density
      ```

  - `transform`模式

    - 设置为`intothisobject`
    
  - 后接**filecache节点**将解算结果缓存


## 点

**scatter节点**

- 对于`fog`体积，`@density`为0的区域不会被撒点
- 基于属性撒点
  - 属性为0的区域不撒点
- 撒点是基于面的，包括按组撒点也是按面的组撒点
- 属性视窗中几项控制撒点的随机性（一般不建议开启随机性）
- 两项控制撒点上限和最大撒点数

**copytopoints节点**
- 物体的朝向拷贝后朝着点云法线方向
- 物体在拷贝前需要在世界`xz`平面的上方
- 物体的定位原理
  - 拷贝后，物体的坐标系（世界坐标）对齐到点云的坐标系，即世界坐标系对齐到点的自身坐标系（非世界坐标系，是3`@transform`的坐标系）
    - `xyz`轴对应对齐
    - 主要是物体朝向的世界轴和世界y轴
- piece `attribute`基于属性拷贝
  - 需要提前分别在点云和模型上定义i`@name`属性
    - 模型设置为1，点云需要拷贝的区域设置为1
    - 两者的`@name`相乘决定了是否拷贝
- 物体必须位于世界原点

**add节点**

- 添加点
- 将模型转换为点云
  - 点的分布遵循原模型，有规律

**clusterpoints节点**

- 将物体按空间位置分组
- `clusters`控制条控制分组的个数
- output `attribute`选项可以更改组的名称（默认为`cluster`）
- `output`选项选择导出模式，有点/面/顶点等
- 效果与撒点类似，可配合后续**aw节点**使用分组数据

  ```c
  if(i@T == int(@Time)){      //利用cp节点的分组属性名实现随时间物体不同位置向上插起的效果
      @P.y += 1;
  }
  ```

**copystamp节点**

- 与**copytopoints节点**类似，但连接对象都是模型
- 将物体A拷贝到物体B的点上去
- 与**ctp节点**的区别：物体A的点属性由自身决定，而非**ctp节点**那样由样条B（输入端B）决定
- `stamp`功能可以在属性视窗的`stamp`选项中勾选，实现引用不同输入端数据
- **cs节点**的输入与输出节点可以看作是单向的，而**ctp节点**的输出与输入节点是双向的
  - 可使用`stamp`函数引用不同输入端的模型数据

## 类型转换

**convert节点**

## sop

**mountain节点**

**normal节点**

- 计算法线

**blast节点**

- 删除点线面体积或者选择
  - 根据组属性或者`@name`选择不同的碎块

- 配合**assemble节点**使用
- 勾选`deletenonselected`并在`group`处指定组选择显示组属性对应的部分
- 勾选`deletenonselected`并在`group`处指定属性名，选择显示属性一致的部分
  - **assemble节点**生成的`@name`属性和**rbdmaterialfracture节点**生成的`@name`属性
- 选择显示`vdb`

**delete节点**

- 删除模型（一般针对合并为组后的模型）
- 在属性视窗`group`处最右端双击按钮，在`sc`视窗选择想要删除的部分（即组），然后按回车即可删除
- 也可在`group`选项处直接写想要删除的部分的组编号，例如 `1,2,3`
- 还可在下方`bounding` `volume`选项中点选`enable`按钮，在`sv`视窗按回车进入编辑小方框模式，小方框覆盖到的多边形会被删除


`foreach`系列节点
- 对每个元素单独进行计算，最后合并输出
- 监视节点
  - `detail`层级属性
    - `iteration`循环序号
    - `numiterations`循环总次数
    - value
      - **end节点**的`piece` `attribute`属性，区分不同的物体

**forloopwithfeedback节点**

- 实现for循环节点，将想要实现的功能节点拽入橙色区域即可
- **repeatend节点**是导出节点，其属性视窗中可调整最大循环次数
- 例如：实现对一个平面切割九次的效果，相当于把**polysplit节点**循环了九次
- 与`foreach`系列节点的区别：`forloopwithfeedback`每次迭代都基于上一次的结果，实现真正的迭代叠加

**attribpromote节点**

- 属性提升，例如将点层级的属性自动匹配到面层级

**connectivity节点**

- 根据点/面是否相连，为分隔的部分设置`class`属性
- 可配合`foreach` named `primitive`进行使用

**measure节点**

- 测量数据并返回属性

copy and **transform节点**

**group节点**

- 分组
- 基于整体分组
- 基于`bounding`区域分组
- 给分组命名

**groupcombine节点**

- 组运算

**groupdelete节点**

- 组删除

**setpointgroup节点**（`vex`函数）

- 在**aw节点**中可直接使用`vex`语言实现与**group节点**相同的功能
  ```c
  setpointgroup(0, "bb", @ptnum, 1);   //0为物体编号（自身），"bb"为组名，@ptnum为写入组的元素，1为在组内，0为不在组内
  ```
- `setpointgroup`函数可以将点的组改到另一个组，若之前定义了组属性，相应的点的属性也会改变
  - `name`为想要改到的组的名称
  - `value`为0或1，移入`name`处表示的组为1，移除为0
  - 第五个`mode`处写固定格式"set"，不写也可
  - 组属性在`gs`视窗中用`@group_`组名 = 1/0 表示

**setprimgroup节点**（`vex`函数）

- 与`setpointgroup`类似，创建面级别的分组
- 在**aw节点**中将属性的运行模式改为面，使用`vex`语言实现面分组
  ```c
  setprimgroup(0, "bb", i, 1);   //将所有面加入bb组
  ```
  或循环写法：
  ```c
  for(int i=0; i<@numprim; i++){
      setprimgroup(0, "bb", i, 1);
  }
  ```

**color节点**

- 添加颜色

**attribtransfer节点**

- 基于距离，距离阈值传播一个物体的属性到另一个物体
- 传递属性

**polywire节点**

- 放样线为管道
- 带有展`uv`功能
  - 相当于把管道剖开为一张平面，u为横向，v为竖向
  - u不用管，v的展`uv`模式需要调整
    - v `texture`设置为`attrib`模式

**mountain节点**

**lattice节点**

- 辅助贴合变形

**pointdeform节点**

- 类似**lattice节点**
- 对比**lattice节点**，更加灵活，输入是模型或者点云均可，不要求输入`lattice`

**uvproject节点**

- 为模型计算`uv`属性并记录到`vertice`层级
- 基于模型整体的`uv`，不是基于每个面

**merge节点**

- 合并两个物体

**ray节点**

- 被投射的对象可以是点云也可以是线
- 投射对象到模型上
- 投射方向是法线方向

**add节点**

- 选择点云中的点连接成线
  - 序号连续的点的缩写形式
    - a-b：按序连接序号从a开始到b的点
  - 按照属性连线
- 将连接成的线转变为封闭的面
  - 注意连接成的线的连续性，不要有相交
- 连接成线后删除没有用到的点

**sort节点**

- 改变点的序号
- `shift`模式
  - 可由时间驱动不断变化点的序号

**switch节点**

- 切换显示输入的节点，例如输入一个球和一个平面节点
- 在属性视窗的`selectinput`选项中通过移动滑块来切换显示的对象
- 选项中也可写数字（从0开始计数），也可写表达式
- 也可输入多个对象，数字分别对应输入的节点的线的编号

**switchif节点**

- 功能与**switch节点**相同，都是切换显示输入的节点
- 在属性视窗的此处拖动滑块改变选择对象
- 不同于**switch节点**：此处的函数有`point`()型，可通过引用属性值来动态切换
  - 写法示例：`point(输入线序号, 节点序号, "属性名", 属性值序号)`
  - 第0位：引用属性来自输入的第几根线
  - 第1位：定义属性的节点来自第几个节点（从非模型节点的第一个开始数）
  - 第2位：属性名称
  - 第3位：属性的第几个值
- 完成`point`函数书写后，按回车键再点击`enable`完成操作
- 例如：当平面的**aw节点**定义属性为0时显示平面，定义为1时显示球

**divide节点**

- 将模型的四边面转换为三角面

**subdivide节点**

- 增加模型的细分等级
- crease `weight`调整边的软硬值

**peak节点**

- 将模型沿着法线缩放
- 缩小模型

**polyreduce节点**

- 减面

**polyextrude节点**

- 挤出面
- 属性视窗中的`distance`选项控制挤出高度
- `divisions`选项控制挤出高度的分段数
- 属性视窗中的`twist`控制条可调整挤出模型的旋转角度，且是基于每个`uv`点的，非整体旋转

**polysplit节点**

- 属于**sop节点**，选择节点按回车键可进行切割线操作
- 在`sv`视窗完成操作后按回车键退出
- 在`ps`切割节点的属性视窗的`path` `type`选项将模式选为循环切割（`loop`）
- 在下方的`numberofloops`可调整切割数量

**blast节点**

- 删除组

**filecache节点**

- 将模型写入磁盘文件
- 加快运行速度
- 将模型读入
- 解算中间多用缓冲
- `geometryfile`处指定输出文件的路径和名字
  - 设置了`substeps`后，文件名中的$F必须改为$FF

- `sequence`选项
  - 设置输出帧的范围
  - `substeps`设置输出帧序列的步幅值（输出小数帧）
    - 使得不丢失解算的小数帧
    - 设置为1/时间轴的`step`（时间轴的最小单位）
- 输出文件的格式是.sc
- 将模型输出为.`sc`格式

**pointsfromvolume节点**

- 将模型转换为点云，不同于**add节点**和**scatter节点**生成的点云，模型内部也会生成点云
- 点的间距
- 随机性
- 点云严格生成在模型内，或者不严格
- 推荐的撒点方式，可控的多
- 勾选`addscaleattribute`创建`@pscale`属性
  - `@pscale`=pointseperation*particleradiusscale


**polyfill节点**

- 对模型进行封口

**polyframe节点**

- 计算法线和切线向量并记录属性
- 可用来基于点生成`xyz`三个轴向的向量
- 在属性视窗中将模式改为点，将三个向量命名为自己想要的名字
- 之后在`sv`视窗单击d键添加三个`marker`来显示即可
- 在`marker`编辑菜单中可调整向量的长度

**font节点**

- 生成一个字体形状的面
- 在属性栏中输入想要显示的文字就会显示出来
- 可以调整文字的大小和位置
- 在属性视窗单击封口按钮可以对文字轮廓进行封口操作
- 配合**polyextrude节点**可以制作立体文字效果

**visualize节点**

- 可视化显示属性，如法线N、**aw节点**添加的属性等
- 在**visualize节点**属性视窗的`visualizers`选项中单击加号新建一层可视化效果
- 在每一层的`visualizer`中单击铅笔按钮显示可视化效果
- 在每一层下方的`attribute`处写想要可视化显示的属性，如法线N
- 在上方的`style`选项选择想要显示的方式即可可视化显示
  - 若模式为`color`，则会以颜色显示，法线等在此模式下不会显示
  - 在`marker`模式下法线才会以向量的形式显示

**pointvelocity节点**

- 可以可视化显示点的属性（如速度）
- 先在点节点属性视窗右侧的此按钮显示点
- 然后回到**pv节点**单击铅笔按钮，进入节点界面后单击p或v可可视化显示p点坐标属性
- 可视化方式需要修改：在工具栏单击`visualization`按钮，在其`edit` `visualization`界面中将`type`改为`marker`，`style`改为`vector`，即可以向量形式显示点属性
- 在**pv节点**属性视窗中单击加号可新增一列`speed`属性，单击加号再添加一个力，在下方`curl` `noise`选项中单击铅笔后回到`add` `velocity`，调节滑块控制条即可实现添加力场使得曲线流动移动的效果

**uvunwrap节点**

- 展`uv`

**uvtransform节点**

- 移动`uv`

**file节点**

- 将模型读入

**attributepaint节点**

- 属性绘制，生成`@mask`属性
- 选择节点在`sv`视窗按回车键进入绘制模式
- 在属性视窗的`attributes`选项中将属性名和模式改为需要绘制的属性（如`Cd`和`color`模式）
- 在`brush`选项中修改想要绘制的颜色
- 笔触参数在颜色下面的滑块控制条处修改，笔触大小可直接滑动鼠标滚轮修改
- 注意：直接赋值时写数值即可
- paint a **mask节点**与`paint` **color节点**的本质都是`ap`绘制节点，区别仅在于节点属性名和模式的设置不同

**restposition节点**

- 记录固定的位置，输出固定位置属性`@rest`
- 第二个输入端为固定位置`@P`
- 当第一个输入端的`@P`一直在变，`@rest`恒等于第二个输入端的`@P`

**alembic节点**

- 在`obj`层级
- 内部的**alembic节点**导入`abc`模型
- 后接**convert节点**转换模型为`polygon`格式

**material节点**

- 赋予材质
- 按组赋予材质

**pack节点**

- 将模型打包
  - 打包后，只有一个点，一个面
- 可以将模型`gs`视窗中的所有点打包为一个点，可以理解为将物体看做一个点
- 可以显著减少占用内存和缓存
- 可以解决卡死问题，避免点过多造成的混乱和卡死
- 选择打包节点在其属性视窗，清除或命名非打包节点输入线的名称，之后进入打包节点发现只有这两根命名的线了
- 进入打包节点后，将节点属性栏中想要添加的属性拽到打包节点属性编辑器中即可创建节点的属性栏
- 在打包节点后设置节点参数时，每个参数的标签可以设置为中文，输入后按回车键即可确认
- 在打包节点参数设置菜单中可以将参数的范围进行限制，锁的图标点亮代表最小值或最大值的限制生效

**unpack节点**

- 将模型解包
- `transferattributes`处将**pack节点**的属性继承到解包后的每个点

**transform节点**

- 移动模型
- 快速将模型移动至世界原点

**fuse节点**

- 焊接相邻点

**attributerename节点**

- 重命名属性

**null节点**

- 空节点
- 空渲染/显示

**uvquickshade节点**

- 将贴图投射到带有`uv`属性的物体表面，类似于**colormap节点**
- 检查`uv`

**ropalembicoutput节点**

- 将模型导出为`abc`格式

**attributewrangle节点**

- 修改层级的属性
- 对粒子（解算后的点）属性的修改，是在点层级

**grouppromote节点**

- 针对组的属性层级转换

**boolean节点**

- 模型布尔运算

**attributefrommap节点**

- 将贴图贴到模型上，模型的`@Cd`记录贴图的颜色
- 和**colormap节点**的作用相同

**trail节点**

- 为运动的对象计算并添加速度属性`@v`，为运动的对象计算并添加加速度属性；基于运动轨迹撒点
- 模式设置为`computevelocity`计算并添加速度属性
- 基于对象相邻帧的移动距离计算速度
- 模式设置为`centraldifference`计算加速度
- 默认模式为基于粒子运动轨迹撒点
  - 在粒子速度的反方向上从粒子当前位置出发撒点
  - `trailincrement`控制撒点间距
  - `traillength`控制撒点个数

**collisionsource节点**

- 将`sop`模型转换为体积
- 可以控制体素精度
- 第二个输出端输出的是体积，第一个输出端输出的是原始模型

**bonedeform节点**

- 针对有骨骼动画的`fbx`模型使得模型动画显示正常

**assemble节点**

- 拆分模型，根据拓扑将模型拆分成不同的部分，勾选`createnameattribute`会为不同的部分的面层级生成不同的`@name`属性，相同的部分的面层级生成相同的`name`属性
  - 区分不同部分的依据是面层级的`name`属性
    - 该节点生成的`@name`属性对比**rbdmaterialfracture节点**生成的`@name`属性，同样有效，但是可选择性不强，只有一级
    - 如果之前有`@name`属性，`assemble`可以选择关掉`createnameattribute`来继承之前的`@name`属性
  - 勾选`createnameattribute`会覆盖掉之前的`@name`属性
- 勾选`creategroups`为拓扑不连续的不同的部分创建组属性，即分组
  - 为每个碎块生成组属性
- 勾选`createpackedprimitives`将不同的部分打包

**attributedelete节点**

- 删除属性，加快进程运行速度

**attributeblur节点**

- 属性插值节点，平滑属性

**smooth节点**

- 属性插值节点，平滑属性

**attribnoise节点**

- 给任意属性添加噪波，产生随机效果
- 在属性视窗的此处选择相应的属性类型和名称（如`Cd`、P等）即可
  - attribute `class`选项选择作用的层级（点/面等）
  - 两项控制添加噪波的模式和强度
- post-`process`选项的最大值和最小值调整噪波的上界和下界
- noise `value`选项中可以激活`ramp`梯度控制条，控制噪波的混合效果（相加相减和混合度）
- 单击`amplitude`最右侧的`xyz`按钮，可以分`xyz`三个组成部分进行细节调整噪波强度
  - 对于`@Cd`、`@orient`这种向量属性就有`xyz`三个坐标可以分别调整
  - 不同于`amplitude`的整体大调
- **attribnoise节点**可以叠加，即制作多重噪波效果

**pyrosource节点**

- 将模型转换为点云
- `mode`设置撒点模式
  - `volumescatter`体积内撒点
  - `surfacescatter`表面随机撒点
  - `keepinput`提取模型本身的点
- `@pscale`=`particleseparation`的值*`particlescale`的值
  - `particleseparation`越小，体积的外形越准确
  - `particlescale`越大，体积单元越大，即体素个数越多

- `attributes`增加属性，可以被体积继承
  - 默认为1
  - `temperature`、`density`

**volumerasterizeattributes节点**

- 将点云转换为体积
  - 可以由粒子驱动烟雾
- 前连**pyrosource节点**/点云
- `attributes`指定继承自粒子的属性
  - `@density`、`@temperature`、`@pscale`、`@v`
- `voxelsize`控制体素大小/精度
  - 一般与`dop`解算的精度一致

- `particlescale`影响`@pscale`的缩放，进而影响体积单元的大小
- `coverage`对属性进行缩放
  - 一般`coverageattribute`不设置任何属性
    - 继承的属性的值会自乘`coverageattribute`的值

- 输出`vdb`

**volumetrail节点**

- 第一个输入端连点云，第二个输入端连体积

- 将体积的速度属性以颜色的形式在点云上可视化显示
- `velocityvolumes`处指定体积的速度属性

**volumevisualization节点**

- 显示体积的属性
  - 显示不可见的`vdb`

- `minimum`和`maximum`设置为体积`density`的最小值和最大值
  - 一般不变

- `diffusefield`指定显示的属性
- **name节点**重命名`density`属性场后无法显示
  - `smoke`选项
    - `densityfield`处指定**name节点**重命名后的`density`属性场的名称
    - `mode`处设置为`noramp`


**voronoifraction节点**

- 用于切割刚体生成小碎块

- 输入端

  - 第一个连刚体
  - 第二个连切割基于的点云

- 会生成面层级的属性

  - 字符串类型属性`name`

    - 记录每个碎块的名字，用于区分不同的碎块
    - 在`pieceprefix`处修改前缀的名称

  - `group`分组属性

    - 可见的朝向外部的面的分组名为`outside`

      ```c++
      @group_ouside;                            //int类型；在组内为1，否则为0
      ```

    - 不可见的朝向内部的面的分组名为`inside`

      ```c++
      @group_inside                             //int类型；在组内为1，否则为0
      ```

**explodedview节点**

- 爆炸视图查看碎块

**uvtexture节点**

- `arclengthspline`模式

  - 用于生成线的`@uv`属性，可用于生成线上点的位置映射

    - `attributeclass`设置为`point`

    - 后接**aw节点**

      ```c++
      @curveu=@uv.x;                                      //@curveu=@ptnum/(@numpt-1)
      ```

**rbdmaterialfracture节点**

- 不能切割面，必须切割有体积的模型
  - 可用`polyextrude`挤出厚度后切割

- `pieceprefix`用于设置`@name`属性分级间的字符
  
  - 用于区分其他**rbdmaterialfracture节点**生成的`@name`属性
  
- materialtype
  - 设置为`glass`产生玻璃破碎的碎块
    - 碎块模式是基于点云呈放射状
  - 设置为`wood`木头切割模式

- 切割刚体产生小碎块

- 第二个输入/输出为约束，第三个输入/输出为简模（不带`interiordetail`的结果），第四个输入为切割基于的点云

  - 只有该节点正确连接（高模连高模，约束连约束，简模连简模）后续的`rbd`系列节点时，后续`rbd`系列节点才能正确输出简模

- `detail`选项

  - 勾选`interiordetail`开启碎块内部面的细节
  - `detailsize`控制碎块内部面凹凸纹理的程度
    - 越小越凹凸，越大越平整
  - `frequency`控制凹凸纹理的频率
    - 越大，凹凸越细碎
  - 勾选`edgedetail`为切割刀面添加噪波
  - `lacunarity`控制每层的纹理大小
    - 越大，每层的纹理小
  - `levelmultiplier`控制该层的纹理大小对上层纹理大小的缩放
  - 大规模场景使用材质内置换的方式生成内部面凹凸

- `chipping`选项

  - `chippingratio`控制可以生成碎屑的碎块个数
    - 越大，越多的碎块可以生成碎屑
  - `cornorratio`控制每个碎块生成的碎屑个数
    - 越大，碎块角落生成的碎屑越多，生成碎屑的角落越多
  - `cornordepth`控制碎屑的大小
  - `directionalnoise`控制碎屑的形状
    - 越大，碎屑的形状越整体
  - 大规模场景，使用激活点云实例的方式生成碎屑

- `primaryfracture`选项

  - `fracturelevel`控制碎块的切割次数
    - 每一次切割都是在上一次切割的碎块的基础上继续切割，切割对象是上一次的碎块
  - `fractureratio`控制该次切割对象来自上次切割的比例
    - 控制比例，生成不同切割等级的碎块，有层次感
  - 勾选`inputspoints`基于第四个输入端的点云进行切割
    - 点云所在的位置切割的碎块越细碎，越远越整体
    - 点云中的点越多，切割出的碎块越多越细碎，反之越少越整体
  - `scatterpoints`控制该次切割的碎块数
    - 当使用第四个输入端基于点云切割时，应该设置为0，避免干扰，或者也可以混用两种切割方式

- 生成面层级的属性

  - 字符串类型属性`name`

    ```c++
    s@name="piecei-j-...-a-b"                        //i为未切割前碎块的块号，固定为0，因为只有一个碎块；j为第一次切割产生的碎块在其所属的上一次切割的碎块中的块号；b为第n-1次切割产生的碎块在其所属的上一次切割的碎块中的块号
    ```

  - `group`分组属性

    - 可见的朝向外部的面的分组名为`outside`

      - 分组内包含的面数是所有`fracturelevel`切割所产生的超向外的面的总和

        ```
        @group_outside
        ```

      - 每一层`fracturelevel`会为上一层的切割的`outside`组内增加面数

      - 每一次`fracturelevel`的切割所产生的碎块有自己独立的`outside`组和`inside`组

        ```c++
        @group_concrete_fracturekoutside                     //k为fracturelevel的值，即切割的次数
        ```

    - 不可见的朝向内部的面的分组名为`inside`

      - 分组内包含的面数是所有`fracturelevel`切割所产生的朝向内的面的总和

        ```
        @group_inside
        ```

      - 每一层`fracturelevel`会为上一层的切割的`inside`组内增加面数

      - 每一次`fracturelevel`的切割所产生的碎块有自己独立的`outside`组和`inside`组

        ```c++
        @group_concrete_fracturekinside                     //k为fracturelevel的值，即切割的次数
        ```

    - `chipping`碎屑

      - `concrete_chippinginside`是碎屑的`inside`与碎屑所在碎块`inside`的交集

        ```c++
        @group_concrete_chippinginside
        ```

      - `concrete_chippingoutside`是碎屑的`outside`与碎屑所在碎块的`outside`的并集

        ```
        @group_concrete_chippingoutside
        ```

      - `concrete_chips`是碎屑的`outside`与`inside`的并集，代表实际的碎屑的组

        ```
        @group_concrete_chips
        ```

**rbdconfigure节点**

- 为刚体添加用于解算的属性
  - 对碎块属性的设置只能通过该节点，否则会丢失简模、约束等信息
  - 可以为不同的刚体（`merge`之后的）根据不同的`@name`属性分别设置属性
  
- 勾选`speedmin`设置初速度
- 勾选`geometryrepresentation`设置刚体按凹面还是凸面解算
  - `concave`凹面解算模式精度高，但是结算慢

- 勾选`active`
  - 设置为1，刚体参与解算
  - 设置为0，刚体不参与解算
    - 可以后连**rbdbulletsolver节点**的第四个输入端作为地面
- 勾选`deforming`
  - 设置为1继承上游节点的动画，带动画的刚体参与解算
  - 动画刚体参与解算的流程
    - **rbdmaterialfracture节点**切割未加动画前的模型→**assemble节点**分别`pack`打包高模和底模的对象→分别为高模和底模的对象设置同一个动画→**rbdconfigure节点**添加解算属性，勾选`deforming`并设置为1→高模和底模对象进入**rbdbulletsolver节点**进行解算

- 会将模型进行打包

**booleanfracture节点**

- 用于切割模型产生碎块
- 第一个输入端连模型，第二个输入端连面作为切割面

**polybevel节点**

- 倒角
- 后接**normal节点**计算法线

**rbdpack节点**

- `rbd`系列节点中的**pack节点**

**rbdunpack节点**

- `rbd`系列节点中的**unpack节点**

**rbdio节点**

- `rbd`系列节点中的**filecache节点**
- 四个输入端都连**rbdbulletsolver节点**对应的输出端
- 模式选择`simulationpoints`缓存模拟点云
- 第四个输出端输出模拟点云
- 加快解算和缓存速度

**rbdexplodedview节点**

- 爆炸视图显示碎块
- 对比**explodedview节点**的劣势是无法只查看简模的爆炸视图

**connectadjacentpieces节点**

- 根据**rbdfractionmaterial节点**输出的简模，生成约束形状

- 模式设置为`adjacentpiecesfromsurfacepoints`

  - 用于生成内部约束形状(碎块内部的约束线)，一般是`Glue`约束

- 模式设置为`adjacentpoints`

  - 将输入的点云连接成约束线

- 模式设置为`adjacentpiecesfrompoints`

  - 用于生成外部约束形状（碎块的`outside`边缘的约束线），一般是`Soft`约束
  - 线不能过多，少量即可

- 勾选`lengthattribute`

  - 产生`@restlength`记录每个约束线的长度于对应的面层级
  - 不勾选不会被识别为约束

- 后接**aw节点**（面层级）

  ```c++
  s@constraint_name="Glue约束的名字";                    //与rbdbulletsolver节点的advanced选项中的Glue约束的dataname要一致
  @strength=约束强度;                                    //设置约束的强度；强度取决于物体大小
  ```

  ```c
  s@constraint_name="Soft约束的名字";                    //与rbdbulletsolver节点的advanced选项中的Soft约束的dataname要一致
  @stiffness=约束强度;                                  //设置约束的强度；强度取决于物体大小
  ```

**transformpieces节点**

- 由输入的模拟点云(**rbdbulletsolver节点**的第四个输出端)和**rbdfracturematerial节点**处理过的模型（必须是同一套破碎完全一样）输出和**rbdbulletsolver节点**一样的解算
- `attribute`处指定`name`属性
  - 基于`name`属性
- 如果刚体带动画，用动画刚体解算结果的模拟点云驱动未加动画前的刚体即可输出解算


**debrissource节点**

- 在解算的碎块的`inside`面上生成粒子，可以作为后续发射源
- 有两个输入端，一个连高模，一个连模拟点云
- 会为每个粒子生成`age`属性
  - `lifespan`设置寿命长度
- 勾选`restposition`记录每个粒子出生时的位置为属性
- 勾选`pointnumberattribute`记录每个粒子的`id`并添加到属性
- 勾选`removeunreleased`和`removeatlifeend`删除不需要的点和已经死亡的点
  - 取消勾选`removeunreleased`可以配合`distancethreshold`调整激活点云生效的阈值
- `distancethreshold`控制激活点云生效的阈值
- `densityscale`控制激活点云的点的数量

**collisionsource节点**

- 将刚体转换为被动碰撞刚体
- 会为输入端的物体计算速度v属性
- 将第一个输出端和第二个输出端`merge`在一起后连**pyrosolver节点**的第二个输入端
- 第一个输出端输出`geometry`，第二个输出端输出`vdb`
- `volume`选项
  - `voxelsize`设置输出的`vdb`精度，越小，碰撞精度越高
  - 勾选`fillinterior`使得最内部也有属性
- 不同的**collisionsource节点**可以`merge`后连到**pyrosolver节点**的第二个输入端
  - 使得它们都与烟雾发生碰撞


**reverse节点**

- 翻转法线
- 配合**normal节点**用于正确显示法线，先`reverse`再计算`normal`

**box节点**

- 为输入端的几何体生成其`boundingbox`

**clean节点**

- 勾选`removeunusedpoints`删除没有用到的点

**primitiveproperties节点**

- 可用于不显示某个属性场/vdb
- `sourcegroup`处指定属性场的名称

- `volumes`选项
  - 勾选`adjustvisualization`
  - `displaymode`设置为`invisible`
    - 不显示某个属性场/vdb

**dopnetwork节点**

- `cache`选项
  - 勾选`savecheckpoints`开启写出缓存
    - 减少内存占用，加快解算速度
    - 写出文件的后缀是`sim`
      - 与**filecache节点**写出的`sc`文件相比，内容更多
      - `sim`文件的用法
        - 断点续算
          - `simulation`选项中的`initialstate`处指定对应帧的`sim`文件，从对应帧开始继续解算
    - checkpointtraillength
      - 设置为0
        - 每帧的解算结果都会被写出，`sim`文件记录了每一帧的数据
        - 此时`sim`文件不支持覆盖，要想覆盖，必须先手动删除
        - 播放条的蓝条/内存中的解算缓存覆盖所有帧
      - 设置为k
        - 只会有k个`sim`文件
        - 此时`sim`文件自覆盖，更新当前帧以及之前k帧的数据
        - 播放条的蓝条/内存中的解算缓存只覆盖当前帧以及之前的k帧，不断更新

## 解算

**solver节点**
- 内部的**Prev_Frame节点**保存并更新为上一帧的结果，不保存更早帧的结果
- **Prev_Frame节点**后接**aw节点**等效果节点
- 最后连接**Output节点**输出解算结果，作为**Prev_Frame节点**的输入
- 内部的`Object` **Merge节点**导入外部的模型
- 可以循环重复计算当前帧的状态到每一帧上，实现随时间迭代的效果
- 注意：**solver节点**一定要进入其最内层的**prev_frame节点**下面操作
- 注意：一开始输入属性后就定死了，要想实现不同的效果，要给定条件（如用if语句只给第一帧的初始状态而非锁死）
- 注意：在**solver节点**内层添加**aw节点**写完功能后，一定要激活**aw节点**，之后回到外层播放效果才会显示
- 属性视窗的`sub` `steps`表示一帧做几遍运算
- start `frame`表示从第几帧开始动画
- 实质上和for循环类似，只不过`solver`是自动计算，随着时间重复迭代计算当前命令
- 可配合**pack节点**使用，解决点过多造成的卡死问题

**pyrosolver节点**

- 发射烟雾
- 第一个输入端连`vdb`体积作为解算源，第二个输入端连**collisionsource节点**作为被动碰撞刚体
- 烟雾运动本质是`vel`速度场的结果，烟雾形状本质是`density`属性场的结果
- 解算`fog`体积
  - 将进行**cloudnoise节点**加工后的`fog`体积作为解算源
- 控制解算精度，类似体素精度

  - 解算精度应该对齐`vdb`的`voxelsize`
- `sourcing`处增加读取的通道/属性
  - 读取上游的体积的某个属性到解算对象的某个属性中，使得上游属性能被写到属性场作为初值参与解算
    - `temperature`、`density`、`vel`、`Cd`、`flame`
      - 注意`Cd`属性要与`density`相乘，`density`接近0，颜色越深，为了消除`density`对`Cd`的影响，后续节点中需要对`Cd`随机
      - v属性不仅受温度/浮力的影响，还受空气阻力的影响
        - 烟雾解算的空气阻力无需设置，节点内部自带的
      - `density`不是必须的，不读取时，不设置初始`density`
        - 只根据`flame`解算`fire`
        - `emitfromflame`发射`smoke`
      - `burn`映射`flame`
  - `operation`解算模式
  - `sourcescale`设置`sourcevolume`处属性乘对应倍数后赋予`targetvolume`处解算对象的对应属性
  - 将上游体积的`temperature`映射到解算对象的`temperature`通道，模式为`pull`
    - 烟雾解算是温度驱动，必须有正确的温度属性
- 产生沿着方向烟雾上升的效果的本质驱动属性是`temperature`温度属性
  - 温度越高，物理密度越低，烟雾往上走
- `collision`选项
  - collisiontype
    - `collisiongeometry`模式
      - 第二个输入端连**collisionsource节点**的两个`merge`在一起的输出端
      - `collisionvoxelsize`与**collisionsource节点**中的`voxelsize`要一致
- `fields`选项
  - `fieldguide`处指定辅助显示的属性场
    - `plane`模式
      - `planeorientation`设置辅助显示平面的位置

      - `guiderange`设置属性被映射的范围
        - 在这个范围内的属性被映射为`colorramp`的横轴0到1
  - 勾选`speed`开启速度通道
    - 勾选后才能作为控制属性`controlfield`
  - density
    - `dissipation`处设置烟雾的寿命
      - 值越小，寿命越大
      - 相同的值，不同的精度，寿命也不同
        - 不同的精度，寿命要相同，值不能相同
        - 保持相同的寿命，精度越低，值越大
    - 勾选`emitfromflame`使得`flame`属性场可以影响`density`属性场
      - `flame`属性场发射`smoke`，增加浓度
      - `operation`模式
        - `add`模式发射的量大于`maximum`模式
      - `emissionscale`控制发射的数量级
      - `flamerange`控制`flame`属性值在范围内的部分产生浓度
  - temperature
    - 温度场是必须有的，要么来自于`sourcing`，要么来自于`emit`
    - `coolingrate`处设置温度降低的速率
      - 越大，温度降的越块
    - 勾选`emitfromflame`使得`flame`属性场可以影响`temperature`属性场
      - `flame`属性场发射温度，升温
        - 一般用于爆炸
      - `operation`模式一般设置为`add`
      - `flamerange`控制`flame`属性值在范围内的部分产生温度
  - flame
    - 勾选`createflamefile`创建`flame`属性/场
    - 在`fields`选项中勾选`density`和`temperature`的`emitfromflame`，使得`flame`属性场可以影响`density`属性场和`temperature`属性场，产生真实的物理效果
    - `flamelifespan`设置`flame`属性的消散，单位是秒
      - 越小，消散的越早，越大，消散的越晚
- `shape`选项/`force`选项
  - 产生和控制力场
  - `buoyancy`选项
    - 产生浮力场，由`temperature`属性场驱动
    - 温度越高，物理密度越低，烟雾在浮力的驱动下往上走
    - 温度越高，浮力越大
    - 温度以开尔文温度K为度量
      - 开尔文温度最低是0k
      - k=c+273
    - 每个体素的温度从起始温度`ambienttemp`升到最终温度`referencetemp`
      - 最终温度与起始温度的温差越大，烟雾升的越高（烟雾可以达到的最大高度）
      - 升的快慢可以解算后用**timewarp节点**来调，也可以用`buoyancyscale`调
      - 控制升的高度
    - 浮力大小`buoyancyscale`
      - 浮力越大，烟雾升的越高（烟雾可以达到的最大高度）
      - 浮力越小，越不会往上升
      - 控制升的快慢
    - 浮力方向`gravity` direction
      - 朝向哪个方向，哪个方向的标量为-1
  - `wind`风场
    - 调整风的朝向和风力大小
  - `turbulence`纹理噪波
    - 是针对解算中间过程的扰乱，不是对发射源的扰乱，发射源需要在进入结算前自行扰乱
    - 噪波应该在小的`voxelsize`下调整即高精度下，否则看不出区别
    - 实际影响的是速度场
    - 产生大的扰乱
      - 块扰乱效果
    - `turbulence`强度
      - 强度越大，效果越明显
    - `swirlsize`纹理大小/频率
      - 越大，纹理越整体
      - 越小，纹理越细碎
      - 依据`@pscale`的大小/单元大小来调整
    - `thresholdfield`指定噪波影响的已经`sourcing`通道，即作用域
      - 一般为`temperature`
    - `thresholdrange`指定噪波影响的范围
    - 勾选`controlfield`开启并设置噪波的控制属性场
      - `controlfield`处指定控制属性场
        - temperature
      - `controlrange`指定控制属性的范围
        - 控制属性的值在范围内的区域，噪波生效，否则不生效
          - 确定范围需要配合`fieldguide`来确定
        - 单击`computerange`计算当前帧控制属性的最值
  - `disturbance`纹理噪波
    - 是针对解算中间过程的扰乱，不是对发射源的扰乱，发射源需要在进入结算前自行扰乱
    - 噪波应该在小的`voxelsize`下调整即高精度下，否则看不出区别
    - 实际影响的是速度场
    - 产生小的扰乱
      - 边缘扰乱效果
    - `baseblocksize`纹理大小
      - 设置为解算精度的3到5倍
    - `disturbance`强度
      - 强度越大，越明显
      - 以5为单位往上设置强度
    - 勾选`usecontrolfield`开启并设置噪波的控制属性场
      - `controlfield`处指定控制属性场
        - speed
      - `controlrange`指定控制属性的范围
        - 控制属性的值在范围内的区域，噪波生效，否则不生效
          - 确定范围需要配合`fieldguide`来确定
        - 单击`computerange`计算当前帧控制属性的最值
  - flameexpansion
    - 勾选产生膨胀力场，产生`divergence`属性场，影响`vel`属性场，模拟爆炸
    - `divergence`属性可以在没有浮力的情况下产生膨胀也就是爆炸效果
  - shredding
    - 对速度场产生高频细碎的旋转，从而产生更加细碎的噪波
      - 比`turbulence`更加细碎
    - `shredding`控制强度
    - `thresholdfield`指定作用对象/作用场
      - `flame`、`density`、`temperature`
      - 指定`flame`时可以使得模拟效果更像燃烧的火焰，有火焰流动的效果，更加自然
      - 指定`density`时可以破除解算初期的规整，一般在内部的**gasshred节点**中设置
    - `controlfield`指定控制属性场
  - viscosity
    - 黏性
- `look`选项
  - 为烟雾添加材质
  - `densityscale`设置渲染的烟雾浓度
  - bindings
    - `fireintensityvolume`处指定火焰强度的控制场
      - 一般是`flame`属性场
    - `firecolorvolume`处指定火焰颜色的控制场
      - 一般是`flame`属性场
    - `smokevolume`处指定烟雾浓度的控制场
  - fire
    - `intensity`的`sourcerange`控制火焰强度在对应的控制场中生效的范围
      - 一般与`color`的`sourcerange`相同
    - `color`的`sourcerange`控制火焰颜色在对应的控制场中生效的范围
- `setup`选项
  - `globalsubsteps`即`substeps`步幅值
    - 提高可以增加解算的流畅度与连续性、准确性

  - `voxelsize`设置解算精度
- `bounds`选项
  - resizing
    - 调制烟雾解算过程中，激活区域的膨胀
      - 激活区域越小，解算压力越小
    - `padding`控制激活区域扩大的程度
      - 越大，激活区域越大，烟雾不会被裁切
        - 超过激活区域的烟雾会被裁切
    
      - 烟雾快速运动时，需要增大`padding`
    
    - `referencefields`为`padding`针对的激活区域的检测属性
      - 基于检测属性的区域为烟雾的激活区域添加`padding`
        - 激活区域外有检测属性，会对激活区域添加`padding`
        - 非检测属性，不会被考虑，即使激活区域外有非检测属性，也不会对激活区域添加`padding`
- `output`选项
  - 选择输出的属性
    - 每个属性对应一个属性场，每个属性场对应一个`vdb`体积
  - 勾选`converttovdb`选择输出为`vdb`体积，默认输出为`volume`体积
    - 一般选择输出`vdb`
  - 勾选`resamplevolumes`并指定属性场名称
    - 对指定的`vdb`的体素精度/大小做修改
      - 降低指定`vdb`的内存大小，例如`vel`
    - `voxelsizescale`修改指定的`vdb`的体素精度
      - 指定的`vdb`的体素精度/大小=`setup`中的精度*`voxelsizescale`的值
- 内层
  - **gasturbulence节点**
    - 后连**force_output节点**
    - 为属性场添加噪波，提供更精确和接近外层`disturbance`的噪波
      - 一般作为扭曲外层`turbulence`噪波，破除静止

    - `turbulencesettings`选项中设置噪波纹理和大小
      - `scale`强度一般是0.x的强度
    - `controlsettings`选项中设置噪波的生效范围/作用对象
      - 勾选`controlfield`开启并设置噪波的作用对象/作用场
        - temperature
    
      - `controlinfluence`设置为1
      - `controlmin`为控制属性的最小值
        - 作用场属性的值低于最小值的区域，噪波不生效
        - 最小值可根据外层`fieldguide`来确定
    
      - `controlmax`为控制属性的最大值
        - 作用场属性的值高于最大值的区域，噪波不生效
        - 最大值可根据外层`fieldguide`来确定
  - **gasshred节点**
    - 后连**force_output节点**
    - 等价于外部`shape`中的`shredding`，对属性场产生高频细碎的噪波纹理
    - `controlsettings`选项中设置噪波的生效范围/作用对象
  - **merge节点**
    - 使得不同的**gas节点**一起产生作用
    - 后连**force_output节点**
  - **gasfieldwrangle节点**
    - 后连**advection_output节点**或**force_output节点**
    - `vex`编程对解算中的属性做修改（不能直接写，会覆盖掉力场作用下的结果）
      
      - ```c
        @vel;                                             //速度属性；vector类型
        ```
      
      - 修改速度场
      
        - 自定义速度场：将缩放单位旋转力和缩放单位向心力叠加到`@vel`上
          - **单位向心力方向 = normalize(`@P` - 吸引点坐标)**，即从吸引点指向当前体素的径向
          - **单位旋转力方向 = normalize(cross(单位向心力, 旋转轴))**，即切线方向
          - `rot_scale`控制旋转力的强度，越大旋转速度越快
          - `cent_scale`为负值时向心力朝向吸引点（收拢效果），为正值时远离吸引点（扩散效果）
          - ```c
            vector attract_pos = chv("center");                        //吸引点（旋转中心）坐标；vector类型
            vector rot_axis = normalize(chv("axis"));                 //旋转轴方向（已归一化）；vector类型
            
            vector radial = @P - attract_pos;                      //从吸引点指向当前体素的径向向量；vector类型
            vector radial_unit = normalize(radial);                 //单位向心力方向；vector类型
            vector rot_unit = normalize(cross(radial_unit, rot_axis)); //单位旋转力方向（切线方向）；vector类型
            
            float rot_scale = chf("rot_scale");                  //旋转力缩放系数；float类型
            float cent_scale = chf("cent_scale") ;                //向心力缩放系数；负值朝向中心；float类型
            
            @vel += rot_unit * rot_scale;                           //叠加旋转力到速度场；vector类型
            @vel += radial_unit * cent_scale;                       //叠加向心力到速度场；vector类型
            ```
    
  - **force_output节点**
    - 最先执行，在所有其他步骤之前
    - 连接`gasturbulence`、`gasshred`、`merge`、`gasfieldwrangle`等施力节点，对速度场`@vel`施加力
      - 此处施加的力会参与后续的压力投影修正，是修改`@vel`的首选挂载点，效果最自然
      - 直接影响烟雾/火焰的运动轨迹，如旋转、弯曲、被风吹动等效果
  - **sources_output节点**
    - 在力之后、平流之前执行
    - 连接修改`density`、`temperature`、`fuel`等源场的节点，控制烟雾/火焰的生成量和位置
      - 此时稀疏模拟的`active` `field`尚未更新，在稀疏模拟下慎用
      - 可用于按条件动态开关发射源、或修改特定区域的属性场强度
  - **advection_output节点**
    - 最晚执行，在平流阶段
    - 前连接`gasfieldwrangle`等节点，在平流发生前读取或修改速度场`@vel`
      - 此处对`@vel`的修改会被后续的压力投影部分抵消，不适合作为主要施力点
      - 适合读取原始速度数据、或手动平流`cd`等自定义属性场


**popnetwork节点**（粒子）

- 属性
  - simulation
    - 时间的加速倍数`scaletime`，相当于调整解算的快慢
    - `substeps`每帧解算次数
      - 增加次数，使得细节更多，使得发射粒子的个数增多
      - 设置次数为n，每帧解算n次，每1/n帧发射一次粒子
      - 增加次数，使得粒子与刚体碰撞的精度更高
    - `offtime`时间偏移，单位秒
  
- **popsource节点**
  - 将连接**popnetwork节点**的模型作为粒子发射源的来源
    - 发射源是点云
  - 网格显示
  - source
    - `emissiontype`发射粒子模式
      - `scatterontosurfaces`随机撒点作为发射源
      - `points`将模型规整的点作为发射源
      - `allgeometry`模型作为发射源
      - `allpoints`将模型规整的点作为发射源，发射点每帧发射一个粒子
    - `geometrysource`和`sourcegroup`指定发射源来源的`sop`对象
  - birth
    - `constbirthrate`每秒/24帧发射粒子的个数，`constactivation`控制`constbirthrate`是否生效
    - `impulsecount`每帧发射粒子的个数，`impulseactivation`控制`impulsecount`是否生效
    - `maxpointsperframe`每帧发射的粒子数
      - 初始时控制粒子源的个数
      - `points`模式才能设置
    - `maxsimpoints`发射出的粒子的最大个数
    - `justborngroup`分组属性
      - 当前帧发射的粒子的属性为1，其他为0
      - 需要组名
    - `seed`为发射增加随机性
    - `lifeexpectancy`控制粒子的生命周期
      - 以秒为单位
      - 实际决定了粒子可以达到的最大高度
    - `lifevariance`控制粒子生命周期的随机性
      - 粒子的生命值=生命周期+/-`lifevariance`间的一个数
  - attributes
    - 使粒子继承粒子发射源来源的属性
    - 继承速度`@v`属性
      - `addtoinheritedvelocity`模式可对继承的`@v`属性加`velocity`+-`variance`范围的速度
      - `setinitialvelocity`模式不继承速度，自行设置初速度
    - 对于没有被继承的外部上游的属性，会被删除
  - stream
    - **popsource节点**发射的所有粒子有分组属性，所有生命周期内的粒子的属性相同
    - 修改组名
  
- **popsolver节点**
  
  - 解算器
  - 紫色输入端连接粒子流，可以同时解算两个粒子流
  - `collisionbehavior`选项
    - `response`设置粒子碰撞地面`groundplane`（被动刚体）时的行为
      - `die`：碰撞时死亡
      - `stop`：碰撞时停止运动
        - 可用于测试碰撞精度
      - `stick`：碰撞时粘贴到地面刚体表面，忽略精度
      - `slide`：碰撞后粒子在刚体表面滑动
      - `unchange`：默认的正常碰撞
    - 勾选`addhitattributes`添加碰撞相关的属性
  
- **popobject节点**
  - 设置粒子属性
  - 后连**popsolver节点**
  - `creationframe`为创建粒子对象的时间，以帧为单位
  - `objectname`指定`popobject`的名称
  - `physical`选项
    - 控制物理参数，碰撞
    - `bounce`控制反弹
      - `bounce`为1为完全弹性碰撞
      - `bounce`大小=`popobject`中的`bounce`*`groundplane`中的`bounce`
    - `friction`控制摩擦
      - 原理同`bounce`
  
- **popforce节点**
  - 粒子不像体积由温度驱动，粒子由速度驱动，再没有初速度的情况下由力场驱动
  - 设置力场方向、噪波力场
  - 前接**popsource节点**/风场
  - 可指定组作为受力对象，支持组操作
  - `guide`可视化显示力场线
  - 纹理噪波力场
    - `swirlsize`控制纹理的频率，`amplitude`控制力场线在力场方向上的偏移程度
      - `swirlsize`越大，相当于频率越低，纹理越整体
      - `ampliitude`越大，偏移越强，力场线越偏移力场方向
    - `pulselength`控制纹理随时间动态变化
      - 为0，纹理不随时间动态变化
      - 为0到1的小数时，变化幅度大；越大，变化幅度越小
    - `turbulence`控制重复次数
  - `inputs`中调用`sop`路径的节点的数据
  - 作为叠加噪波力场
  - 支持`vex`
  
- **groundplane节点**
  - 添加碰撞地面
  - 与**popsolver节点**`merge`后，连到`output`
  - 显示`bug`，需要外部再解算节点的后面接**blast节点**删除
  
- **popwind节点**
  
  - 添加力场驱动粒子运动，类似`popforce`
  - `windvelocity`设置风场方向，`windspeed`设置风力大小
  - 前接**popsource节点**
  - 纹理噪波力场（与`popforce`类似）
    - `amplitude`控制在风场方向上力场线的偏移程度
  - 支持组操作
  - 支持`vex`
  
- **popdrag节点**
  - 添加阻力，不用设置方向
  - 前接`popwind`/popforce/力场类节点
  - 叠加的风场只需要有一个阻力
    - 风场的叠加：一个风场后连另一个风场
  
- **popwrangle节点**
  
  - 修改解算过程中的属性，每帧对每个粒子进行计算
    - `@v`
  - `vex`写法
    - 可以使用`@P`
    - 可以使用常用的函数
  - `inputs`选项中`input`设置为`sop`，读取外部的模型，外部的模型不必连到`popnetwork`输入端
    - 类似`sop`中的**wrangle节点**的辅助线输入端
    - 将`input1`设置为`myself`充当0号主线，`inputk`(k>1)设置为外部`sop`模型充当辅助线
      - `input1`设置为自身，相当于`attributewrangle`中的0号线，与`vex`中的地址一致
      - `inputk`为外部模型，相当于`attributewrangle`中的k-1号线，与`vex`中的地址一致
  
- **popcurveforce节点**
  - 指定外部的`sop`曲线作为管道风场
    - 粒子发射源一般被管道风场包裹
  - `soppath`指定外部曲线放样为外部风场
  - `individualforces`选项
    - 四种`ramp`图`falloff`：控制三种力以及速度在风场不同位置的值；横轴0代表风场的起始位置；横轴1代表风场的末尾位置
      - `followforce`为顺着曲线的推力
      - `suctionforce`为径向向内的吸引力
        - 吸引力大，粒子才不会飞出管道风场，使得局限在风场内
        - 一般设置为10，才能局限粒子
        - 以10为尺度
      - `orbitforce`为绕曲线的旋转力
      - `velocity`为速度的衰减
  - `maxinfluenceradius`控制管道风场半径
    - 半径不够大，粒子可能控制不住，飞出力场
    - 半径要足够大，使得粒子不飞出力场
  - `globalforces`选项
    - `globalforcefallofffromcurve`控制所有力的`ramp`图
      - 横轴为径向距离曲线的距离，为0最近，为1靠近管道外侧
      - 可以设置为从最靠近曲线的位置到管道外侧，力衰减
    - `forcealonglength`控制所有力的`ramp`图
      - 横轴为曲线位置，与`individualforces`选项中的`ramp`图的横轴相同

- **gravityforce节点**
  - 添加重力场
  - 前连**popsolver节点**/所有**solver节点**的**merge节点**
  - 类似**popdrag节点**

- **popreplicate节点**
  - 根据输入的粒子生成新的发射源，发射源随着粒子的运动而运动，是固定的
  - 后接风场节点/**popsolver节点**，前接风场节点即粒子
  - `shape`选项的`shape`设置为`sphere`为球状发射源，根据输入粒子在其附近生成球状发射源
    - `uniformscale`控制球状发射源的区域大小
    - 每个球状发射源内的发射点的个数受`impulsecount`影响
  - `shape`选项的`shape`设置为`point`时，新的发射源即输入粒子
  - `attributes`选项中控制新发射源发射的粒子继承该发射源发射点的属性以及设置速度
  - `birth`选项中设置发射速率和寿命
    - `impulsecount`控制发射源的每个发射点每帧发射的粒子数目
    - `constbirthrate`控制所有发射源的所有发射点每秒发射的粒子总数
  
- **popgroup节点**

  - 对粒子进行分组

  - 前接粒子

  - 勾选`enable`后在属性的`vex`框中进行分组

    ```c
    if(判断逻辑)
    {
        ingroup=1;                            //符合判断逻辑的粒子在组内
    }
    ```

  - `groupname`处命名组，即外部的分组属性名

  - 生成`dop`外部的`sop`中的分组属性和`stream`的分组属性

- **popcolor节点**

  - 赋予粒子颜色

  - `colortype`为`ramp`模式

    - 根据`ramp`图输出颜色

    - 默认横轴为`@nage`

    - 可在`vex`中自定义横轴含义

      ```c
      ramp=@属性名；
      ```

  - `colortype`为`random`模式

    - 根据种子赋予粒子颜色

    - VEX

      ```c
      seed+=@id
      ```

- **popkill节点**

  - 杀死粒子，立即结束粒子的生命周期

  - 前接粒子

  - `group`处指定组可以对指定的粒子组进行操作，支持组操作

  - 勾选`enable`后在属性的`vex`框中进行设置

    ```c
    if(判断逻辑)
    {
        dead=1;                              //杀死符合判断逻辑的粒子
    }
    ```

- **poplocation节点**

  - 添加一个点作为发射源

- **groundplane节点**
  - 添加地面作为碰撞被动刚体
  - 与**popsolver节点**`merge`在一起后，后连**output节点**
  - `physical`选项
    - `bounce`反弹
      - 反弹系数=地面的`bounce`*粒子的`bounce`
      - 反弹系数越接近1，反弹越接近完全弹性碰撞
    - `friction`摩擦
      - 越小，地面越光滑
  - `initialstate`选项
    - 控制地面的位置
  - `gridsize`控制地面大小

- **popproperty节点**
  - 设置粒子的属性，类似**popwrangle节点**
    - `uniformscale`设置`@pscale`
    - 勾选启用`bounce`，设置粒子的反弹系数
      - 值域是0到1
    - 勾选`friction`，设置粒子的摩擦系数
      - 值域是0到1
  - 前接粒子
  - 支持`vex`
  - 支持组操作

- **staticobject节点**

  - 读取外部的`sop`模型作为被动碰撞刚体
  - 勾选`usedeforminggeometry`将运动的模型参与解算
  - `collisions`选项
    - `rbdsolver`的`volume`中勾选`collisionguide`显示实际碰撞体
    - `rbdsolver`的`volume`中模式设置为`rayintersect`
      - `uniformdivisions`设置碰撞体的精度
        - 50常用，100、200较高
    - `rbdsolver`的`volume`中模式设置为`volumesample`
      - 主动碰撞体是烟雾时需要设置为`volumesample`模式并提高`uniformdivisions`精度
      - `proxyvolume`需要指定`sop`模型的`distance`体积

- **staticsolver节点**

  - 刚体解算节点
  - 和**popsolver节点**`merge`在一起后连到**output节点**
  - 前连**staticobject节点**/**groundplane节点**

- **popadvectbyvolumes节点**

  - 将外部体积的属性映射到粒子，不在外部体积范围内的粒子不受影响

  - `parameters`选项
    - `sop`处指定外部体积
    - `fieldname`指定体积的属性
      - 体积的`vel`速度属性

    - `advectiontype`设置映射方式
      - `updateforce`属性变为风场影响，间接映射
        - 更加自然
      - `updatevelocity`属性变为速度影响，直接影响
        - 更加贴近体积
      
    - `velocityblend`控制映射的紧密程度
      - 数值越大，粒子与体积的运动越拟合
      - 默认0.5
    
  - 前连粒子流，体积速度映射后，不需要再加力场驱动粒子运动

- **popspin节点**
  - 为粒子流的粒子添加旋转运动
  - 支持`vex`
  - 支持组操作
  - 会生成`@w`属性

- **popdragspin节点**
  - 为粒子流的粒子添加旋转运动
  - 支持`vex`
  - 支持组操作
  - 会生成`@w`属性

**dopnetwork节点**

- 用于自定义解算

- 属性
  - `simulation`选项
    - `scaletime`控制解算的快慢，最终解算结果的速度是原解算结果的`scaletime`倍

- `pyrosolver`（`sparse`）节点
  - 解算器
  - 后连**output节点**
  - `advanced`选项
    - `minsubsteps`和`maxsubsteps`
      - 设置每帧解算次数，使得解算不丢失小数帧
  - `simulation`选项
    - temperature
      - coolingrate
        - 降温速度
      - `ambienttemp`与`referencetemp`
        - 初始温度与最终温度
        - 温差越大，高度越高
      - buoyancyscale
        - 浮力大小
        - 浮力越大，升的越高
    - gravity
      - gravitydirection
        - 控制重力方向
        - 哪个轴向的值为-1，重力朝向哪个轴
    - advectionreflection
      - `advectionreflection`精度模式
        - 从`disable`到`double`-`project`解算精度从低到高，时间从快到慢
    - timescale
      - 设置解算快慢/烟雾升的快慢，相当于**timewarp节点**
    - calculatespeedfield
      - 勾选计算并记录速度属性
  - `shape`选项
    - disturbance
      - 影响速度场
      - 勾选并设置小的外形噪波
        - 勾选旁边的值越大，强度越大
      - `thresholdfield`设置噪波影响的属性场
        - 由于是边缘小噪波，一般设置为`density`
      - `thresholdrange`设置噪波的影响范围
        - 左阈值越大，噪波的影响范围越大
          - 噪波对属性场的属性值低于左阈值的属性才有效果
      - `baseblocksize`设置噪波基本单位大小
        - 一般设置为体素大小的3到5倍
    - turbulence
      - 影响速度场
      - 勾选并设置大的外形噪波
        - 勾选旁边的值越大，强度越大
      - `influencefield`控制噪波影响的属性场
        - 一般设置为`temperature`
          - 体积是由温度驱动的
      - `influencerange`的左阀值越小，噪波的影响范围越大
        - 噪波对属性场的属性值高于左阈值的属性才有效果
      - `swirlsize`设置噪波基本单位的大小
        - 越大，纹理表现越整体
        - 越小，纹理表现越细碎
      - `levels`设置纹理的叠加
        - 每层`level`将上一层噪波的`swirlsize`减半后叠加，可以增加细节
      - `grain`设置纹理的叠加
        - 配合`levels`一起使用，每层`level`的噪波的强度要在上一层噪波的强度的基础上乘`gain`值
      - controlsettings
        - 勾选`controlfield`并设置属性场/控制场
          - 设置噪波的控制场，实际决定了噪波的影响范围
          - 控制场属性值为0的地方，噪波也为0，控制场属性值为1的地方，噪波为其自身大小
          - 噪波大小=噪波自身大小*控制场属性大小
    - wind
      - 勾选并设置风场
        - 勾选旁边的值越大，风场的强度越大
        - `winddirection`控制风场的方向
          - 朝向哪个方向，哪个方向的分量为1
    - dissipation
      - 勾选并设置体素的寿命
        - 勾选旁边的值越大，体素的寿命越短
      - 不同精度的解算，想要寿命相等，需要调整值，值不同
  
- **volumesource节点**
  - 添加解算源
  - 后连`pyrosolver`（`sparse`）节点的第三个输入端
  - `input`和`soppath`指定外部的体积作为解算源
  - `volumes`选项
    - `fieldtomatch`将解算的场的属性赋给解算源体积
      - 默认为`density`属性，即外形
    - operations
      - 读取外部解算源体积的属性并映射到解算场中
      - `sourcevolume`即外部解算源体积的属性
        - `temperature`属性对应的`sourcevolume`即外部解算源体积的`temperature`
          - 必须通过`pyrosource`设置为1
        - density
        - 自定义速度属性
      - `targetfield`即映射到解算场的属性
        - `temperature`属性对应的`targetfield`即解算场的`temperature`
          - 默认为1
        - density
        - vel
      - `sourcescale`控制解算源体积的属性映射到解算场时对解算场的值是否缩放
      - `operation`控制映射后的解算方式
        - `density`属性为`add`模式
        - `temperature`属性为`pull`模式
          - 温度的实际值逐渐上升
          - `accelerationstrength`和`decelerationstrength`控制增减的快慢
        - `vel`属性为`add`/`pull`模式
        - `add`模式比`pull`模式增加的快
      - `fieldrank`控制属性的类型
        - `scalar`为浮点型
          - `density`、`temperature`
        - `vector`为向量型
          - vel
    - 勾选`enlargefieldstocontainsources`
      - 确保外部体积`transform`放大后，能识别到体积，不勾选不会被识别
  
- `smokeobject`（`sparse`）节点
  - 根据解算场生成体积
  - 后连`pyrosolver`（`sparse`）节点的第一个输入端
  - `guides`选项
    - `visualization`选项
      - 勾选`activeregion`显示体积区域
      - 勾选`multifield`显示`multifield`选项中的内容
      - 勾选`temperature`显示温度
  - `properties`选项
    - 勾选`maxsize`限制激活区域的大小
      - 超过激活区域的体积不会被显示
    - boundaryconditions
      - treatx/y/zasclosebelow/above
        - x/y/z轴上设置空气墙（与体积发生碰撞的被动刚体）
        - `below`时，对应轴上小于设定值的体积不会被显示
        - `above`时，对应轴上大于设定值的体积不会被显示，即空气墙位置
        - 和`maxsize`的区别是会发生碰撞
    - `voxelsize`设置体素大小
      - 要与外部体积的体素大小一致
  
- **gasfieldwrangle节点**

  - 修改解算过程中的属性场，每帧对每个体素单元进行计算

  - 前连**volumesource节点**，后连**pyrosolver节点**

  - VEX

    - 可以定义和修改的属性场

      ```c++
      v@vel;                                                //速度场；一般不直接赋值，否则会覆盖掉pyrosolver节点shape选项中的disturbance和turbulence的结果
      ```

  - `inputs`选项

    - 同**popwrangle节点**


**rbdbulletsolver节点**

- 第一个输入端是模型，第二个输入端是约束，第三个输入端是简模，第四个输入端是地面/被动碰撞刚体
  - 多个刚体都要输出动画时，需先用 merge 合并后再连入第一个输入端，因为`rbdbulletsolver`只从第一个输入端输出动画
- 前接**rbdconfigure节点**
- `collision`选项
  - `groundcollision`添加碰撞地面
- `visualization`选项
  - 勾选`showcollisionshape`显示碰撞刚体的外形
  - 勾选`showactive`显示参与碰撞的刚体
  - 勾选`ground`显示地面
  - 勾选`constraintgeometry`显示约束
    - 勾选`showguides`
  - 勾选`showgeometry`显示被动碰撞刚体
    - 当第四个输入端连`active`为0的刚体即被动碰撞刚体时，显示被动碰撞刚体
- `advanced`选项
  - `glue`设置胶水约束
    - 胶水约束相当于钢筋
    - `dataname`要与s`@constraint_name`一致
  - `soft`设置软约束
    - 软约束相当于钢筋的粘连效果
- `constraints`选项
  - `distancethreshold`控制`constraintnames`对应的约束的作用范围
    - 面与`constraintnames`对应的约束的面的间距超过这个阈值后，约束消失
  - `anglethreshold`控制`constraintnames`对应的约束的作用范围
    - 面与`constraintnames`对应的约束的面的角度超过这个阈值后，约束消失
  - 勾选`usevexsnippet`开启`vex`控制约束，针对于当前页的约束
    - `vex`中该约束对应的线编号是0
  - `vexsnippetsoppath`指定辅助`sop`对象
    - `vex`中对应的线编号是2
- 内层
  - **presolve节点**
    - 前连**popwrangle节点**
      - 可以修改解算中的属性的值


**popnetwork节点**（刚体）

- **rbdpackedobject节点**
  - `geometrysource`处导入外部已经设置解算属性的模型（带动画）
  - `initialobjecttype`设置为`createanimatedstaticobjects`或`deforming`
    - 减少解算量
  - `bulletdata`选项
    - `geometryrepresentation`设置为`concave`可提高解算精度，但耗时
    - 勾选`showguidegeometry`显示刚体轮廓
- **rigidbodysolver节点**
  - 输出解算结果
  - 前连**rbdpackedobject节点**，后连**output节点**


## 时间

**timeshift节点**

- 动画提取或冻结帧

- 读取前n帧的数据
- 属性视窗中的`frame`选项处写时间函数
  - `method`为by `frame`时，写帧数（如F-n代表往前推移n帧）
  - `method`为by `time`时，写时间（秒）
- `clamp`选项控制超出范围的帧的处理方式
  - clamp to `first`：使用第一帧的数据（切割头部效果）
  - clamp to `last`：使用最后一帧的数据（切割尾部效果）
- 例如：配合**transform节点**实现将某物体的动画往前推移n帧

**timewarp节点**

- 动画时间长度缩放，动画变速
- evaluationmode
  - `fitrange`模式
    - 设置输入和输出帧的范围

  - `byspeed`模式
    - 设置输入和输出帧的范围
    - `speed`处设置变速几倍

- `interpolation`补帧
  - 勾选`interpolatebetweeninputframes`和取消勾选`interpolaterotationofnormals`，`quaternions`，`andtransforms`使得动画流畅，动画插值

- `volumes`选项
  - 烟雾补帧
  - blendmode
    - `byvoxelposition`模式可以对变速后的烟雾解算补帧
    - `advected`模式可以对变速后的烟雾解算补帧
      - 需要`vel`属性场
      - 比`byvoxelposition`模式更加精确


**timeblend节点**

- 动画插值
  - 一般只能为粒子解算补帧，不能为烟雾解算补帧，烟雾补帧可能会出错
  - `rbd`解算结果`unpack`后可以该方式补帧
  
- 插值的间隔取决于时间轴的最小单位
  - 时间轴设置的`step`
- 输入时间和输出时间
- `interpolation`插值模式设置为`cubic`
- 为解算补帧，使得不丢失小数帧的细节

## vop

对应层级的属性想要在`vop`中操作最好用对应层级的**vop节点**

对应层级的`vop`实际是对对应层级`gs`视窗中的属性操作

材质类

- **materialbuilder节点**
  - principled shader **core节点**
    - 利用输入通道（例如自发光、透明、粗糙、颜色）结合`bind`导入的属性做处理
      - `bind`导入的属性既可以来自点层级，也可以来自面层级，都可以正确显示
  - compute **lighting节点**
    - 前接**principledshadercore节点**，后连**surface_output节点**
  - displacement `bound`选项
    - 涉及置换效果，必须添加并设置为1
    - editparameterinterface
  - 输入节点是**surface_globals节点**和**displacement_globals节点**
    - 前者关注表面，后者关注置换
    - 前者的法线关注于原本表面的法线，后者的法线关注于置换后表面的法线
    - **surface_globals节点**的I通道是以相机位置到模型表面的点的连线为方向的单位向量
  - 输出节点是**surface_output节点**和**displacement_output节点**
- pbrvolumephasefunction
  - 针对的是体积材质
  - 计算物体的`BSDF`，作用于`pbr`模式的F通道
  - `out_F`的`BSDF`+`pbrphase`的`BSDF`的结果输入到`surface_output`的F通道
  - 调节反射，`scattering` `phase`为负代表光被反向折射，为正代表光被正向折射，越靠近0物体越亮
- **computelighting节点**
  - 将`pbr`渲染模式下的`BSDF`类型的F通道转换为`raytracing`渲染模式下的`vector`类型的`Cf`通道
- **uvtriplanarproject节点**
  - 将路径对应的图片朝着x/y/z轴投射到物体表面，和`uv`无关，不计算`uv`属性
  - 每个轴向的`mark` `sharpness`控制边缘的锐度；`tint` with `color`辅助显示投射图片的位置；`angle`控制图片的旋转角度；`scale`控制图片的缩放
  - 相当于图片的**file节点**，把路径的贴图读入，方便后续处理
    - 一般不用其原本的功能，即沿坐标轴投射贴图到模型表面，而用其导入贴图的功能
    - 需要三个方向的路径都指定同一张照片，或者在正确的投射方向上指定
  - 不受相机移动的影响以及显示准确
    - P和N通道连接世界坐标系下的P和N
- **transform节点**
  - 将相机/世界坐标系转换到世界/相机坐标系
    - material **builder节点**中的置换输出节点的P通道只能识别相机坐标系
    - material **builder节点**中的输入是相机坐标系，输出也是相机坐标系
    - 对`material` **builder节点**中位置通道的置换需要先转换为世界坐标系，之后再转换为相机坐标系
      - 使得P不受相机移动的影响
  - `interpretation`处选择转换的属性，例如法线`normal`，位置`position`
- shading **normal节点**
  - 计算相机坐标系下的法线
  - 置换完之后要重新计算法线，使得渲染准确
  - 输入置换完的转为相机坐标系下的P，输出相机坐标系下的N
- **displace节点**
  - `displacementalongnormal`模式沿法线置换
    - 输入的是相机坐标系下的P和N，但是要注意`value`置换值要在世界坐标系下计算得到，使得纹理不随着相机移动而改变，置换效果不随着相机移动而改变
    - `offset`要改为0
    - 置换之后，输出的相机坐标系下的法线不是准确的，需要后连**shadingnormal节点**进行重新计算置换后的法线
- **restposition节点**
  - 相当于`bind`导入外部的`@rest`，`@rest`需要提前在`sop`中通过`sop`的**restposition节点**初始化
  - 无需连接输入，直接可以作为输出
    - `space`选择`world`世界坐标，即世界坐标系下的固定位置
    - `space`选择`camera`相机坐标，即相机坐标系下的固定位置
  - 只能通过该节点读取`sop`中的`@rest`
- **texture节点**
  - 用于读取贴图
  - 前连**uvcoords节点**读取模型的`uv`，后连**principledshadercore节点**的对应通道输出贴图
    - 贴图文件名与 Principled Shader Core 输入通道的对应关系：
      - **BaseColor** → `basecolor` 通道：直接连接，漫反射/基础颜色
      - **Normal** → `baseN` 通道：直接连接法线贴图
      - **Bump** → `baseN` 通道：需经过 Bump 节点处理后连接（与 Normal 二选一或叠加）
      - **Roughness** → `rough` 通道：直接连接，控制表面粗糙程度
      - **Gloss** → `rough` 通道：需反转（`Invert` 节点），因为 Gloss = 1 - Roughness
      - **Specular** → `reflect` 通道：控制反射/高光强度
      - **Cavity** → `basecolor` 通道（乘法修饰）：无直接对应通道，通常乘以 BaseColor 模拟缝隙阴影/`AO`效果
- **displacementtexture节点**
  - 可以导入法线贴图
    - 需要有`uv`属性，连接`uv`通道，一般配合**uvcoords节点**
    - 模式设置为`normal`
- **globalvariables节点**
  - 创建材质调用通道输入端
  - `contexttype`选择模式
  - 勾选`outputasinglevariable`只显示一个通道
    - 在下方选择显示的通道

**uvcoords节点**

- 将外部的`uv`属性（`vector`类型）读入`vop`，类似于`bind`

turbulent **noise节点**

- 生成浮点或者向量类型的噪波
- 可生成`anoise`、`onoise`等不同类型的噪波

multiply **constant节点**

- 乘法

**bindexport节点**

- 将属性导出`vop`外面
- 需要有输入，例如**const节点**

**bind节点**

- 将外部属性导入`vop`

**const节点**

- 创建常量

**subtract节点**

- 做减法

**add节点**

- 做加法

类型转换节点

- **vectortofloat节点**

**length节点**

- 计算向量的长度

**fitrange节点**

- 相当于`wrangle`中的`fit`函数

attribute **vop节点**

- 在`sop`中建立`vop`系统

**addconst节点**

- 加法

**setvectorcomponent节点**

- 将向量某一个分量变为0

**multiply节点**

- 乘法

**divide节点**

- 除法

**xyzdist节点**（不推荐，有`bug`）

- 实现`xyzdist`函数的功能
- input `geometry`可以是`sop`路径，`promote` `parameter`后在外面的`vop`属性中输入`sop`路径
- `maxdist`是最大搜索范围，超过搜索范围的距离会被映射为该值

**displacealongnormal节点**

- 沿法线置换
- 在材质`vop`中，P通道连接相机坐标系下的P

worley **noise节点**

- 生成`worley` noise
- 锐化边缘的方法
  - `dist2`减去`dist1`作为输出

**rand节点**

- 实现`rand`函数的功能

**rampparameter节点**

- 将颜色根据输入端作为横轴映射输出
- 在`vop`外部的属性中进行调整

**colormap节点**

- 将图片根据模型的`uv`贴到模型表面
- `cmap`指定图片的路径

**mix节点**

- 实现`lerp`函数的功能

**colorcorrection节点**

- 对颜色做调整
- 调整饱和度
  - 饱和度越高，颜色越饱满
  - 饱和度为0，黑白

**complement节点**

- 实现1-x

**parameter节点**

- 相当于`bind`，将外部属性导入`vop`
- 优于**bind节点**

importpoint/primitive/vertex/**detailattribute节点**

- 相当于`vex`中的属性调用函数`attrib`
- 外部辅助线连**vop节点**的第二个输入端，内部`input`设置为`secondinput`
- `attribute`和`signature`设置调用的属性名称和类型

**normalize节点**

- 将输入的向量单位化输出

**dotproduct节点**

- 计算两个向量的点乘
- 可以用来计算一个向量在另一个向量上的投影

**absolute节点**

- 输出输入的绝对值

**pow节点**

- 实现`pow`函数的功能

**flownoise节点**

- 生成连续的噪波纹理，由`flow`驱动噪波的动态变化
- `flow`连接`time`通道

**nearpoint节点**

- 实现`vex`中`nearpoint`函数的功能

**distance节点**

- 计算两个点/向量之间的距离，并输出

**setvectorcomponent节点**

- 设置向量某个分类的值
- 设置的值的通道必须在输入端中键手动添加`constant`

## 渲染

在节点视窗的最外层单击上方可以选择切换节点层或通道：
- `obj`：模型层
- `mat`：负责材质的通道，在此层添加`principledshader`可以添加材质
- 在**obj节点**层的模型节点后接**material节点**，在**material节点**属性视窗中选择路径为**mat节点**层的**principledshader节点**，即可载入材质贴图
- 在`gs`视窗查看物体的面级别中的`shop_materialpath`属性就会显示贴图材质的路径

一般使用**principledshader节点**的`surface`通道选项的`basecolor`导入贴图，一般与**texture节点**相连：
- **texture节点**属性视窗中的`texture` `map`选项可以指定贴图所在路径
- 若`uv`翻转，在**texture节点**属性视窗最下方选择`swap` u/v
- **principledshader节点**的所有通道一般都与**texture节点**相连
- 注意：材质贴图都是针对面级别的，`shop_materialpath`属性是`string`类型
- 在**aw节点**的`vex`编程框在面级别运行，也可通过代码添加材质，如：

  ```c
  if(@primnum<10){
      s@shop_materialpath="/mat/principledshader1";
  }
  ```

- 在**ps节点**属性视窗中可以调节材质参数（如玻璃、金属等）
- 在**ps节点**的`surface`通道连接的**texture节点**是渲染才能看到的；只有在**ps节点**属性视窗`textures`选项中`base` `color`添加的贴图才是直接可以看到的

单击左侧工具架的渲染按钮可渲染框选的区域

**mantra节点**

- 分层渲染
  - 一个**mantra节点**负责渲染一个层
  - 渲染被`forced` `matte`遮挡的`force` objects
    - 两个视觉上有遮挡关系的渲染对象，必须有且只有一个**mantra节点**中的`forcedmatte`和`forceobjects`指定了它们的遮挡关系
  - forceobjects
    - 只能选择外层的`geometry`
  - forcedmatte
    - 被动遮挡，不仅产生遮挡，还产生光影关系
    - 只能选择外层的`geometry`
    - 不会被渲染显示
  - forcedphantom
    - 无遮挡关系，只产生光影关系
    - 只能选择外层的`geometry`
    - 不会被渲染显示
  - 烟雾的分层渲染
    - 刚体的**mantra节点**
      - 刚体在`forceobjects`
      - 烟雾在`forcedphantom`，不在`forcedmatte`
        - 烟雾是透的，不能作为`matte`，否则背景刚体是虚的
    - 烟雾的**mantra节点**
      - 烟雾在`forceobjects`
      - 刚体在`forcedmatte`
  
- 渲染质量
  - `sampling`中的`pixel` `samples`以3为单位往上加，像素越高，渲染质量越高
    - 减少噪点
  - `sampling`中的`max` ray `samples`越高，渲染质量越高，噪点越少
  - `sampling`中的`noise` `level`越低，渲染质量越高
  - 体积类、透明材质
    - `sampling`中的`stochastic` `samples`越高，渲染质量越高
      - 一般设置为8，16
    - `sampling`中的`volume` step `rate`越低，渲染质量越高
- 输出图片带有的通道
  - 在`extra` image `planes`中勾选`shading` `depth`、`shading` `position`、`shading` normal
    - shading `depth`是相机坐标下的`@P`.z
    - shading `position`是相机坐标下的`@P`
    - shading `normal`是相机坐标系下的`@N`
  - 添加`extra` image `planes`通道，将勾选的属性输出
  - 保证随着相机的移动，能正确显示运动
    - **geometry节点**的`render`选项的`coving`处设置为`disable` coving
- 运动模糊
  - `rendering`选项中勾选`allowmotionblur`
    - 只有勾选，才会开启运动模糊效果
  - op:/下的运动对象所属的**geometry节点**的`render`选项下的`sampling`中设置`geometryvelocityblur`
    - 设置为`novelocityblur`时，影响运动模糊的只有**mantra节点**`rendering`选项中的采样次数和相机的快门时间
      - 适用于模型
      - 模型的速度过快时，需要调低相机的快门时间来避免失真
    - 设置为`velocityblur`时，影响运动模糊的只有速度属性和相机的快门时间
      - 适用于粒子
    - 设置为`accelerationblur`时，影响运动模糊的只有加速度属性和相机的快门时间
  - `shutteroffset`控制运动模糊
    - 为0代表对象沿着当前帧的速度方向产生运动模糊
    - 为1代表基于当前帧和下一帧的位置产生运动模糊
    - 为-1代表基于上一帧和当前帧的位置产生运动模糊
  - 相机的`shuttertime`快门时间
  - `geotimesamples`控制`op`:/下的**geometry节点**下的`sop`对象的运动模糊的采样次数
  - `xformtimesamples`控制`op`:/下的**geometry节点**的运动模糊的采样次数

**camera节点**

- focal `length`焦距
- 画面尺寸
- `sampling`选项中的`shuttertime`控制快门时长
  - 时长越长，运动模糊越明显

## 灯光

**light节点**

- `geometrylight`模式
  - 噪点
    - sampling `quality`参数调高降低噪点
  - 颜色
    - 通过`color`指定
  - 指定发光几何体及其材质
    - 在`area` light `option`中
    - 点不能作为物体光，线可以
      - Geometry Light 只能从有面积的表面采样发光，粒子（点）没有面积，所以实际的光源是实体模型，如果粒子和实体模型`merge`后作为`geometryobject`，看起来粒子好像是光源显得更亮，只是因为`Geometry` Light 把整个 geometryobject 标记为光源，该对象在渲染中直接显示为发光体外观，`Merge` 后粒子成为光源对象的一部分，所以视觉上显得明亮发光，但粒子（点）没有面积，不参与实际的光照采样，对场景的照明贡献仍然为零，真正照亮场景的还是实体模型部分
      - 只有粒子指定为`geometryobject`：无可采样面，`light`初始化失败
      - 粒子和模型`merge`后指定为`geometryobject`：有可采样面（模型），`light`初始化成功
        - 实体：采样发光，照亮场景
        - 粒子：视觉发光，但不参与采样
    - 指定模型而不要指定材质
      - Geometry Light 不依赖材质的`Emission` 来发光，它直接把几何体的表面当作面积光源来采样，发光颜色和强度由 Light 节点自身的参数控制（比如 `Color`、`Intensity`），和物体有没有材质、材质有没有 Emission 无关
- 渲染质量
  - sampling `quality`设置为2、4、8
    - 物体光设置为4、8
    - 普通灯光设置为2
- `sun`模式
  - distant light `options`中的`sun` `angle`越小，影子边界越锐利，反之影子边界越模糊

## 体积雾

**volume节点**

- `name`赋予名称，属于面层级
- 修改体素的个数`uniform` sampling `divs`和大小`size`
- 基于相机产生体积雾
- initial `value`设置为1使得体积雾可见

**blast节点**

- 删除体积雾

**name节点**

- 更改体积雾的`@name`属性，即`vdb`的名称
- 更改`density`属性场对应的`vdb`的名称后，如果无法显示，需要使用**volumevisualization节点**纠正错误，使得正常显示

**vdbfrompolygons节点**

- 将连接的`geometry`转换为体积雾，既可以是`fog`体积，也可以是`distance`体积
- 设置`@name`属性
- 控制体素大小，不能控制体素个数
- 当为`fog`体积雾模式时，内外界失效
  - 可实现相当于距离驱动的效果，转为`vdb`后，主线使用`volumesample`函数读取辅线`vdb`的`@density`，靠近0的地方距离远

- 只创建出体积雾，不能设置`@density`和`@surface`属性
- 当为`distance`体积雾时，内外界生效
- 会丢失`@uv`属性

**vdbactivate节点**

- 控制`vdb`体积的激活区域，第二个输入端作为参考

**attributefromvolume节点**

- 将体积的属性读到点云的点上

**volumewrangle节点**

- 专门针对体积的**vex节点**，其他**wrangle节点**不能操作体积
- 控制`fog`体积的`@density`属性、`distance`体积的`@surface`属性

**vdbvectorfromscaler节点**

- 将标量体积转换为矢量体积，需要连接三个标量体积合并后的体积
- 矢量体积的浓度属性仍然是f`@density`

**vdbvisualizetree节点**

- 可视化显示`vdb`的激活区域，不显示浓度

**vdbcombine节点**

- `vdb`运算节点，对两个体积进行运算，类似**groupcombine节点**
- 求交集，并集，布尔
- 匹配体素大小`voxelsize`

**convertvdb节点**

- 将体积转换为其他类型
  - `fog`转为`sdf`
    - `fogisovalue`控制缩放
- 将模型转为体积，再将体积转为`polygons`可解决原模型破面的问题
  - `fog`体积不能直接转为`polygon`，需要先转为`distance`体积，再从`distance`体积转为`polygon`

**vdbfromparticles节点**

- 将点云转换为体积（`fog`、`sdf`）
- 设置体素大小
- `pointradiusscale`控制粒子的`@pscale`的缩放，进而控制每个单元的大小
- `pointattributes`增加并选择从粒子继承到体积的属性

**vdbvisualization节点**

- 辅助查看浓度

**cloudnoise节点**

- 给`fog`体积的`@density`添加噪波，产生随机效果
- 要将`amplitude`强度调高才能看到效果
- 频率越小，纹理越细碎；频率越大，纹理越整体
- 会增加体素个数，不改变体素大小

材质类

- **volumeshadercore节点**
  - 设置自发光程度`emission` `scale`和自发光颜色`emission` color
    - 自发光是光源，既影响自身，也影响其他`geo`
  - 设置体积颜色`smoke` color
  - 渲染浓度`density` scale
  - **principledshadercore节点**无法渲染`fog`
- **computelighting节点**

**vdbsmooth节点**

- 属性插值/模糊，平滑属性，类似`attribute` **blur节点**

**vdbresample节点**

- 调整体素的大小`voxelsize`
  - using voxel size `only`模式

**pyrobakevolume节点**

- 为`vdb`体积赋予材质
  - 不需要再自定义材质赋予体积
  - 比**pyrosolver节点**自带的`look`更好
- 输入端是`vdb`
  - 可以是多个`vdb`经过`merge`后相连

- 前连`volume`体积/`vdb`体积
- `fire`选项
  - 勾选`fire`
  - `intensityscale`控制火焰亮度
    - 值越大，火焰越亮
      - 亮度=值*`intensityvolume`对应的`vdb`的值
  - `firecolorramp`调整火焰颜色
    - 横轴为将`colorvolume`的值对应的`vdb`的值映射为0到1之间后的值
    - 修改对应属性的颜色
- `bindings`选项
  - fire
    - `intensityvolume`为负责控制火焰亮度的`vdb`的名称，`colorvolume`为负责控制火焰颜色的`vdb`的名称
  - smoke
    - `smokevolume`为负责控制烟雾颜色的`vdb`的名称
- `smoke`选项
  - 取消勾选`smoke`不渲染烟雾，使得只渲染`fire`
  - `densityscale`设置渲染的`density`浓度
  


# 其他

可视化

- `marker`只能可视化@属性
- d键显示选项
  - `guides`中打开显示世界坐标轴
  - `geometry`中修改点的显示大小`pointsize`
    - 不是渲染大小，是显示大小
  - 关闭`hdri`灯光背景显示
- 在`sv`视窗单击d键打开`display` `options`选项菜单，在`visualize`选项中添加`marker`，设置`type`为`marker`/color/`vector`等显示属性
  - 在`marker`编辑菜单中将A值改为1，颜色就不会透明显示
- 在`gs`视窗可以单击属性右侧的小三角来根据大小对属性排序

节点拖入`pythonshell`获得节点的地址

数字资产（`Digital` `Asset`）

- 选择节点，在节点视窗上方工具栏中单击此按钮，创建`hda`数字资产，在创建完成后，打包节点会以资产的形式保存在指定路径中
- 想要应用的话，在节点视窗单击`tab`键，选择资产打开即可
- 右键单击数字资产节点，单击对应按钮可对资产的属性视窗增加一项属性，将左侧想要添加的属性中的参数按住拽到自定义中即可实现自定义属性面中属性参数的增加
- 不想要的话选择属性参数然后`delete`即可删除，完成后应用并确认即可
- 选择节点按p键快捷打开属性视窗

**aw节点**使用技巧

- 在**aw节点**属性视窗`vex`编程栏单击`alt`+e最大化显示编程栏
- 编程栏下方的按钮分别代表执行命令和执行命令并退出，一定要点击`accept`否则白做
- 在`vex`编程栏中选择函数单击`f1`打开说明帮助文档
- 单击`vex`编程框的此按钮，将所有函数变蓝或者正常显示
- 点亮`aw`编程栏的锁定按钮，锁定当前编程栏，即使切换到其他节点的属性视窗，还是当前**aw节点**的编程栏
- **aw节点**的本质是加属性

`sop`路径/地址的写法

- op:/第一层/第二层/.../**geometry节点**名
  - op:/obj/object/line1

函数中的输入端选项可以用`sop`地址替代，即输入端序号或者`sop`地址

`vop`和其他系统的数据通道

- bind
- bindexport
- promote parameter

`VEX`和`HScript`的数据通道

- `ch`变量滑块
- 直接引用`ch`通道

`HScript`的数据通道

- copy `parameter`与`paste`

`fog`体积雾/`vdb`体积雾与`pyro`解算

- 大小受体素大小和体素个数的共同影响

- 体素大小越小，渲染的`fog`体积的精度越高，一般体素大小设置为0.1为中档

- 一般`vdb`大小的三个分量控制在500，精度适中，1000高精度

- 进`dop`解算前必须在点云点层级（未生成体积前）定义的三种属性

  ```c++
  @pscale                                                     //控制体积单元的大小；一般在aw节点中控制，使用噪波增加随机度；必须比体素大小/精度大       
  @density                                                    //控制显示浓度；一般在aw节点中控制，使用噪波增加随机度  
  @temperature												//控制解算中温差的缩放；一般在aw节点中控制，使用噪波增加随机度；值越大，烟雾升的越高；为1时是标准温差，温差即为pyrosolver节点内部设置的温差；一般设置在0.k到1之间
  ```
  
- 进`dop`解算前根据需要在点云点层级（未生成体积前）定义的属性

  ```c++
  @v                                                          //控制烟雾蔓延速度方向及大小；可在aw节点中控制
  @divergence                                                 //控制烟雾从中心向四周膨胀的程度；值越大，越向四周膨胀；可在aw节点中控制；常用于爆炸效果；数量级是10
  ```

- 每个属性场对应一个`vdb`体积

  - `flame`属性场可以影响`temperature`属性场、`density`属性场、`divergence`属性（隐含的，热胀）、`vel`属性场

- `vdb`体积的名称

  ```c++
  @name                                                      //体积的名称；位于面层级；string类型
  0/1/...                                                    //体积的名称；volumewrangle中的函数中相当于@name；int类型
  ```

- 对`density`属性场对应的`vdb`体积使用**name节点**重命名后，如果无法显示
  - 使用**volumevisualization节点**显示重命名后的`density`属性场
  - 后续节点使用重命名后的`vdb`名称作为`density`属性场
    - **pyrobakevolume节点**

- 对`vdb`体积使用**name节点**改名后，对应`vdb`的属性场的名称也改为了**name节点**中指定的名称，而不是原来的名称例如`density`属性场`vdb`改名为`test`，`@density`改为了`@test`

- 烟雾解算的步骤

  - `pop`解算→`pyro`解算
- 不同精度的解算，参数不能套用，需要调整
- 设置解算的两种方法

  - **gasfieldwrangle节点**`vex`控制
  - 节点内置的噪波和内部的`gas`系列整体噪波节点
  
- `@pscale`决定体积单元的范围，`voxelsize`决定体积单元的精细度

  - $$
    \text{`@pscale`} \times 2 \quad (\text{直径}) > \text{voxelsize} \times 1.5
    $$


  - 只有当`@pscale`>voxelsize*0.75，体积单元才能正确渲染


`distance`体积雾

- `@surface`的值*体积的梯度=点到到模型表面的距离向量
  - 点的坐标加上该距离向量，点就会被投射到模型表面
  - `distance`体积雾内部的`@surface`为负，梯度为正，乘积结果也为负；外部的`@surface`为正，梯度为负；正确的乘积结果即距离向量应该为正，即`@surface`*体积的梯度的结果应该添个负号

线可以作为`geometry` `light`，点不可以作为`geometry` light

材质

- 透明可以自发光，但不能显示`base` color
- 对于`pbr`渲染，颜色是F通道，对于`raytracing`渲染，颜色是`Cf`通道
- `vex`中的颜色属性`Cd`属性不会被渲染
- 自发光
  - `Ce`是材质的自发光属性，在**mat节点**中设置
    - 可以通过**bindexport节点**将`Ce`属性导出，实现自发光照亮其他物体的效果，不改变模型自身的表面颜色，和**light节点**的物体光等价，并且不需要勾选**principledshadercore节点**中的`emissionilluminatesobjects`也可以照亮其他物体
  - 在**principledshadercore节点**中设置自发光颜色，勾选`emissionilluminatesobjects`后会照亮其他物体，前提是其他物体的材质节点中有**principledshadercore节点**
    - `emissioncolor`会改变表面颜色
  - 和**light节点**的物体光一样都可以照亮其他物体，两者可以一起使用
    - 和**light节点**的物体光相比，`light`是真正的物体发光源，可以调整光源的强度，而材质的自发光不可以调整发光强度
- 透明材质
  - `transparency`透明度
  - `ior`折射率
  - `roughness`粗糙度
    - 越接近0，透明效果越好

获取相机的位置的`vex`代码

```c++
4@camMatrix = optransform("op:/obj/cam1");         //获取相机的变换矩阵
v@camPos = cracktransform(0,0,0,0, @camMatrix);    // 提取相机位置
```

分层渲染

- 每个图层新建一个**mantra节点**渲染
- 注意遮挡关系
- 框选所有**mantra节点**并输出

噪波

- alligator noise
  - 鹅卵石样式
  - 频率（`vector`类型）越大，越细碎
  - 值介于0到0.5
- original perlin noise
  - 颜料盘样式
  - 频率（`vector`类型）越大，越细碎
  - 值介于-1到1
- worley noise
  - 蜂巢/细胞/岩石样式
  - 返回两个浮点数
  - 频率（`vector`类型）越大，越细碎
  - 值介于0到1之间
- flownoise
  - 连续的流动纹理
  - 值介于0到1之间


线的位置映射

- `@ptnum`/(`@numpt`-1)作为位置索引
  - 在除之前先将`@ptnum`和`@numpt`强转为`float`型，除法才能得到小数
  - 越靠近头（`@ptnum`小的一端），位置索引越小/越接近0
  - 越靠近尾（`@ptnum`大的一端），位置索引越大/越接近1

解算粒子的位置映射

- `@age`/`@life`作为位置索引
  - 越靠近0，越年轻，位置越靠下
  - 越靠近1，越老，位置越靠上

属性面板齿轮选择`edit` parameter `interface`设置`existing` `parameters`显示的参数

- 可以添加调色盘

时间轴设置

- 设置帧率
- 取消勾选`integerframevalues`，时间轴的最小单位变为小数帧
  - 不影响解算
- `step`设置时间轴的最小单位

`dop`系统中的虚线输入端

- 只输出节点的输出结果，不输出节点的输入结果

`dop`系统中的实线输入端

- 既输出节点的输出结果，又输出节点的输入结果

`dop`系统的`vex`与属性

- 节点的属性中有`rule`，勾选`enable`开启`vex`编程

- 属性
  - 在**popwrangle节点**的`vex`中是@+属性名的形式
  
  - 对于`dop`，有一些`HScript`的通道是可写的，在`VEX`框中直接对通道的`parameter`/属性做修改，直接写属性名（鼠标停留在通道上显示的名称）不用加@
  
    - `popwind`、`popkill`、`popgroup`、**popproperty节点**
  
    ```c
    通道名=语句;                         //对parameter做修改
    ```
  
  - 在虚线上定义的属性不会生效

粒子的`dop`系统影响粒子与刚体碰撞精度的因素

- 粒子大小`@pscale`
- `substeps`每帧解算次数

通过`file`菜单的`import`导入`fbx`模型

- 对于有动画的模型在最内层的`file`文件后接**deform节点**后，在`op`:/下新建一个**geometry节点**，内部用**objectmerge节点**读取

`fbx`与`abc`模型的区别

- `abc`高性能，轻量化，适合模拟和解算
- `fbx`包含的信息更多，包含骨骼动画

体积驱动粒子比粒子驱动体积更加真实

解算/动画补帧（不丢失小数帧，使得解算更加流畅）

- 粒子解算
  - **timeblend节点**插帧
  - **filecache节点**输出带小数帧的帧序列
- 烟雾解算
  - 提高**pyrosolver节点**的`substeps`每帧解算次数
  - **filecache节点**输出带小数帧的帧序列
- `rbd`解算
  - 解算结果`unpack`后使用**timeblend节点**插帧
  - **filecache节点**输出带小数帧的帧序列

- 普通动画
  - 使用**timeblend节点**插帧


`rbd`系列节点

- 输入输出端都有高模简模约束三条线

- 解算前和解算后都是`packed`

- 简模参与解算时，解算速度更快

- 约束属性的三要素

  ```c++
  s@constraint_name                                  //约束名
  @strength/@length                                  //约束强度
  @restlength                                        //约束线长度；后续随着解算的进行，约束线也变长，需要读取约束线的长度
  ```

- 生成约束（线）

  - **connectadjacentpieces节点**+**aw节点**面层级
    - 前者生成约束线，后者设置约束名和约束强度
  
- 模拟点云

  - 由**rbdbulletsolver节点**第四个输出端输出
  - 点的位置在不同的碎块的裂缝上
  - 可驱动碎块的解算，不用**rbdbulletsolver节点**，用**transformpieces节点**
  - 可连接**debrissource节点**生成激活点云
  
- `name`属性和分组属性都不能丢，否则会出错


快捷键

```c++
shift+s                                         //节点间的连线的线型
alt+左键                                         //理线
y+左键                                           //切断线
```


`dop`系统

- 内部不同的**solver节点**`merge`在一起之后后连**output节点**使得不同的**solver节点**相互作用
  - `popsolver`、`rigidbodysolver`、`staticsolver`、`pyrosolver`能相互产生碰撞
    - `merge`的顺序必须是刚体在最前面

向量跟随物体旋转

- 得到记录物体旋转的坐标系，即三维矩阵m

  - 三维矩阵m要能准确记录物体的运动，所选的轴向要与物体运动相关，例如法线和切线方向

  ```c++
  vector test*=m;                                                       //向量跟随物体旋转;旋转矩阵驱动        
  ```


右侧工具架的`displaygroupandattributelist`

- 将不同组或者属性不同的模型以不同的颜色区分高亮显示，并可选择对应的部分

单位

- 世界中一个小方格是一米

`pack`打包

- `pack`状态下的模型是`packedprimtives`类型，`packedprimtives`即将每个块打包为一个面
  - `packedprimtives`个数=点层级个数=面层级个数
  - 一个点/面对应一个`packedprimitive`/`pack`块
- 两种属性
  - s`@name`
    - 区分不同`packedprimitive`的依据是点层级的`@name`属性
    - 所有含打包功能的节点都会在打包后的模型的点层级中记录`@name`属性
  - v`@P`
    - 每个`@P`记录的是点对应的`pack`块的中心点的位置
- **wrangle节点**
  - 点层级/面层级的代码对整个`pack`块生效，且每个`pack`块执行一次
- for-**eachprimitives节点**
  - 由于一个面对应一个`pack`块，固可以使用该遍历节点对每个`pack`块操作

`vop`系统的节点

- 输入端中键添加`constant`或者`promote`

综合解算

- 设置刚体和烟雾的碰撞
  - pyrosolver+**collisionsource节点**
- 设置粒子和刚体的碰撞
  - `dop`内部的**merge节点**
    - 刚体在粒子之前

含有`ch`通道`copy`和`paste`关联的节点的复制

- 复制两个含有`ch`关联的节点，`ch`关联也会自动更新，不会发生冲突

`rbd`解算

- 最重要的是`@name`属性，要在面层级
- 先切割，再添加动画
- `rbd`碎块的低模不包含`interiordetail`

点云驱动

- `pointdeform`、`transformpieces`
- 比模型解算更加高效（解算速度快）

比例

- 一个单位是1米
- 解算中如果模型的比例过大，不会解算

解算的帧率

- 三同步
  - 时间轴的`step`、解算器的`substeps`、缓存节点的`substeps`
- substeps
  - 代表每秒解算几次
- 三种帧率的关系
  - 1s=substeps*step

坐标系

- 默认是左手坐标系，食指朝上是y轴，大拇指朝屏幕外是z轴，中指是x轴

`pop`系列节点

- `stream`属性中可以设置组名指定影响的粒子流
- 都支持组操作
- `VEX`中访问粒子本身的属性推荐用@属性名，在非**popwrangle节点**内，函数的输入端的0号代表的不是粒子流，而是外部的输入

材质`vop`（**materialbuilder节点**）的渲染管线

- 先置换，后着色
  - 置换阶段：相机坐标系P变形改变几何形状，顶点`uv`值不变
  - 着色阶段：对变形后的表面插值`uv`，用`uv`采样贴图，使得`principledshadercore`接收的贴图能正确贴到变形后的表面
