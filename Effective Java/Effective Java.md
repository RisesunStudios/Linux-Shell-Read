# Effective Java 读书笔记

- [ ] 书籍作者: [Joshua Bloch](https://book.douban.com/author/196022/)

- [ ] 笔记时间: 2020.12.15

## 第 1 章 引言

一共90条,每条讨论一个规则

交代新版本更新内容和约定

## 第 2 章 创建和销毁对象  

### 【01】用静态工厂方法代替构造器  

类可以提供一个公有的静态工厂方法（ static factory method ），它只是一个返回类的实例的静态方法 (不是为了对应设计模式的工厂方法)

优势:

1. 静态工厂方法与构造器不同的第一大优势在于，它们有名称。  

2. 静态工厂方法与构造器不同的第二大优势在于，不必在每次调用它们的时候都创建一个新对象  (单例,缓存等,总能严格控制某个时刻实例存在的类称为 实例受控的类)

3. 静态工厂方法与构造器不同的第三大优势在于，它们可以返回原返回类型的任何子类型的对象  (API可以返回对象,技术适用于 基于接口的框架) 因此一般没有任何理由给接口提供一个不可实例化的伴生类 

4. 静态工厂的第四大优势在于，所返回的对象的类可以随着每次调用而发生变化，这取决于静态工厂方法的参数值 。

5. 静态工厂的第五大优势在于，方法返回的对象所属的类，在编写包含该静态工厂方法的类时可以不存在 。这种灵活的静态工厂方法构成了服务提供者框架的基础，例如 JDBC(        

   > 服务提供者框架有三个重要的组件:
   >
   > - 服务接口:这是提供者实现的  
   > - 服务访问API:这是客户端用来获取服务的实例   
   > - 提供者注册API: 这是提供者用来注册实现的  
   > - 第四个组件服务提供者接口是可选的，它表示产生服务接口之实例的工厂对象  

劣势:

1. 类如果不含公有的或者受保护的构造器，就不能被子类化  
2. 程序员很难发现它们  

静态工厂方法的一些惯用名称  :

1. from : 类型转换方法，它只有单个参数，返回该类型的一个相对应的实例
2. of : 聚合方法，带有多个参数，返回该类型的一个实例  
3. valueOf : 比 from 和 of 更烦琐的一种替代方法  
4. instance 或者 get Instance : 返回的实例是通过方法的（如有）参数来描述的，但是不能说与参数具有 同样的值  
5. create 或者 newInstance : instance 或者 getinstaηce 一样，但 create 或者 new instance 能够确保每次调用都返回一个新的实例 
6. get Type : 像 getInstance 一样，但是在工厂方法处于不同的类中的时候使用
7. newType :  像newInstance 一样，但是在工厂方法处于不同的类中的时候使用  
8. type : getType 和 newType 的简版  

切忌第一反应就是提供公有的构造器， 而不先考虑静态工厂  

### 【02】遇到多个构造器参数时要考虑使用构建器  

静态工厂和构造器有个共同的局限性：它们都不能很好地扩展到大量的可选参数 。 比如用一个类表示包装食品外面显示的营养成分标签    

- 习惯采用 重叠构造器 ,参数少的调用参数长的,这样很糟糕,阅读性差

  简而言之，重叠构造器模式可行，但是当有许多参数的时候，客户端代码会很难缩写，并且仍然较难以阅读 。  

- JavaBeans模式,在构造过程中可能处于不一致的状态 并且 使得把
  类做成不可变的可能性不复存在   

- 建造者模式 :Builder模式模拟了具名的可选参数Builder 模式也适用于类层次结构 。  与构造器相比， builder 的微略优势在于，它可 以有 多个可 变（ varargs ） 参数 。 因为builder 是利用单独的方法来设置每一个参数 。  

Builder 模式的确也有它自身的不足 。 为了创建对象 ，必须先创建它 的构建器 。 虽然创建这个构建器的开销在实践中可能不那么明显 但是在某些十分注重性能的情况下，可能就成问题了   

简而言之 ， 如果类的构造器或者静态工厂中具有多个参数，设计这种类时， Builder模式就是一种不错的选择， 特别是当大多数参数都是可选或者类型相同的时候 。  

### 【03】用私有构造器或者枚举类型强化 Singleton 属性  

Singleton 通常被用来代表一个无状态的对象，如函数  使类成为 Singleton会使它的客户端测试变得十分困难  

通常有两种方法:

1. 公有静态成员是个 final 域  

   > 可以通过反射进行调用构造器,修改构造器,让第二次创建抛出异常

2. 静态工厂方法

   公有域方法的主要优势在于， API 很清楚地表明了这个类是一个 Singleton ： 公有的静态域是 final 的，所以该域总是包含相同的对象引用 。 第二个优势在于它更简单。  

   > 优势1: 在不改变其 API 的前提下 ， 我们可以改变该类是否应该为 Singleton 的想法  
   >
   > 优势2: 如果应用程序需要，可以编写一个泛型 Singleton 工厂  
   >
   > 优势3: 可以通过方法引用（ method reference）作为提供者，比如Elvis : : instance 就是一个 Supplier\<Elvis\> 。  

为了Singleton可序列化,必须声明所有实例域都是transitient,并提供一个readResolve(),否则反序列化都会创造一个新的实例(反序列化不会调用构造器)

3. 使用enum 更加简洁，无偿地提供了序列化机制,单元素的枚举类型经常成为实现 Singleton 的最佳方法  (如果需要扩展一个基类,则不宜使用)  

### 【04】通过私有构造器强化不可实例化的能力  

工具 类不希望被实例化，因为实例化对它没有任何意义   (比如Math,Arrays等)

企图通过将类做成抽象类来强制该类不可被实例化是行不通的 。(会误导用户以为可以继承)  

私有化构造器避免调用,记得加注释

副作用，它使得一个类不能被子类化 。 所有的构造器都必须显式或隐式地调用超类（ superclass ）构造器  

> 直接用接口会不会好一点,毕竟Java8可以在接口里面实现默认方法,静态方法(第一条,优势)

### 【05】优先考虑依赖注入来引用资源  

静态工具类和 Singleton 类不适合于需要引用底层资源的类 。 (能够支持类的多个实例 ,，每一个实例都使用客户端指定的资源   ) 

满足该需求的最简单的模式是， 当创建一个新的实例时， 就将该资源传到构造器中 。这是依赖注入（ dependency injection ）的一种形式     

依赖注入的对象资源具有不可变性，因此多个客户端可以共享依赖对象（假设客户端们想要的是同一个底层资源） 。 依赖注入也同样适用于构造
器、静态工厂和构建器 。    

另一个有用的变体,将资源工厂（ factory ）传给构造器  ,Supplier接口类型可以实现.

虽然依赖注人极大地提升了灵活性和可测试性，但它会导致大型项目凌乱不堪，因为它通常包含上千个依赖 。一个依赖注入框架(Spring,Dagger或Guice)可以解决

### 【06】避免创建不必要的对象  

重用方式既快速，又流行 。 如果对象是不可变的（ immutable ) ，它就始终可以被重用 。  

比如

```java
String s = "bikini";// ok
String s = new String("bikini"); // Don't do this
```

对于同时提供了静态工厂方法 （ static factory method)和构造器的不可变类，通常优先使用静态工厂方法而不是构造器，以避免创建不必要的对象。  

建议将一些创建成本高的建议缓存起来,比如Integer.valueOf就有

如果一个对象是不变的，那么它显然能够被安全地重用，但其他有些情形则并不总是这么明显 。考虑适配器（ adapter）的情形,有时也叫作视图 。   

> Map 接口 的 keySet 方法返回该 Map 对象的 Set 视图，其 中包含该 Map 中所有的键（ key ）, 实际上每次调用 keySe t 都返回同样的 Set 实例,虽然创建 key Set 视图对象的多个实例并无害处， 却是没有必要，也没有好处的  

另一种情况是 自动装箱.自动装箱使得基本类型和装箱基本类型之间的差别变得模糊起来， 但是并没有完全消除 。  

