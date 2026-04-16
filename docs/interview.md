# 面试总结

最后更新时间：2026.4.11

---
# 1. 面试知识点
## 1.1 计算机基础/C#
### 1.1.1 内存

- <details><summary>栈和堆</summary>

    >- **什么是栈和堆？**
    >   - C# 中的 栈（Stack） 和 堆（Heap），本质上是两种不同的内存管理区域，它们在存储方式、生命周期、访问速度和管理方式上都有明显区别。
    >
    >   - **栈**:由操作系统分配的一块连续内存，用于存放 方法调用时的局部变量、参数和返回地址。它的本质是一种 先进后出（LIFO，Last In First Out） 的内存管理结构。
    >
    >   - **堆**:由操作系统管理的一块大内存区域，用于存放 对象和引用类型实例，由 垃圾回收器（GC）负责分配和回收。

    >- **区别对比**
    >
    >|  | 栈 | 堆 |
    >|-----|-----|-----|
    >| **储存内容** | 值类型(int,float,bool,struct等); 方法的参数和局部变量 | 引用类型(class, string, >object, 数组 等); 内部可能引用栈上的值(引用类型字段)|
    >| **分配方式** | 编译器自动分配，连续内存; 不需要垃圾回收 | new 关键字在堆上分配内存; 由 GC 自动回收|
    >| **访问速度** | 速度非常快，因为是连续内存、CPU 可以直接访问 | 相对慢，因为堆内存可能不连续，需要通过引用指针访问|
    >| **生命周期** | 随着方法调用结束自动释放 | 由 GC 决定，可能在方法退出之后仍然存在，直到没有引用|
    >| **大小** | 栈空间有限，通常几 MB，过深的递归可能导致 StackOverflowException | 通常远大于栈，受系统内存限制|
    >| **典型问题** | 栈溢出（StackOverflow） | 内存泄漏或频繁 GC|

    >- **总结**
    >   - 本质上都是内存区域：栈和堆都是 RAM 的一部分，只是管理方式不同。
    >   - 栈主要管理调用逻辑和小对象，堆管理大对象和动态对象。
    >   - 值类型和引用类型的差异：值类型一般在栈上分配，引用类型在堆上，栈上存储的是指向堆的引用。
    </details>
    

- <details><summary>值类型和引用类型</summary>

    >- **区别对比**
    >   - **值类型：**
    >       - 存储在栈上（或对象内嵌在堆上）
    >       - 直接存储数据
    >       - 赋值会复制整个值（深拷贝）
    >       - 生命周期短、内存小
    >   - **引用类型：**
    >       - 存储在堆上
    >       - 栈上存储 引用指针
    >       - 赋值是 引用拷贝（浅拷贝）
    >       - 生命周期由 GC 管理
    >   - **总结**
    >
    >       |  | 值类型 | 引用类型 |
    >       |-----|-----|-----|
    >       | **储存位置** | 栈 | 堆|
    >       | **赋值行为** | 复制(深拷贝) | 引用拷贝(浅拷贝)|
    >       | **默认值** | 值类型的零值 | null|
    >       | **类型** | int,float,bool,char,struct | class,string,object,数组|
    >       | **内存管理** | 由栈管理，生命周期短 | 由GC管理，生命周期长|
    >       | **性能** | 访问快，传递大对象由性能开销 | 访问慢，适合存储大对象和复杂结构|
  
    >- **适用场景**
    >   - **栈适用场景：**
    >       - 1.小数据量、简单数据:
    >           - int, float, double, bool, char
    >           - 自定义的小型 struct，如 Point { int x, y }
    >           - 优点：访问快，不用 GC
    >       - 2.不可变数据:
    >           - 值类型适合“只读或简单修改”的数据，避免共享状态引发副作用
    >       - 3.频繁创建和销毁:
    >           - 栈分配比堆分配快，值类型适合临时计算的对象
    >       - 注意事项：
    >           - 值类型过大（比如 >16~32 字节）可能造成 栈拷贝开销大
    >           - 避免在栈上存储过大结构（会导致性能下降）
    >   - **堆适合场景：**
    >       - 1.大对象：
    >           - 包括数组、类实例、复杂对象
    >           - 优点：避免大对象频繁拷贝
    >       - 2.需要共享状态
    >           - 多个变量引用同一个对象（比如集合、配置对象、缓存）
    >           - 修改对象在各处可见
    >       - 3.对象生命周期不确定或较长
    >           - 堆上的对象可以在方法退出后仍然存在
    >           - 比如缓存、全局数据、UI 控件等
    >       - 4.多态和继承
    >           - 引用类型支持类的继承、多态和接口实现
    >           - 值类型不支持继承（只能继承 System.ValueType）
    
    >- **综合适用建议**
    >
    >   | **需求/特性** | 值类型 | 引用类型 |
    >   |----------|----------|----------|
    >   | **数据量大小** | 小 (<16-32 字节) | 大，复杂对象|
    >   | **是否频繁创建/销毁** | 是 | 否，或管理复杂|
    >   |**是否需要共享/修改状态** | 否 | 是|
    >   | **生命周期** | 短 | 长 |
    >   | **是否需要继承/多态** | 否 | 是|
    >   | **性能考虑** | 栈快、拷贝开销小 | 堆慢、引用传递|
    
    </details>