结论很明显 ： 要优先使用基本类型而不是装箱基本类型，要当心无意识的自动装箱。不要错误地认为本条目所介绍的内容暗示着“创建对象的代价非常昂贵，我们应该要尽可能地避免创建对象” 。相反，由于小对象的构造器只做很少量的显式工作，所以小对象的创建和回收动作是非常廉价的，特别是在现代的 JVM 实现上更是如此 。       

反之，通过维护自己的对象池来避免创建对象并不是一种好的做法，除非池中的对象是非常重量级的 。 正确使用对象池的典型对象示例就是数据库连接池 。   

### 【07】消除过期的对象引用  

把过期的引用置为null,让垃圾处理器可以回收.

清空过期引用的另一个好处是，如果它们以后又被错误地解除引用，程序就会立即抛出 NullPointerException 异常  

清空对象引用应该是一种**例外** ， 而不是一种规范行为 。 消除过期引用最好的方法是让包含该引用的变量结束其生命周期 。   

一般来说 ， 只要类是自己管理内存，程序员就应该警惕内存泄漏问题 。 一旦元素被释放掉，则该元素中包含的任何对象引用都应该被清空 。

内存泄漏的另一个常见来源是缓存。 一旦你把对象引用放到缓存中，它就很容易被遗忘掉，从而使得它不再有用之后很长一段时间内仍然留在缓存中 。(可以用 WeakHashMap 代表缓存  )    

内存泄漏的第三个常见来源是监昕器和其他回调 。 如果你实现了一个 API，客户端在这个 API 中注册回调，却没有显式地取消注册，那么除非你采取某些动作，否则它们就会不断地堆积起来 。 确保回调立即被当作垃圾回收的最佳方法是只保存它们的弱引用  

### 【08】避免使用终结方法和清除方法  

终结方法 通常是不可预测的，也是很危险的，一般情况下是不必要的 。 使用终结方法会导致行为不稳定 、 性能降低，以及可移植性问题。   

清除方法没有终结方法那么危险，但仍然是不可预测、运行缓慢，一般情况下也是不必要的 。  

两者的缺点在于不能保证会被及时执行。 从一个对象变得不可到达开始，到它的终结方法被执行，所花费的这段时间是任意长的   

结论是： 永远不应该依赖终结方法或者清除方法来更新重要的持久状态。   (清除线程优先级低,不保证被执行)

终结方法的另一个问题是：如果忽略在终结过程中被抛出来的未被捕获的异常，该对象的终结过程也会终止.未被捕获的异常会使对象处于破坏的状态，如果另 一个线程企图使用这种被破坏的对象，则可能发生任何不确定的行为 。

使用终结方法和清除方法有一个非常严重的性能损失  

终结方法有一个严重的安全问题： 它们为终结方法攻击（ finalizer attack ） 打开了类的大门 。从构造器抛出的异常，应该足以防止对象继续存在；有了终结方法的存在，这一点就做不到了 。为了防止非 final 类受到终结方法攻击 ， 要编写一个空的 final 的 finalize 方法 。       

但是也有两种合法用途:

1. 当资源的所有者忘记调用它的 close 方法时，终结方法或者清除方法可以充当  "安全网"
2. 与对象的本地对等体（ native peer）有关  .本地对等体是一个本地（非 Java 的）对象（ native object），普通对象通过本地方法（ native method ）委托给一个本地对象 。  

总而言之，除非是作为安全网，或者是为了终止非关键的本地资源，否则请不要使用清除方法，对于在 Java 9 之前的发行版本，则尽量不要使用终结方法 。若使用了终结方法或者清除方法，则要注意它的不确定性和性能后果 。

### 【09】 try-with-resources 优先于 try-finally  

后一个异常就会被禁止，以保留第一个异常 。 通过编程调用 getSuppressed 方法还可以访问到它们， getsuppressed 方法也已经添加在 Java 7 的 Throwable 中了 。并且前者也可以有catch语句块

## 第 3 章 对于所有对象都通用的方法  

尽管 Object 是一个具体类，但设计它主要是为了扩展。 它所有的非 final 方法都有明确的通用约定 

### 【10】 覆盖 equals 时请遵守通用约定

覆盖 equals 方法看起来似乎很简单，但是有许多覆盖方式会导致错误，并且后果非常严重 。 最容易避免这类问题的办法就是不覆盖 equals 方法， 如果满足了以下任何一个条件，这就正是所期望的结果 ：   

- 类的每个实例本质上都是唯一的(枚举,单例)
- 类没有必要提供“逻辑相等”（ logical equality ）的测试功能 。
- 超类已经覆盖了 equals ， 超类的行为对于这个 类也是合适的 。  
- 类是私有的,或者是包级私有的,可以确定它的 equals 方法永远不会 被调用 。  

在覆盖 equals 方法的时候，必须要遵守它的通用约定  :

- 自反性

- 对称性

- 传递性:

  面向对象语言中等价关系的一个基本问题。**除非你愿意放弃面向对象的抽象优点，否则无法继承一个可实例化的类并添加一个值组件，同时保留 equals 约定。**

  使用组合而不是继承更利于编写

- 一致性: 多次调用结果一致

- 非空性

实现equals的诀窍:

- 使用==操作符检查“参数是否为这个对象的引用”   
- 使用 instanceof 操作符检查“参数是否为正确的类型”  
-  把参数转换成正确的类型   
- 对于该类中的每个“关键”（ significant ）域，检查参数中的域是否与该对象中对应的域相匹配  

一些告诫 :

- 覆盖 equals 时总要覆盖 hashCode  
- 不要企 图让 equals 方法过于 智能 
- 不要将 equals 声明中的 Object 对象替换为其他的类型   

### 【11】当覆盖 equals 时，始终覆盖 hashCode

**在覆盖 equals 的类中，必须覆盖 hashCode。** 如果你没有这样做，你的类将违反 hashCode 的一般约定，这将阻止该类在 HashMap 和 HashSet 等集合中正常运行。

Object对hashCode的规范:

- 相等的对象必须具有相等的 hash 代码
- 如果两个对象根据 equals(Object)方法比较是相等的，那么调用这两个对象中的 hashCode 方法都必须产生同样的整数结果   
- hashCode相同,equals不一定相同

因没有覆盖 hashCode 而违反的关键约定是第二条：相等的对象必须具有相等的散到码（ hash code ） 。   

一个好的散列函数通常倾向于“为不相等的对象产生不相等的散列码” 。 这正是hashCode 约定中第 三条 的含义  

一种解决方案:

1. 声明一个 int 变量并命名为 result ，将它初始化为对象中第一个关键域的散列码c   
2. 对象中剩下的每一个关键域 f 都完成以下步骤：  
   1. 为该域计算 int 类型的散列码 C:  
      - 基本类型 包装类.hashCode(f)
      - 引用类型 递归调用hashCode
      - 如果该域是一个数组，则要把每一个元素当作单独的域来处理   
   2. result = 31*result + c;
3. 返回result

推荐只在性能不重要的情况下使用这种 hash 函数

```java
Objects.hash(lineNum, prefix, areaCode);
```

假如计算开销很大,可以建立缓存,用一个字段记录

### 【12】始终覆盖 toString 方法

toString 方法应该返回对象中包含的所有值得关注的信息，  

无论是否决定指定格式，都应该在文档中明确地表明你的意图  

无论是否指定格式， 都为 toString 返回值中包含的所有信息提供一种可以通过编程访问之的途径  

静态工具类和枚举类就不要编写toString了

在所有其子类共享通用字符串表示法的抽象类中，一定要编写一个 toString方法 。  

### 【13】谨慎地覆盖 clone  

Cloneable 接口的目的是作为对象的一个 mixin 接口,表明这样的对象允许克隆.主要缺陷在于缺少一个 clone 方法， 而 Object 的 clone 方法是受保护的

事实上，实现 Cloneable 接口的类是为了提供一个功能适当的公有的 clone 方法 。  暗示一种语言之外的机制 ---它无须调用构造器就可以创建对象  

一般含义: 以下的表达式一般都是true,但是不是强制规定

```java
x.clone() != x; 
x.clone().getClass() == x.getClass(); 
x.clone().equals(x);
x.clone().getClass() == x.getClass();
    
```

不可变的类永远都不应该提供 clone 方法

实际上， clone 方法就是另一个构造器； 必须确保它不会伤害到原始的对象， 并确保正确地创建被克隆对象中的约束条件 。   

就像序列化一样，Cloneable 架构与引用可变对象的 final 域的正常用法是不相兼容的，  

对象拷贝的更好的办法是提供一个拷贝构造器或拷贝工厂

总之，复制功能最好由构造器或者工厂提供 。 这条规则最绝对的例外是数组，最好利用 clone 方法复制数组。  

### 【14】考虑实现 Comparable 接口

规则可以参考 equals 的,自反性,对称性和传递性   

强烈建议 `(x.compareTo(y)== 0) == (x.equals(y))` 成立，但不是必需的

在 Java 8 中，Comparator 接口配备了一组比较器构造方法，

可以流畅地构造比较器;对象引用类型也有比较器构造方法 。 

在 compareTo 方法中使用关系运算符 < 和 > 冗长且容易出错，因此不再推荐使用。

静态方法 comparing有两个重载  (不要使用简单的加减,很容易溢出)

## 第 4 章 类和接口

### 【15】使类和成员的可访问性最小化

一个模块不需要知道其他模块的内部工作情况 。 这个概念被称为信息隐藏.

- 解耦
- 提高了软件的可重用性  
- 降低了构建大型系统的风险  

规则很简单 ： 尽可能地使每个类或者成员不被外界访问 。   

顶层的类和接口 : package private和 public(应该尽量少用受保护的成员)

> 公有类的实例域决不能是公有的,也同样适用于静态域  (常量除外)  
>
> 可以使用私有域加上返回不可变副本返回
>
> 模块可以通过其模块声明中的导出声明显式地导出它的一部分包 
>
> 现在说模块将在 JDK 本身之外获得广泛的使用，还为时过早 。 同时，似乎最好不用它们，除非你的需求非常迫切 。   

### 【16】要在公有类而非公有域中使用访问方法  

也就是getter和setter

如果类可以在它所在的包之外进行访问，就提供访问方法，  

如果类是包级私有的，或者是私有的嵌套类， 直接暴露它的数据域并没有本质的错误  

### 【17】使可变性最小化  

为了使类成为不可变，要遵循下面五条规则：  

1. 不要提供任何会修改对象状态的方法  (没有setter方法)
2. 保证类不会被扩展   
3. 声明所有的域都是 final 的  
4. 声明所有的域都为私有的 
5. 确保对子任何可变组件的互斥访问  (不要依赖客户端引用初始化,不要返回引用对象,使用 保护性拷贝) 

函数方法: 这些方法返回了一个函数的结果，这些函数对操作数进行运算但并不修改它   

过程/命令方法: 使用这些方法时，将一个过程作用在它们的操作数上， 会导致它的状态发生改变  

函数方法好处:

1. 不可变对象比较简单  
2. 不可变对象本质上是线程安全的，它们不要求同步  (自由地共享)
3. 不仅可以共享不可变对象，甚至也可以共享它们的内部信息 
4. 不可变对象为其他对象提供了大量的构件
5. 不可变对象无偿地提供了失败的原子性  
6. 不可变类真正唯一的缺点是 ， 对于每个不同的值都需要一个单独的对象 。  

有关序列化功能的一条告诫有必要在这里提出来。 如果你选择让自己的不可变类实现Serializable 接口 ，并且它包含一个或者多个指向可变对象的域，就必须提供一个显式的readObject 或者 readResolve 方法，或者使用 ObjectOutputStream.writeUnshared 和 ObjectIntputStream.readUnshared 方法，即便默认的序列化形式是可以接受的，也是如此 。 否则 ，攻击者可能从不可变的类创建可变的实例 。  

除非有令人信服的理由要使域变成是非 final 的，否则要使每个域都是 private final 的 。  

构造器应该创建完全初始化的对象 ，并建立起所有的约束关系  

### 【18】复合优先于继承  

对于专门为了继承而设计并且具有很好的文档说明的类来说，使用继承也是非常安全的  

- 与方法调用不同的是，继承打破了封装性  

现有的类变成了新类的一个组件 。 新类中的每个实例方法都可以调用被包含的现有类实例中对应的方法，并返回它的结果 。 这被称为转发，新类中的方法被称为转发方法 。   

需要注意的一点是， 包装类不适合用于 回调框架  

只有当子类和超类之间确实存在子类型关系时，使用继承才是恰当的   

### 【19】要么设计继承并提供文档说明，要么禁止继承  

为继承而设计的类必须有文档说明它可覆盖的方法的自用性

@implSpec 标签是在 Java 8 中增加的

类必须以精心挑选的受保护的方法的形式，提供适当的钩子，以便进入其内部工作中   (可以参照AbstractList 等抽象类地实现)

构造器决不能调用可被覆盖的方法， 无论是直接调用还是间接调用 (从构造函数调用私有方法、最终方法和静态方法是安全的，它们都是不可覆盖的)   

无论是 clone 还是 readObject ， 都不可以调用可覆盖的方法，不管是以直接还是间接的方式 。  

### 【20】接口优于抽象类

- 现有的类可以很容易被更新，以实现新的接口 
- 接口是定义 mixin （混合类型）的理想选择 
- 接口允许构造非层次结构的类型框架   
- 接口使得安全地增强类的功能成为可能    (包装模式)

实现了这个接口的类可以把对于接口方法的调用转发到一个内部私有类的实例上，这个内部私有类扩展了骨架实现类 。 这种方法被称作模拟多重继承  骨架实现类是为了继承的目的而设计的  ,好的文档绝对是非常必要的，  

### 【21】为后代设计接口

并非每一个可能的实现的所有变体，始终都可以编写出一个缺省方法 。

有了缺省方法，接口的现有实现就不会出现编译时没有报错或警告，运行时却失败的情况 。

尽管缺省方法现在已经是 Java 平台的组成部分， 但谨慎设计接口仍然是
至关重要的 。  

### 【22】接口只用于定义类型

有一种接口被称为常量接口,只包含静态的 final 域， 每个域都导出一个常量  

常量接口模式是对接口的不良使用 。  (枚举类不是更好吗)

### 【23】类层次结构优于带标签的类

标签类过于冗长、容易出错，并且效率低下 。  

标签类正是对类层次的一种简单的仿效  

### 【24】静态成员类优于非静态成员类  

嵌套类是指定义在另一个类的内部的类 。 嵌套类存在的目的应该只是为
它的外围类提供服务  

- 静态成员类

  > 最好把它看作是普通的类，只是碰巧被声明在另一个类内部,可以访问外部成员,包括私有地部分  
  >
  > 一种常见用法是作为公有的辅助类，只有与它的外部类一起使用才有意义  (Calculator的Operation)

- 非静态成员类

  > 每个实例都隐含地与外围类的一个外围实例,在没有外围实例的情况下，要想创建非静态成员类的实例是不可能的  
  >
  > 一种常见用法是定义一个 Adapter ,它允许外部类的实例被
  > 看作是另一个不相关的类的实例    
  >
  > 一种常见用法是代表外围类所代表的对象的组件  
  >
  > 如果声明成员类不要求访问外围实例，就要始终把修饰符 static 放在它的声明中， 否则容易造成内存泄漏  

- 匿名类

- 局部类

如果一个嵌套类需要在单个方法之外仍然是可见的，或者它太长了，不适合放在方法内部，就应该使用成员类 。

如果成员类的每个实例都需要一个指向其外围实例的引用，就要把成员类做成非静态的；否则 ，就做成静态的 。 

假设这个嵌套类属于一个方法的内部，如果你只需要在一个地方创建实
例， 并且已经有了一个预置的类型可以说明这个类的特征，就要把它做成匿名类 ； 否则， 就做成局部类 。    

### 【25】源文件仅限有单个顶层类

如果一定要把多个顶级类放进一个源文件中，就要考虑使用静态成员类，以此代替将这两个类分到独立源文件中去 。  

永远不要把多个顶级类或者接口放在一个源文件中 。 遵循这个规则可
以确保编译时一个类不会有多个定义 。  

## 第 5 章 泛型

### 【26】不要使用原始类型

即不带泛型参数的类型,比如 使用 List\<String\> 而不是 List

如果使用原生态类型，就失掉了泛型在安全性和描述性方面的所有优势  