- <details><summary>拆箱和装箱</summary>

  >- **1.装箱(Boxing)**：把值类型转换成引用类型，本质就是把栈上的值类型，复制一份到堆上，并返回一个引用
  >
  >     - **底层操作：**
  >         - 1.在堆上创建一个对象
  >         - 2.把值 拷贝到堆上
  >         - 3.在栈上 保存这个堆对象的地址
  >
  >- **2.拆箱(Unboxing)**：把引用类型还原成值类型
  >
  >     - **底层操作：**
  >         - 1.先检查对象是否真的是值类型
  >         - 2.从堆中取出值
  >         - 3.拷贝一份到栈上
  >
  </details>

### 1.1.2 GC

- <details><summary>管理范围</summary>
  
  >管理堆内存
  </details>

- <details><summary>GC卡顿本质</summary>
  
  >GC从 GC Root 出发进行可达性分析，标记所有仍然被引用的对象，未被引用的对象会被回收。
  >在这个过程中，需要遍历大量对象并进行标记和整理，而 Unity 的 GC 是 Stop-The-World，会暂停主线程执行，所以在回收期间会导致明显卡顿。

  > - **扩展1：什么是 GC Root？有哪些？**
  >     GC Root 是垃圾回收时的“起始引用集合”，GC 会从这些 Root 出发做可达性分析，遍历整个对象引用图。
  >     常见的 GC Root 包括：
  >
  >     - 栈上的局部变量（方法中的引用）
  >     - 静态变量
  >     - CPU 寄存器中的引用
  >     - 正在使用的对象（如正在执行的对象）
  >
  >     只要对象从 GC Root 可达，就不会被回收。

  > - **扩展2：为什么“遍历对象图”会很慢？**
  >     因为对象之间是引用关系，GC 需要做深度遍历（DFS/BFS），当对象数量很多时，这个遍历是 O(n) 的，并且涉及大量内存访问（cache miss），成本很高。

  > - **扩展3：为什么 Unity 的 GC 卡顿更明显？**
  >     因为 Unity 使用的是非增量 GC（或老版本 Mono GC），在 GC 过程中会 Stop-The-World，暂停主线程，导致一帧时间被完全占用，从而出现卡顿。
  </details>

- <details><summary>GC算法</summary>
  
  >标记-清除（Mark-Sweep）
  </details>