使用无限制的通配符类型。 如果要使用泛型，但不确定或者不关心实际的类型参数，就可以用一个问号代替 。   

### 【27】消除 unchecked 警告

要尽可能地消除每一个非受检警告。  

如果无法消除警告 ，同时可以证明引起警告的代码是类型安全的，（只有在这种情况下 ）才可以用 一个＠Suppre ssWarnings ( “ unchecked " ）注解来禁止这条警告。   

尽可能小的范围内使用＠ Suppress ­Warnings(" unchecked ")注解禁止该警告  

每当使用 SuppressWarnings ( “ unchecked " ）注解时，都要添加一条注释，说明为什么这么做是安全的   

### 【28】列表优于数组  

- 数组是协变的 , 是具体化的
- 泛型则是不变的  

为什么创建泛型数组是非法的？因为它不是类型安全的  

```java
// Why generic array creation is illegal - won't compile!
List<String>[] stringLists = new List<String>[1]; // (1)
List<Integer> intList = List.of(42); // (2)
Object[] objects = stringLists; // (3)
objects[0] = intList; // (4)
String s = stringLists[0].get(0); // (5)
```



当你得到泛型数组创建错误时，最好的解决办法通常是优先使用集合类型 List\<E\>,而不是数组类型 E ［]  

### 【29】优先使用泛型

不能创建泛型数组,所以一旦使用Object[]进行存储,就会发生类型转换问题

1. 数组进行强制转换,证明类型转换是安全的(转换一次,它会导致堆污染  )
2. 返回元素时候进行强制转换

### 【30】优先考虑泛型方法  

虽然相对少见，但是通过某个包含该类型参数本身的表达式来限制类型参数是允许的 。这就是递归类型限制  

### 【31】利用有界通配符来提升 API 的灵活性  

有时候，我们需要的灵活性要比不变类型所能提供的更多  

PECS 表示 producer-extends, consumer－super 。

使用时始终应该是 Comparable<? super T> 优先于Comparable<T＞ 

一般来说， 如果类型参数只在方法声明中出现一次，就可以用通配符取代它 。 如果是无限制的类型参数，就用无限制的通配符取代它；如果是有限制的类型参数，就用有限制的通配符取代它 。  

### 【32】谨慎地合用泛型和可变参数

可变参数的作用在于让客户端能够将可变数量的参数传给方法，但这是个技术露底（ leaky abstration ）：当调用一个可变参数方法时，会创建一个数组用来存放可变参数；  

非具体化类型是指其运行时表示的信息少于其编译时表示的信息，并且几乎所有泛型和参数化类型都是不可具体化的

当一个参数化类型的变量指向一个不是该类型的对象时，会产生堆污染.它导致编辑器的自动生成转换失败，破坏了泛型系统的基本保证    

```java
// 泛型和可变参数混合使用可能违反类型安全原则！
static void dangerous(List<String>... stringLists) {
    List<Integer> intList = List.of(42);
    Object[] objects = stringLists;
    objects[0] = intList; // Heap pollution
    String s = stringLists[0].get(0); // ClassCastException
}
```

此转换失败，表明类型安全性受到了影响，并且在泛型可变参数数组中存储值是不安全的。

例子引出了 一个有趣的问题：为什么显式创建泛型数组是非法的，用泛型可变参数声明方法却是合法的呢  ???

答案在于，带有泛型可变参数或者参数化类型的方法在实践中用处很大，因此 Java 语言的设计者选择容忍这 一矛盾的存在  

本质上， SafeVarargs 注解是通过方法的设计者做出承诺，声明这是类型安全的 。  

```java
// UNSAFE - Exposes a reference to its generic parameter array!
// 编译后得到 T 是 Object 类型
static <T> T[] toArray(T... args) {
  return args;
}

// 编译后得到 T 是 String类型
static <T> T[] pickTwo(T a, T b, T c) {
  switch(ThreadLocalRandom.current().nextInt(3)) {
    case 0: return toArray(a, b);
    case 1: return toArray(a, c);
    case 2: return toArray(b, c);
  }
  throw new AssertionError(); // Can't get here
}

public static void main(String[] args) {
  String[] attributes = pickTwo("Good", "Fast", "Cheap");
}
```

它实际上很危险 ！这个数组的类型，是由传到方法的参数的编译时类型来决定的，编译器没有足够的信息去做准确的决定 。 因为该方法返回其可变参数数组 ，它会将堆污染传到调用堆枝上 。  

泛型可变参数方法在下列条件下是安全的 ：

1. 它没有在可变参数数组中保存任何值 。
2. 它没有对不被信任的代码开放该数组（或者其克隆程序） 。

以上两个条件只要有任何一条被破坏，就要立即修正它 。  

### 【33】考虑类型安全的异构容器

有时候你会需要更多的灵活性 。 例如，数据库的行可以有任意数量的列，如果能以类型安全的方式访问所有列就好了 。  (不能使用列接口吗?)

局限性:

1. 首先，恶意客户端很容易通过使用原始形式的类对象破坏 Favorites 实例的类型安全。但是生成的客户端代码在编译时将生成一个 unchecked 警告
2. 它不能用在不可具体化的,你可以保存最喜爱的 String 或者 String[]，但不能保存最喜爱的 List\<String\>

注解 API 广泛利用了有限制的类型令牌

```java
// 本质上是异构容器
public <T extends Annotation>
T getAnnotation(Class <T> annotationType);
```

## 第 6 章 枚举和注解

### 【34】用枚举类型代替 int 常量

枚举类型是指由一组固定的常量组成合法值的类型

没有枚举之前,使用int/String枚举模式,不具有类型安全性,也几乎没有描述性可言

特点: 单例,final类,类型安全

包含同名常量的多个枚举类型可以在一个系统中和平共处，因为每个类型都有自己的命名空间,因为导出常量的域在枚举类型和它的客户端之间提供了一个隔离层 ： 常量值并没有被编译到客户端代码中，而是在 int 枚举模式之中

枚举类型还允许添加任意的方法和域，并实现任意的接口        

为了将数据与枚举常量关联起来，得声明实例域，并编写一个带有数据并将数据保存在域中的构造器 。    

当把一个元素从一个枚举类型中移除时，会发生什么情况呢？答案是 ： 没有引用该元素的任何客户端程序都会继续正常工作 。  

一种更好的方法可以将不同的行为与每个枚举常量关联起来：在枚举类
型中声明一个抽象的 appl y 方法，并在特定于常量的类主体中，用具体的方法覆盖每个常量的抽象 apply 方法 。 这种方法被称作特定于 常量的方法实
现

枚举类型有一个自动产生的 valueOf(String ）方法，它将常量的名字转变成常量本身 。 如果在枚举类型中覆盖 toString ， 要考虑编写一个 fromString 方法，将定制的字符串表示法变回相应的枚举 。  

除了编译时常量域之外，枚举构造器不可以访问枚举的静态域。 (静态域还没初始化,原因是域按顺序初始化,每个枚举对象都是静态的常量)  

枚举结合策略模式可以实现灵活的计算方式

枚举中的 switch 语旬适合子给外部的枚举类型增加特定于常量的行为 ,枚举有个小小的性能缺点， 即装载和初始化枚举时会需要空间和时间的成本，但在实践中几乎注意不到这个问题   

每当需要一组固定常量．并且在编译时就知道其成员的时候，就应该使用枚举 。 枚举类型中的常量集并不一定要始终保持不变 。   

### 【35】使用实例字段替代序数

所有的枚举都有一个 ordinal 方法，它返回每个枚举常量在类型中的数字位置   

永远不要根据枚举的序数导出与它关联的值， 而是要将它保存在一个实例域中   

Enum 规范中谈及 ordinal 方法时写道：“大多数程序员都不需要这个方法 。 它是设计用于像 EnumSet 和 EnumMap 这种基于枚举的通用数据结构的 。 ”除非你在编写的是这种数据结构，否则最好完全避免使用 ordinal 方法 。  

### 【36】用 EnumSet 替代位字段

如果一个枚举类型的元素主要用在集合中，一般就使用 int 枚举模式  

位域表示法也允许利用位操作，有效地执行像 union 和 intersection 这样的集合操作 。 但位域具有 int 枚举常量的所有缺点，甚至更多 。  

- 当位域以数字形式打印时，翻译位域比翻译简单的工 nt 枚举常量要困难得多  
- 要遍历位域表示的所有元素也没有很容易的方法  
- 编写 API 的时候，就必须先预测最多需要多少位，同时还要给位域选择对应的类型  

java.util 包提供了 EnumSet 类来有效地表示从单个枚举类型中提取的多个值的多个集合  .这个类实现Set 接口，提供了丰富的功能、类型安全性，以及可以从任何其他 Set 实现中得到的互用性 。 但是在内部具体的实现上，每个 EnumSet 内容都表示为位矢量 .  

- 如果底层的枚举类型有 64 个或者更少的元素（大多如此）整个 EnumSet 就使用单个 long 来表示，因此它的性能比得上位域的性能 。  

正是因为枚举类型要用在集合中，所以没有理由用位域来表示它   

###  【37】使用 EnumMap 替换序数索引

用普通的序数索引数组是非常不合适的：应使用 EnumMap 代替。 如果所表示的关系是多维的，则使用EnumMap<..., EnumMap<...>>。这是一种特殊的基本原则，程序员很少（即使有的话）使用 Enum.ordinal

### 【38】使用接口模拟可扩展枚举

使用接口规范枚举类的行为

在可以使用基础操作的任何地方，现在都可以使用新的操作，只要 API 是写成采用接口 类型而非实现

用接口模拟可伸缩枚举有个小小的不足，即无法将实现从一个枚举类型继承到另一个枚举类型 。 如果实现代码不依赖于任何状态，就可以将缺省实现放在接口中 。  

应用 java.nio.file.LinkOption 枚举类型实现 CopyOption 和 OpenOption 接口。

### 【39】注解优于命名模式

- 首先，排版错误会导致没有提示的失败
- 其次,无法确保只在相应的程序元素上使用它们
- 再者,它们没有提供将参数值与程序元素关联的好方法

注解不会改变被注解代码的语义，而是通过工具对其进行特殊处理

处理使用反射获取每个字段/方法等的注解,逐个判断注解内容

添加可重复注解是为了提高源代码的可读性，源代码在逻辑上将同一注解类型的多个实例应用于给定的程序元素。如果你觉得它们增强了源代码的可读性，那么就使用它们，但是请记住，在声明和处理可重复注解方面有更多的样板，并且处理可重复注解很容易出错。

### 【40】坚持使用 @Override 注解

应该在 要覆盖超类声明的每个方法声明上使用 @Override 注解。 这条规则有一个小小的例外。如果你正在编写一个没有标记为 abstract 的类，并且你认为它覆盖了其超类中的抽象方法，那么你不必费心在这些方法上添加 @Override 注解

### 【41】使用标记接口定义类型

相比标记注解的好处:

1. 首先，**标记接口定义的类型由标记类的实例实现；标记注解不会。** 标记接口类型的存在允许你在编译时捕获错误，如果你使用标记注解，则在运行时才能捕获这些错误。
2. **标记接口相对于标记注解的另一个优点是可以更精确地定位它们。** 如果注解类型使用 `@Target(ElementType.TYPE)` 声明，它可以应用于任何类或接口

我是否可以编写一个或多个方法，只接受具有这种标记的对象？那就使用标记接口,这将使你能够将接口用作相关方法的参数类型，这将带来编译时类型检查的好处。如果你确信自己永远不会编写只接受带有标记的对象的方法，那么最好使用标记注解。此外，如果框架大量使用注解，那么标记注解就是明确的选择



## 第 7 章 λ 表达式和流

### 【42】λ 表达式优于匿名类

Item-26 告诉你不要使用原始类型，Item-29 告诉你要优先使用泛型，Item-30 告诉你要优先使用泛型方法。在使用 lambda 表达式时，这些建议尤其重要，因为编译器获得了允许它从泛型中执行类型推断的大部分类型信息

与方法和类不同，**lambda 表达式缺少名称和文档；如果一个算法并非不言自明，或者有很多行代码，不要把它放在 lambda 表达式中**。 一行是理想的，三行是合理的最大值

**很少序列化 lambda**。如果你有一个想要序列化的函数对象，比如比较器，那么使用私有静态嵌套类的实例

### 【43】方法引用优于 λ 表达式

可以由方法名称看出作用

**如果方法引用更短、更清晰，则使用它们；如果没有，仍然使用 lambda 表达式。**

> 不用 Function.identity() 而是使用 x->x

### 【44】优先使用标准函数式接口

**不要尝试使用带有包装类的基本函数式接口，而不是使用基本类型函数式接口**

**总是用 `@FunctionalInterface` 注释你的函数接口。**

有三个目的：

1. 它告诉类及其文档的读者，接口的设计是为了启用 lambdas 表达式；
2. 它使你保持诚实，因为接口不会编译，除非它只有一个抽象方法；
3. 它还可以防止维护者在接口发展过程中意外地向接口添加抽象方法。

### 【45】合理地使用流

流（表示有限或无限的数据元素序列）

流管道（表示对这些元素的多阶段计算）

请注意，没有 Terminal 操作的流管道是无动作的，因此不要忘记包含一个 Terminal 操作。

有些事情你可以对代码块做，而你不能对函数对象做：

- 从代码块中，可以读取或修改作用域中的任何局部变量；在 lambda 表达式中，只能读取 final 或有效的 final 变量 ,不能修改任何局部变量。
- 从代码块中，可以从封闭方法返回、中断或继续封闭循环，或抛出声明要抛出的任何已检查异常；在 lambda 表达式中，你不能做这些事情。

如果你不确定一个任务是通过流还是通过迭代更好地完成，那么同时尝试这两种方法，看看哪一种更有效

### 【46】在流中使用无副作用的函数

### 【47】优先选择 Collection 而不是流作为返回类型

使用适配器进行两者之间的转换

```java
// Adapter from Stream<E> to Iterable<E>
public static <E> Iterable<E> iterableOf(Stream<E> stream) {
    return stream::iterator;
}

// Adapter from Iterable<E> to Stream<E>
public static <E> Stream<E> streamOf(Iterable<E> iterable) {
    return StreamSupport.stream(iterable.spliterator(), false);
}
```

如果集合比较大,可以表示成有规律的形式(比如幂集),使用AbstractList

### 【48】谨慎使用并行流

实际上就是分治算法

通常，**并行性带来的性能提升在 ArrayList、HashMap、HashSet 和 ConcurrentHashMap 实例上的流效果最好；int 数组和 long 数组也在其中。** 这些数据结构的共同之处在于，它们都可以被精确且廉价地分割成任意大小的子程序，这使得在并行线程之间划分工作变得很容易。stream 库用于执行此任务的抽象是 spliterator，它由流上的 spliterator 方法返回并可迭代。



总之，甚至不要尝试并行化流管道，除非你有充分的理由相信它将保持计算的正确性以及提高速度。不适当地并行化流的代价可能是程序失败或性能灾难。如果你认为并行性是合理的，那么请确保你的代码在并行运行时保持正确，并在实际情况下进行仔细的性能度量。如果你的代码保持正确，并且这些实验证实了你对提高性能的怀疑，那么，并且只有这样，才能在生产代码中并行化流。

## 第 8 章 方法

### 【49】检查参数的有效性

如果没有验证参数，可能会违反故障原子性

对于公共方法和受保护的方法，如果在方法说明使用 Javadoc 的 `@throws` 标签记录异常，表明如果违反了对参数值的限制，将会引发该异常

**在 Java 7 中添加的 `Objects.requireNonNull` 方法非常灵活和方便，因此不再需要手动执行空检查。**

特别重要的是，应检查那些不是由方法使用，而是存储起来供以后使用的参数的有效性

不加区别地依赖隐式有效性检查可能导致失败原子性的丢失

不要从本条目推断出：对参数的任意限制都是一件好事。相反，你应该把方法设计得既通用又实用。对参数施加的限制越少越好，假设该方法可以对它所接受的所有参数值进行合理的处理。然而，一些限制常常是实现抽象的内在限制。

### 【50】在需要时制作防御性副本

**是在检查参数的有效性之前制作的，有效性检查是在副本上而不是在正本上执行的。**

**对可被不受信任方子类化的参数类型，不要使用 clone 方法进行防御性复制。**

防御性复制可能会带来性能损失，而且并不总是合理的

如果一个类具有从客户端获取或返回给客户端的可变组件，则该类必须防御性地复制这些组件。如果复制的成本过高，并且类信任它的客户端不会不适当地修改组件，那么可以不进行防御性的复制，取而代之的是在文档中指明客户端的职责是不得修改受到影响的组件

### 【51】仔细设计方法签名

- **仔细选择方法名称**
- **不要提供过于便利的方法**
- **避免长参数列表**
  - 分解成多个方法
  - 创建 helper 类来保存参数组。通常，这些 helper 类是静态成员类
  - 从对象构建到方法调用都采用建造者模式

- **对于参数类型，优先选择接口而不是类**
- **双元素枚举类型优于 boolean 参数，** 除非布尔值的含义在方法名中明确

### 【52】合理地使用重载

 **重载方法的选择是静态的，而覆盖法的选择是动态的**

**安全、保守的策略是永远不导出具有相同数量参数的两个重载。**

自动装箱和泛型出现后，在重载时就应更加谨慎。Java 8 中添加的 lambdas 和方法引用进一步增加了重载中混淆的可能性

**不要重载方法来在相同的参数位置上使用不同的函数式接口**

### 【53】明智地使用可变参数

可变参数是为 printf 和经过改造的核心反射机制而设计的，它们与可变参数同时被添加到 JDK，printf 和 reflection 都从可变参数中受益匪浅。

	总之，当你需要定义具有不确定数量参数的方法时，可变参数是非常有用的。在可变参数之前加上任何必需的参数，并注意使用可变参数可能会引发的性能后果。


### 【54】返回空集合或数组，而不是 null

空返回值比空集合或数组更可取，因为它避免了分配空容器的开销。

- 在这个级别上担心性能是不明智的，除非分析表明这个方法正是造成性能问题的真正源头
- 返回空集合和数组而不分配它们是可能的

可以使用不可变对象进行共享.是一个优化,但是很少用

### 【55】明智地的返回 Optional

Optional 的本质上是一个不可变的集合，它最多可以容纳一个元素

允许该方法返回一个空结果来表明它不能返回有效的结果。具备 Optional 返回值的方法比抛出异常的方法更灵活、更容易使用，并且比返回 null 的方法更不容易出错。

**容器类型，包括集合、Map、流、数组和 Optional，不应该封装在 Optional 中。** 你应该简单的返回一个空的 `List<T>`，而不是一个空的 `Optional<List<T>>`

Optional 不适合在某些性能关键的情况下使用。某一特定方法是否属于这一情况只能通过仔细衡量来确定

> 总之，如果你发现自己编写的方法不能总是返回确定值，并且你认为该方法的用户在每次调用时应该考虑这种可能性，那么你可能应该让方法返回一个 Optional。但是，你应该意识到，返回 Optional 会带来实际的性能后果；对于性能关键的方法，最好返回 null 或抛出异常。最后，除了作为返回值之外，你几乎不应该以任何其他方式使用 Optional。

------

### 【56】为所有公开的 API 元素编写文档注释

**要正确地编写 API 文档，必须在每个公开的类、接口、构造函数、方法和字段声明之前加上文档注释。** 如果一个类是可序列化的，还应该记录它的序列化形式

**方法的文档注释应该简洁地描述方法与其客户端之间的约定。**

```java
/**
* Returns the element at the specified position in this list.
**
<p>This method is <i>not</i> guaranteed to run in constant
* time. In some implementations it may run in time proportional
* to the element position.
**
@param index index of element to return; must be
* non-negative and less than the size of this list
* @return the element at the specified position in this list
* @throws IndexOutOfBoundsException if the index is out of range
* ({@code index < 0 || index >= this.size()})
*/
E get(int index);

/**
* A geometric series converges if {@literal |r| < 1}.
*/
```

**文档注释应该在源代码和生成的文档中都具备可读性。** 如果不能同时实现这两个目标，要保证生成的文档的可读性超过源代码的可读性。

**类或接口中的任何两个成员或构造函数都不应该具有相同的摘要描述**

**为泛型类型或方法编写文档时，请确保说明所有类型参数:**

**编写枚举类型的文档时，一定要说明常量** 以及类型、任何公共方法

**为注释类型的文档时，请确保说明全部成员** 以及类型本身。

包级别的文档注释应该放在名为 package-info.java 的文件中。除了这些注释之外，package-info.java 必须包含一个包声明，并且可能包含关于这个声明的注释。类似地，如果你选择使用模块系统，模块级别的注释应该放在 module-info.java 文件中。

**无论类或静态方法是否线程安全，你都应该说明它的线程安全级别**

## 第 9 章 通用程序设计

### 【57】将局部变量的作用域最小化

**将局部变量的作用域最小化，最具说服力的方式就是在第一次使用它的地方声明**

**每个局部变量声明都应该包含一个初始化表达式**

就限制作用域 for 比 while 更好

### 【58】for-each 循环优于传统的 for 循环

不幸的是，有三种常见的情况你不应使用 for-each：

1. **破坏性过滤**，如果需要遍历一个集合并删除选定元素，则需要使用显式的迭代器，以便调用其 remove 方法。
2. **转换**，如果需要遍历一个 List 或数组并替换其中部分或全部元素的值，那么需要 List 迭代器或数组索引来替换元素的值
3. **并行迭代**，如果需要并行遍历多个集合，那么需要显式地控制迭代器或索引变量，以便所有迭代器或索引变量都可以同步执行

### 【59】了解并使用库

在大多数情况下，**选择的随机数生成器现在是 ThreadLocalRandom**

- **通过使用标准库，你可以利用编写它的专家的知识和以前使用它的人的经验。**
- 不必浪费时间为那些与你的工作无关的问题编写专门的解决方案
- 随着时间的推移，它们的性能会不断提高，而你无需付出任何努力
- 可以将代码放在主干中

至少了解

-  **`java.lang`、`java.util` 和 `java.io` 的基础知识及其子包**

- collections 框架和 streams 库
- JUC

### 【60】若需要精确答案就应避免使用 float 和 double 类型

**float 和 double 类型特别不适合进行货币计算**

使用 BigDecimal 有两个缺点：它与原始算术类型相比很不方便，而且速度要慢得多。

如果性能是最重要的，那么你不介意自己处理十进制小数点，而且数值不是太大，可以使用 int 或 long。如果数值不超过 9 位小数，可以使用 int；如果不超过 18 位，可以使用 long。如果数量可能超过 18 位，则使用 BigDecimal。

### 【61】基本数据类型优于包装类

区别:

- 基本类型只有它们的值，而包装类型具有与其值不同的标识
- 基本类型只有全功能值，而每个包装类型除了对应的基本类型的所有功能值外，还有一个非功能值，即 null
- 基本类型比包装类型更节省时间和空间

**将 `==` 操作符应用于包装类型几乎都是错误的。**

**在操作中混合使用基本类型和包装类型时，包装类型就会自动拆箱**

适合的情况:

- 泛型只能是包装类型
- 集合的K-V键值对

### 【62】其他类型更合适时应避免使用字符串

字符串是其他值类型的糟糕替代品.如果有合适的值类型，无论是基本类型还是对象引用，都应该使用它；如果没有，你应该写一个。

总之，当存在或可以编写更好的数据类型时，应避免将字符串用来表示对象。

字符串经常被误用的类型包括基本类型、枚举和聚合类型。

### 【63】当心字符串连接引起的性能问题

使用 **字符串串联运算符重复串联 n 个字符串需要 n 的平方级时间。** 

拼接使用StringBuilder或者，使用字符数组，再或者一次只处理一个字符串，而不是组合它们



### 【64】通过接口引用对象

**如果存在合适的接口类型，那么应该使用接口类型声明参数、返回值、变量和字段。** 惟一真正需要引用对象的类的时候是使用构造函数创建它的时候

**如果没有合适的接口存在，那么用类引用对象是完全合适的**

- String等final类型
- 框架对象通常是类
- 实现接口但同时提供接口中不存在的额外方法的类

### 【65】接口优于反射

可以动态获得几乎所有类型,但是有代价

- 失去了编译时类型检查的所有好处，包括异常检查
- 执行反射访问所需的代码既笨拙又冗长。
- 性能降低

**通过非常有限的形式使用反射，你可以获得反射的许多好处，同时花费的代价很少。** 对于许多程序，它们必须用到在编译时无法获取的类，在编译时存在一个适当的接口或超类来引用该类

### 【66】明智地使用本地方法

> 一般很少需要使用它们来提高性能。如果必须使用本地方法来访问底层资源或本地库，请尽可能少地使用本地代码，并对其进行彻底的测试。本地代码中的一个错误就可以破坏整个应用程序。

------

### 【67】明智地进行优化

努力编写 **好的程序，而不是快速的程序。** 如果一个好的程序不够快，它的架构将允许它被优化。

**尽量避免限制性能的设计决策**

这些设计组件中最主要的是 API、线路层协议和持久数据格式。这些设计组件不仅难以或不可能在事后更改，而且所有这些组件都可能对系统能够达到的性能造成重大限制

**考虑 API 设计决策的性能结果。**

使公共类型转化为可变，可能需要大量不必要的防御性复制

总而言之，不要努力写快的程序，要努力写好程序；速度自然会提高。但是在设计系统时一定要考虑性能，特别是在设计 API、线路层协议和持久数据格式时。当你完成了系统的构建之后，请度量它的性能。如果足够快，就完成了。如果没有，利用分析器找到问题的根源，并对系统的相关部分进行优化。第一步是检查算法的选择：再多的底层优化也不能弥补算法选择的不足。根据需要重复这个过程，在每次更改之后测量性能，直到你满意为止。

## 第 10 章 异常

### 【68】遵守被广泛认可的命名约定

《The Java Language Specification》可以查到

|  Identifier Type   |                       Example                        |
| :----------------: | :--------------------------------------------------: |
| Package or module  | `org.junit.jupiter.api`, `com.google.common.collect` |
| Class or Interface |     Stream, FutureTask, LinkedHashMap,HttpClient     |
|  Method or Field   |              remove, groupingBy, getCrc              |
|   Constant Field   |             MIN_VALUE, NEGATIVE_INFINITY             |
|   Local Variable   |                  i, denom, houseNum                  |
|   Type Parameter   |            T, E, K, V, X, R, U, V, T1, T2            |

### 【69】仅在确有异常条件下使用异常

使用try-catch会降低性能,阅读增加困难

( 1）如果要在没有外部同步的情况下并发地访问对象，或者受制于外部条件的状态转换，则必须使用 Optional 或可识别的返回值，因为对象的状态可能在调用「状态测试」方法与「状态依赖」方法的间隔中发生变化。

（2）如果一个单独的「状态测试」方法重复「状态依赖」方法的工作，从性能问题考虑，可能要求使用 Optional 或可识别的返回值。

（3）在所有其他条件相同的情况下，「状态测试」方法略优于可识别的返回值。它提供了较好的可读性，而且不正确的使用可能更容易被检测：如果你忘记调用「状态测试」方法，「状态依赖」方法将抛出异常，使错误显而易见；

（4）如果你忘记检查一个可识别的返回值，那么这个 bug 可能很难发现。但是这对于返回 Optional 对象的方式来说不是问题。

总之，异常是为确有异常的情况设计的。不要将它们用于一般的控制流程，也不要编写强制其他人这样做的 API。

### 【70】对可恢复情况使用 checked 异常，对编程错误使用运行时异常

决定是使用 checked 异常还是 unchecked 异常的基本规则是：**使用 checked 异常的情况是为了合理地期望调用者能够从中恢复。** 通过抛出一个 checked 的异常，你可以强制调用者在 catch 子句中处理异常，或者将其传播出去。

有两种 unchecked 的可抛出项：运行时异常和错误。它们在行为上是一样的：都是可抛出的，通常不需要也不应该被捕获

- **使用运行时异常来指示编程错误。**
- **实现的所有 unchecked 可抛出项都应该继承 RuntimeException**除了 AssertionError 之外，不应该抛出Error及其子类

### 【71】避免不必要地使用 checked 异常

消除 checked 异常的最简单方法是返回所需结果类型的 Optional 对象.这种技术的缺点是，该方法不能返回任何详细说明其无法执行所需计算的附加信息。相反，异常具有描述性类型，并且可以导出方法来提供附加信息

### 【72】鼓励复用标准异常

|            Exception            |                       Occasion for Use                       |
| :-----------------------------: | :----------------------------------------------------------: |
|    IllegalArgumentException     | Non-null parameter value is inappropriate（非空参数值不合适） |
|      IllegalStateException      | Object state is inappropriate for method invocation（对象状态不适用于方法调用） |
|      NullPointerException       | Parameter value is null where prohibited（禁止参数为空时仍传入 null） |
|    IndexOutOfBoundsException    | Index parameter value is out of range（索引参数值超出范围）  |
| ConcurrentModificationException | Concurrent modification of an object has been detected where it is prohibited（在禁止并发修改对象的地方检测到该动作） |
|  UnsupportedOperationException  |    Object does not support method（对象不支持该方法调用）    |

请记住，异常是可序列化的。如果没有充分的理由，不要编写自己的异常类。

### 【73】抛出能用抽象解释的异常

**高层应该捕获低层异常，并确保抛出的异常可以用高层抽象解释。** 这个习惯用法称为异常转换

如果不可能从低层防止异常，那么下一个最好的方法就是让高层静默处理这些异常，使较高层方法的调用者免受低层问题的影响。在这种情况下，可以使用一些适当的日志工具（如 `java.util.logging`）来记录异常。这允许程序员研究问题，同时将客户端代码和用户与之隔离。

### 【74】为每个方法记录会抛出的所有异常

**始终单独声明 checked 异常，并使用 Javadoc 的 `@throw` 标记精确记录每次抛出异常的条件**

特别重要的是，接口中的方法要记录它们可能抛出的 unchecked 异常。此文档构成接口通用约定的一部分，并指明接口的多个实现之间应该遵守的公共行为。

**使用 Javadoc 的 `@throw` 标记记录方法会抛出的每个异常，但是不要对 unchecked 异常使用 throws 关键字。**

### 【75】异常详细消息中应包含捕获失败的信息

**要捕获失败，异常的详细消息应该包含导致异常的所有参数和字段的值** **不应包含密码、加密密钥等详细信息。**

### 【76】尽力保证故障原子性

**一般来说，失败的方法调用应该使对象处于调用之前的状态。** 具有此属性的方法称为具备故障原子性

- 不可变对象
- 在执行操作之前检查参数的有效性
- 对计算进行排序，以便可能发生故障的部分都先于修改对象的部分发生。
- 以对象的临时副本执行操作，并在操作完成后用临时副本替换对象的内容。



### 【77】不要忽略异常

**空 catch 块违背了异常的目的，**如果你选择忽略异常，catch 块应该包含一条注释，解释为什么这样做是合适的，并且应该将变量命名为 ignore

##  第 11 章 并发

### 【78】对共享可变数据的同步访问

**线程之间能可靠通信以及实施互斥，同步是所必需的。** 这是由于语言规范中，称为内存模型的部分指定了一个线程所做的更改何时以及如何对其他线程可见

- **不要使用 `Thread.stop`**
- **除非读和写操作都同步，否则不能保证同步工作**
- **应当将可变数据限制在一个线程中**

### 【79】避免过度同步

**为避免活性失败和安全故障，永远不要在同步方法或块中将控制权交给客户端**

**作为规则，你应该在同步区域内做尽可能少的工作**

### 【80】Executor、task、流优于直接使用线程

### 【81】并发实用工具优于 wait 和 notify

**始终使用 wait 习惯用法，即循环来调用 wait 方法；永远不要在循环之外调用它**

### 【82】文档应包含线程安全属性

**Lock 字段应该始终声明为 final。**

### 【83】明智地使用延迟初始化

延迟初始化的最佳建议是「除非需要，否则不要这样做」

**在大多数情况下，常规初始化优于延迟初始化。** 

**如果需要在静态字段上使用延迟初始化来提高性能,使用惰性初始化holder类习惯用法**

如果需要使用延迟初始化来提高实例字段的性能，请使用双重检查模式

```java
// Double-check idiom for lazy initialization of instance fields
private volatile FieldType field;
private FieldType getField() {
    FieldType result = field;
    if (result == null) { // First check (no locking)
        synchronized(this) {
            if (field == null) // Second check (with locking)
                field = result = computeFieldValue();
        }
    }
    return result;
}
```

```java
// Single-check idiom - can cause repeated initialization!
private volatile FieldType field;
private FieldType getField() {
    FieldType result = field;
    if (result == null)
        field = result = computeFieldValue();
    return result;
}
```

### 【84】不要依赖线程调度器

**任何依赖线程调度器来保证正确性或性能的程序都可能是不可移植的。**

**如果线程没有做有用的工作，它们就不应该运行。**

**`Thread.yield` 没有可测试的语义。** 更好的做法是重构应用程序，以减少并发运行线程的数量。

## 第 12 章 序列化

### 【85】Java 序列化的替代方案

序列化的一个根本问题是它的可攻击范围太大，且难以保护，而且问题还在不断增多：通过调用 ObjectInputStream 上的 readObject 方法反序列化对象图。这个方法本质上是一个神奇的构造函数，可以用来实例化类路径上几乎任何类型的对象，只要该类型实现 Serializable 接口。在反序列化字节流的过程中，此方法可以执行来自任何这些类型的代码，因此所有这些类型的代码都在攻击范围内。

攻击者和安全研究人员研究 Java 库和常用的第三方库中的可序列化类型，寻找在反序列化过程中调用的潜在危险活动的方法称为 gadget。

不使用任何 gadget，你都可以通过对需要很长时间才能反序列化的短流进行反序列化，轻松地发起拒绝服务攻击。这种流被称为反序列化炸弹

**避免序列化利用的最好方法是永远不要反序列化任何东西**

**没有理由在你编写的任何新系统中使用 Java 序列化。**

领先的跨平台结构化数据表示是 JSON 和 Protocol Buffers，也称为 protobuf.JSON 和 protobuf 之间最显著的区别是 JSON 是基于文本的，并且是人类可读的，而 protobuf 是二进制的，但效率更高；JSON 是一种专门的数据表示，而 protobuf 提供模式（类型）来记录和执行适当的用法。

如果无法避免序列化，那么可以使用 Java 9 中添加的对象反序列化筛选，并将其移植到早期版本

### 【86】非常谨慎地实现 Serializable

**实现 Serializable 接口的一个主要代价是，一旦类的实现被发布，它就会降低更改该类实现的灵活性。**

**实现 Serializable 接口的第二个代价是，增加了出现 bug 和安全漏洞的可能性**

**实现 Serializable 接口的第三个代价是，它增加了与发布类的新版本相关的测试负担**

- 为继承而设计的类很少情况适合实现 Serializable 接口，接口也很少情况适合扩展它
- 如果类的实例字段初始化为默认值,那么必须添加 readObjectNoData 方法
- 内部类不应该实现 Serializable。静态内部类可以

### 【87】考虑使用自定义序列化形式

**在没有考虑默认序列化形式是否合适之前，不要接受它。**

**如果对象的物理表示与其逻辑内容相同，则默认的序列化形式可能是合适的**

**即使你认为默认的序列化形式是合适的，你通常也必须提供 readObject 方法来确保不变性和安全性。**

**当对象的物理表示与其逻辑数据内容有很大差异时，使用默认的序列化形式有四个缺点：**

- 它将导出的API永久的绑定到当前的内部实现
- 占用过多的空间
- 占用过多的时间
- 堆栈溢出

> writeObject 做的第一件事是调用 defaultWriteObject, readObject 做的第一件事是调用 defaultReadObject，即使 StringList 的所有字段都是 transient 的。你可能听说过，如果一个类的所有实例字段都是 transient 的，那么你可以不调用 defaultWriteObject 和 defaultReadObject，但是序列化规范要求你无论如何都要调用它们。这些调用的存在使得在以后的版本中添加非瞬态实例字段成为可能，同时保留了向后和向前兼容性。如果实例在较晚的版本中序列化，在较早的版本中反序列化，则会忽略添加的字段。如果早期版本的 readObject 方法调用 defaultReadObject 失败，反序列化将失败，并出现 StreamCorruptedException。

**在决定使字段非 transient 之前，请确信它的值是对象逻辑状态的一部分。**

### 【88】防御性地编写 readObject 方法

readObject 方法实际上是另一个公共构造函数，它与任何其他构造函数有相同的注意事项。

构造函数必须检查其参数的有效性并在适当的地方制作防御性副本一样，readObject 方法也必须这样做

**当对象被反序列化时，对任何客户端不能拥有的对象引用的字段进行防御性地复制至关重要**防御副本是在有效性检查之前执行的，我们没有使用 Date 的 clone 方法来执行防御副本

指导原则:

1. 对象引用字段必须保持私有的的类，应防御性地复制该字段中的每个对象。
2. 检查任何不变量，如果检查失败，则抛出 InvalidObjectException
3. 如果必须在反序列化后验证整个对象图，那么使用 ObjectInputValidation 接口
4. 不要直接或间接地调用类中任何可被覆盖的方法。

### 【88】对于实例控制，枚举类型优于 readResolve

**如果你依赖 readResolve 进行实例控制，那么所有具有对象引用类型的实例字段都必须声明为 transient**

> 攻击有点复杂，但其基本思想很简单。如果单例包含一个非 transient 对象引用字段，则在运行单例的 readResolve 方法之前，将对该字段的内容进行反序列化。这允许一个精心设计的流在对象引用字段的内容被反序列化时「窃取」对原来反序列化的单例对象的引用。
>
> 工作原理。首先，编写一个 stealer 类，该类具有 readResolve 方法和一个实例字段，该实例字段引用序列化的单例，其中 stealer 「隐藏」在其中。在序列化流中，用一个 stealer 实例替换单例的非 transient 字段。现在你有了一个循环：单例包含了 stealer，而 stealer 引用了单例。
>
> 因为单例包含 stealer，所以当反序列化单例时，窃取器的 readResolve 方法首先运行。因此，当 stealer 的 readResolve 方法运行时，它的实例字段仍然引用部分反序列化（且尚未解析）的单例。
>
> stealer 的 readResolve 方法将引用从其实例字段复制到静态字段，以便在 readResolve 方法运行后访问引用。然后，该方法为其隐藏的字段返回正确类型的值。如果不这样做，当序列化系统试图将 stealer 引用存储到该字段时，VM 将抛出 ClassCastException。

总之，在可能的情况下，使用枚举类型强制实例控制不变量。如果这是不可能的，并且你需要一个既可序列化又实例控制的类，那么你必须提供一个 readResolve 方法，并确保该类的所有实例字段都是基本类型，或使用 transient 修饰。

### 【90】考虑以序列化代理代替序列化实例

序列化代理模式相当简单。首先，设计一个私有静态嵌套类，它简洁地表示外围类实例的逻辑状态。这个嵌套类称为外围类的序列化代理。它应该有一个构造函数，其参数类型是外围类。这个构造函数只是从它的参数复制数据：它不需要做任何一致性检查或防御性复制。按照设计，序列化代理的默认序列化形式是外围类的完美序列化形式。外围类及其序列代理都必须声明实现 Serializable 接口。

与防御性复制方法类似，序列化代理方法阻止伪字节流攻击和内部字段盗窃攻击.序列化代理模式允许反序列化实例具有与初始序列化实例不同的类。你可能不认为这在实践中有用，但它确实有用。

```java
// EnumSet's serialization proxy
private static class SerializationProxy <E extends Enum<E>> implements Serializable {
    // The element type of this enum set.
    private final Class<E> elementType;

    // The elements contained in this enum set.
    private final Enum<?>[] elements;

    SerializationProxy(EnumSet<E> set) {
        elementType = set.elementType;
        elements = set.toArray(new Enum<?>[0]);
    }

    private Object readResolve() {
        EnumSet<E> result = EnumSet.noneOf(elementType);
        for (Enum<?> e : elements)
            result.add((E)e);
        return result;
    }

    private static final long serialVersionUID =362491234563181265L;
}
```

序列化代理模式两个限制:

1. 它与客户端可扩展的类不兼容
2. 序列化代理模式所增强的功能和安全性并不是没有代价的