- <details><summary>怎么避免GC卡顿</summary>
  
  >- 1.减少 GC 触发
  >     - 对象池
  >     - 避免临时对象
  >     - 减少 LINQ / string 拼接
  >- 2.减少 GC 扫描量
  >     - 减少长期存活对象
  >     - 控制对象引用链复杂度
  >- 3.控制 GC 时机
  >     - 手动触发 GC（低负载时）：切换场景、后台加载、Loading中
  >     - 分帧处理（部分平台支持增量 GC）
  </details>

- <details><summary>Mono GC vs IL2CPP GC</summary>

  >- **1.Mono GC**：Mono 使用的是：Boehm GC
  >   - 特点：
  >     - 非分代
  >     - 非压缩
  >     - Stop-The-World
  >     - 保守GC（Conservative GC）
  >   - 执行流程：
  >     Stop The World
  >     → 扫描所有内存
  >     → 标记引用
  >     → 清除未引用
  >   - 问题：
  >     扫描范围大 → 卡顿明显

  >- **2.IL2CPP**：本质仍然使用的是：Boehm GC
  >   - IL2CPP 的运行流程:
  >     ```
  >     C#代码
  >     ↓
  >     IL
  >     ↓
  >     IL2CPP转换
  >     ↓
  >     C++代码
  >     ↓
  >     编译成本地机器码
  >     ```
  >   - IL2CPP GC的变化:
  >     - 对象布局更清晰：因为AOT编译（静态类型）→ GC 能更容易识别引用
  >     - 扫描效率更高：Mono是解释执行（JIT），IL2CPP是原生代码，扫描更快
  >     - 更少额外分配：Mono JIT 可能产生临时对象、运行时结构；IL2CPP提前编译好，GC压力更小
  >   - 问题：
  >     虽然更快，但：
  >       - 仍然不是分代GC
  >       - 仍然不是压缩GC
  >       - 仍然会 STW（卡顿）
  >
  >     所以：GC卡顿仍然存在（只是更少）

  >-  Mono GC VS IL2CPP GC 对比
    >
    >       | 对比 | Mono | IL2CPP |
    >       |-----|-----|-----|
    >       | **GC类型** | Boehm | Boehm|
    >       | **执行方式** | JIT | AOT|
    >       | **分代** | ❌ | ❌|
    >       | **压缩** | ❌ | ❌|
    >       | **性能** | 较慢 | 更快|
    >       | **GC频率** | 更高 | 更低|
    >       | **卡顿** | 更明显 | 相对较少|

  >-  **扩展**
  > - 分代GC(Generational GC)：.Net GC，更高级，更高效，更成熟
  > - 增量GC(Incremental GC)：unity2019后引入的，可以分帧执行，减少卡顿
  </details>

### 1.1.3 GC优化

### 1.1.4 位/进制
- <details><summary>储存单位</summary>

  > - **bit(位)**：计算机中 最小的数据单位，只有两种状态：0或1

  > - **byte(字节，B)**：1 byte = 8 bit，2^8 = 256中可能，所以 1 Byte 可以表示 256 个不同的数值（0~255）。
  > 由于CPU按字节寻址，所以CPU最小寻址单位是1byte

  > - **内存单位换算**：
  >   1B = 8bit
  >   1KB = 1024B
  >   1MB = 1024KB
  >   1GB = 1024MB

  > - **常见数据类型大小**：
  >   char:16bit = 2byte
  >   int:32bit = 4byte
  >   float:32bit = 4byte
  >   bool:8bit = 1byte，逻辑上需要1bit就够(0/1)，但是cpu最小寻址单位是byte;
  </details>

- <details><summary>二进制</summary>
  
  > - 定义
  >   0和1，满2进位

  > - 转 十进制
  >   2 ^ n，n为1所对应的索引，从右到左数
  >   例如：0101
  >    ```
  >    2 ^ 2 + 2 ^ 0
  >    ```
  </details>

- <details><summary>十六进制</summary>
  
  > - 定义
  >   16进制使用 **0–9** + **A–F** 表示：
  >   | 十进制 | 16进制 |
  >   |---|---|
  >   | 10 | A |
  >   | 11 | B |
  >   | 12 | C |
  >   | 13 | D |
  >   | 14 | E |
  >   | 15 | F |

  > - 转 十进制
  >   C#使用`0x`前缀表示16进制，后面每位数字对应4bit例如：
  >    ```
  >    0x10：0001 0000 -> 2^4 = 16
  >    ---------------------------
  >    16进制    -> 0x   1      0
  >    ↓
  >    转换2进制 ->     0001   0000
  >    ↓
  >    位索引    ->     7654   3210
  >    ↓
  >    转换10进制->     2^4 = 16
  >    ```
  >
  </details>
- <details><summary>二进制储存负数/小数原理</summary>
  
  > - 负数/补码：
  >   计算机中的负int(c#)使用的是 补码（Two’s Complement）表示法
  >
  >   - <details><summary>二进制 转 十进制</summary>
  >
  >     1. 规则如下：
  >         - 看最高位（1 → 负数）
  >         - 从右往左找到第一个 1
  >         - 这个 1 右边不变，左边全部取反
  >
  >         例如：-5
  >         ```
  >         11111111 11111111 11111111 11111011
  >         ↓
  >         第一位是1，表示负数
  >         ↓
  >         右数第一位为1的数(右1)
  >         ↓
  >         取反其他所有位 -> 00000000 00000000 00000000 00000101 -> 2^2 + 2^0 = 5
  >         ↓
  >         按最高位取符号，即1为负数 -> -5
  >         ```
  >     2. 特殊情况：
  >         ```
  >         10000000 00000000 00000000 00000000
  >         因为它没有“正数对应值”，没有正数对应值的情况 = MinValue
  >         实质是补码把“负0的位置”让给了最小值（MinValue）
  >         ```
  >     3. 面试题：
  >         ```
  >         int x = int.MinValue;
  >         int y = -x;
  >         Console.WriteLine(y == x); // true
  >         ```
  >         因为：
  >         ```
  >         -(-2^31) = 2^31（溢出）
  >         → 回绕 → -2^31
  >         ```
  >     4. 移位：
  >         - 右移
  >         C# 中有两种“右移”语义（虽然语法上只有一种）：
  >           ```
  >           >>   // 算术右移（Arithmetic Shift）
  >           >>>  // 逻辑右移（C# 11+ 才有，了解即可）
  >           ```
  >           1. 算术右移（>>）：保留符号位
  >             例如：
  >               ```
  >               int a = -1;
  >               int b = a >> 1;
  >
  >               二进制：
  >               a = 11111111 11111111 11111111 11111111
  >               右移1位：
  >               b = 11111111 11111111 11111111 11111111
  >               结果：
  >               b = -1
  >               ```
  >               结论：负数右移会“补1”
    >           2. 逻辑右移（>>>）：不管正负，一律补0
  >             例如：
  >               ```
  >               int a = -1;
  >               int b = a >>> 1;
  >
  >               11111111 → 01111111
  >               结果：
  >               b = 2147483647
  >               ```
  >               结论：逻辑右移 = 当作无符号数处理
  >
  >         - 右移重点：
  >         右移不是“除2”
  >         对负数来说是 向下取整（floor）
  >         例如：
  >           ```
  >           -5 >> 1 = ?
  >           很多人会说：= -2（错）
  >           正确：= -3
  >           因为：-5 / 2 = -2.5
  >           向下取整：= -3
  >           ```
  >
  >         - 左移的本质（简单但容易忽略）
  >           ```
  >           int x = 1 << n;
  >           等价于：x = 2^n
  >           但有坑！
  >           int x = 1 << 31;
  >           = 10000000 00000000 00000000 00000000
  >           = -2^31
  >           溢出成负数
  >           ```
  >
  >     5. 超级常见技巧（Unity / 底层常用）:获取符号位
  >         
  >         ```
  >         int sign = x >> 31;
  >
  >         0:正数
  >         1:负数
  >         ```
  >
  >           非常常见于：
  >           - 数学优化
  >           - SIMD / Burst
  >           - 无分支代码
  >
  >     6. 终极理解（非常重要）：
  >       要建立一个直觉：位移 ≠ 数学运算
  >
  >         | 操作 | 本质 |
  >         |---|---|
  >         | << | 位搬移 |
  >         | >> | 位搬移（带符号扩展） |
  >     </details>
  >
  >   - <details><summary>十进制 转 二进制</summary>
  >
  >     1. 规则：负数补码 = 正数 → 从右往左：保留第一个1，左边全部取反
  >
  >       例如：-5
  >       ```
  >       写出5的二进制：00000000 00000000 00000000 00000101
  >       ↓
  >       从右往左找第一个1：0000010'1'
  >       ↓
  >       这个 1 右边不变（包括它）,左边全部取反：00000101 -> 11111011
  >       ↓
  >       11111111 11111111 11111111 11111011
  >       ```
  >     </details>

  > - 小数
  </details>
- <details><summary>位操作</summary></details>
- <details><summary>十六进制/十进制/二进制对比</summary></details>

### 1.1.5 数据结构
- <details><summary>常用数据结构</summary></details>
- <details><summary>list:本质&原理/移除元素有哪些操作/复杂度</summary></details>
- <details><summary>linklist:合并/反转</summary></details>
- <details><summary>Dictionary:底层原理/字典的问题(哈希冲突/扩容问题)</summary></details>
- <details><summary>树遍历</summary>

     - 先序遍历：由左向右遍历

     - 中序遍历：左节点->跟节点->右节点

     - 后序遍历：跟节点->左节点->右节点
     
     - 实践
    </details>

### 1.1.6 算法

## 1.2 Unity
### 1.2.1 基础
- <details><summary>Mono生命周期函数</summary></details>

### 1.2.2 UGUI
- <details><summary>源码</summary>
  
  - Graphic
    - 生成Mesh
    - 脏标记系统(SetDirty)
      - SetVerticeDirty()
        - vertice
        - uv
        - color
        - triangle
      - SetMaterialDirty()
      - SetLayoutDirty()
  - Canvas
  - CanvasUpdateRegistroy
  - Layout
  - CanvasRender
  - EventSystem
  </details>
- <details><summary>渲染流程</summary></details>
- <details><summary>与粒子系统/3D物体一起使用的问题(层级关系/遮挡等)</summary></details>
- <details><summary>UIParticle原理</summary></details>
- <details><summary>Text/TMP区别</summary>
  
  - Text（旧版）
    - 特点：
      - 每次改 text → 重新生成 Mesh
      - 实现简单 → CPU 较低
      - 但：
      - ❌ GC 多（string、builder等）
      - ❌ 不可控

  - TMP
    - 特点：
      - Mesh 生成更复杂
      - 但：
      - ✅ 使用缓存数组（减少 GC）
      - ❌ CPU 更重（排版复杂）
  
  - 核心区别：
    - Text：简单粗暴（便宜但不稳定）
    - TMP：复杂可控（稳定但更贵）

  - 排行榜场景下谁更好？
    - 答案是：大多数情况下还是 TMP 更好
    - 虽然 TMP 更贵，但：
  
    - ❌ Text 的问题：
      - GC 抖动（更致命）
      - 字体模糊
      - 控制弱

    - ✅ TMP 的优势：
      - 可控（你可以优化）
      - 不容易 GC 抖动
      - 表现稳定
  </details>
- <details><summary>Mask/Rect2DMask</summary></details>
- <details><summary>UGUI优化(Rebuild/合批/Layout/GC)</summary></details>
- <details><summary>实际问题：ScrollView优化</summary></details>

### 1.2.3 资源管理
- <details><summary>AB</summary></details>
- <details><summary>Addressable</summary></details>
- <details><summary>资源管理设计目的</summary></details>
- <details><summary>资源管理主要实现的功能</summary></details>
- <details><summary>如何设计</summary></details>
- <details><summary>卡顿优化</summary></details>
- <details><summary>实际问题：打包规则/资源冗余</summary></details>

### 1.2.4 热更新
- <details><summary>Lua / HybridCLR(原理)</summary></details>
- <details><summary>IL2CPP 限制</summary></details>

### 1.2.4 动画系统
- <details><summary>Animator（状态机）</summary></details>
- <details><summary>性能问题（频繁切状态）</summary></details>

## 1.3 Lua
### 1.3.1 基础
- <details><summary>pair/ipair</summary></details>
- <details><summary>require/dofile/loadfile</summary></details>
- <details><summary>闭包</summary></details>
- <details><summary>元表/元方法</summary></details>
- <details><summary>面向对象的实现</summary></details>

### 1.3.2 底层/源码

### 1.3.3 内存
- <details><summary>变量存在的形式(在cpu中以什么形式存在)</summary></details>
- <details><summary>GC</summary></details>
- <details><summary>优化</summary></details>

### 1.3.3 实际问题
- <details><summary>nil/null</summary></details>
- <details><summary>网易面试</summary></details>
- <details><summary>私有化变量</summary></details>


## 1.4 项目经验/技术栈
### 1.4.1 事件和委托
- <details><summary>定义/区别/适用场景</summary></details>

### 1.4.2 状态机和行为树

### 1.4.3 递归函数

### 1.4.4 async/await和协程
- <details><summary>定义</summary></details>
- <details><summary>协程实现</summary></details>
- <details><summary>UniTask</summary></details>

### 1.4.5 对象池

### 1.4.6 同步方式

### 1.4.7 UI框架
- <details><summary>MVC</summary></details>
- <details><summary>MVP</summary></details>
- <details><summary>MVVM</summary></details>

### ECS

### 1.4.8 网络
- <details><summary>TCP/UDP</summary></details>
- <details><summary>长短链接</summary></details>
- <details><summary>Proto(定义/优点)</summary></details>

### 1.4.9 设计模式
- <details><summary>观察者模式</summary></details>
- <details><summary>工厂</summary></details>
- <details><summary>适配器</summary></details>

## 1.5 优化

### 1.5.1 代码优化
### 1.5.2 *UGUI优化
### 1.5.3 渲染优化
### 1.5.4 内存优化
### 1.5.5 *GC优化



## 1.6 高频和经典问题
LINQ 为什么慢？
什么是 GC Root？
CPU寄存器中的引用？
lua中的变量在cpu中的储存形式？
Mask原理，为什么会打断合批？

### 1.6.1 回答技巧/模板总结
><details><summary>技巧</summary>
>回答套路
>
> - 原理
> 
> - 问题
> 
> - 解决发难
> 
> - 项目实践
</details>

><details><summary>模板</summary>我主要从几个方面做过优化：
>
>- 代码层：减少 GC、对象池
>- UI 层：动静分离、减少 Canvas rebuild
>- 渲染层：图集合批、减少 DrawCall
>- 资源层：Addressables 管理资源
>- 在项目中比如 ScrollView 列表优化，成功降低了卡顿和内存占用
</details>


# 2.面试
## 2.1 2026.3-4
- 知行远科技
- 易娱
- 迦游
- 欢乐杨帆
- tap4fun
- 作业帮
- 啊哈
- 游兔
- 深圳禅游
- 极客创游
- 创优天地
- 紫龙
- 掌趣
- Magic Tavern(麦吉太文)
- 畅聊天下
- 儒意景秀
- 游酷盛世
- 游卡
- 莉莉丝
- 瑞尼科斯
- 天天玩家
- 贝塔
- 鲸甲
- 途游
- 永航
- 祖龙
- IM30(龙创)
- FanPlus(趣家)

## 2.2 2024.10-11