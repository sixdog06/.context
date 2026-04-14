# Java 编程规范

本 skill 涵盖通用 Java 编程最佳实践与规范。写 Java 代码时，主动对照以下规则给出建议或指出问题。

---

## 一、命名风格

- 命名不能以 `_` 或 `$` 开头/结尾
- 严禁拼音与英文混合，禁止中文命名
- 类名用 `UpperCamelCase`，但 DO/BO/DTO/VO/AO/PO/UID 除外
- 方法名、参数名、成员变量、局部变量用 `lowerCamelCase`
- 常量全大写，单词间用下划线：`MAX_STOCK_COUNT`
- 抽象类以 `Abstract`/`Base` 开头；异常类以 `Exception` 结尾；测试类以 `Test` 结尾
- 数组类型：`int[] array`，不写 `int array[]`
- **POJO 类布尔字段不加 `is` 前缀**（否则 RPC 框架反序列化出错）
- 包名全小写，单数形式
- 禁止随意缩写（`AbsClass` `condi` 等）
- Service/DAO 实现类用 `Impl` 后缀；能力接口用 `-able` 形式
- 枚举类建议带 `Enum` 后缀，枚举成员全大写加下划线

**各层方法命名：**
| 操作 | 前缀 |
|------|------|
| 获取单个对象 | `get` |
| 获取多个对象 | `list`，复数结尾 |
| 统计 | `count` |
| 插入 | `save` / `insert` |
| 删除 | `remove` / `delete` |
| 修改 | `update` |

**领域模型命名：**
- 数据对象：`xxxDO`
- 数据传输对象：`xxxDTO`
- 展示对象：`xxxVO`
- 禁止命名 `xxxPOJO`

---

## 二、常量定义

- 禁止魔法值直接出现在代码中
- `long`/`Long` 赋值用大写 `L`，不用小写 `l`（易与数字 `1` 混淆）
- 不要用一个大常量类维护所有常量，按功能归类分开维护（如 `CacheConsts`、`ConfigConsts`）
- 固定范围的变量用 `enum` 定义

---

## 三、代码格式

- 大括号：左括号前不换行，左括号后换行，右括号前换行
- `if/for/while/switch/do` 与括号之间必须加空格
- 二目、三目运算符左右各一个空格
- **4 个空格缩进，禁止 tab**
- 单行不超过 120 个字符，超出换行（运算符/点号跟下文一起换行，逗号后换行）
- 单个方法不超过 80 行
- 注释 `//` 后有且仅有一个空格
- 文件编码 UTF-8，换行符 Unix 格式

---

## 四、OOP 规约

- 静态变量/方法用类名访问，不通过对象引用
- 覆写方法必须加 `@Override`
- 包装类对象比较用 `equals`，不用 `==`（Integer 缓存范围 -128~127 之外不可用 `==`）
- `equals` 调用：用常量/确定有值的对象调用，如 `"test".equals(obj)`，不反过来
- **POJO 类属性必须用包装类型**；RPC 方法返回值和参数必须用包装类型；局部变量推荐基本类型
- POJO 类不设属性默认值
- 序列化类新增属性不修改 `serialVersionUID`
- 构造方法禁止放业务逻辑，初始化逻辑放 `init` 方法
- POJO 类必须写 `toString` 方法（继承时加 `super.toString()`）
- POJO 类中同一属性禁止同时存在 `isXxx()` 和 `getXxx()`
- 循环体内字符串拼接用 `StringBuilder.append()`，不用 `+`
- 类成员访问控制从严：能 `private` 就不用 `protected`，能 `protected` 就不用 `public`
- 工具类禁止有 `public` 构造方法
- 慎用 `Object.clone()`（默认浅拷贝）

**类内方法顺序：** 公有/保护方法 > 私有方法 > getter/setter

---

## 五、集合处理

- 重写 `equals` 必须重写 `hashCode`
- `ArrayList.subList()` 结果不可强转 `ArrayList`
- 不要在 `foreach` 循环中 `remove`/`add`，用 `Iterator` 方式删除
- `Arrays.asList()` 返回的集合不支持 `add`/`remove`/`clear`
- 集合转数组用 `toArray(T[] array)`，传入 `list.size()` 大小的数组
- `HashMap` 初始化时指定容量：`(元素数 / 0.75) + 1`
- 遍历 Map 用 `entrySet`，不用 `keySet`（keySet 遍历两次）
- `ConcurrentHashMap` 的 key/value 均不允许为 null
- 泛型通配符：`<? extends T>` 只读，`<? super T>` 只写（PECS 原则）
- `Comparator` 必须满足：反对称、传递性、一致性

---

## 六、并发处理

- 线程资源必须通过线程池提供，禁止显式 `new Thread()`
- **禁止用 `Executors` 创建线程池**，用 `ThreadPoolExecutor` 显式指定参数（避免 OOM）
- 创建线程/线程池时指定有意义的线程名称
- `SimpleDateFormat` 线程不安全，不定义为 static；JDK8 用 `DateTimeFormatter`
- 高并发时优先用无锁结构；锁粒度尽量小（锁区块 > 锁方法 > 类锁）
- 多资源加锁保持一致顺序，防死锁
- 并发修改记录加锁（乐观锁用 version，冲突概率 <20% 用乐观锁）
- 定时任务用 `ScheduledExecutorService`，不用 `Timer`
- 多线程用 `ThreadLocalRandom` 替代 `Random`
- 双重检查锁中目标属性声明为 `volatile`
- `count++` 用 `AtomicInteger`；JDK8 推荐 `LongAdder`
- `ThreadLocal` 建议用 `static` 修饰

---

## 七、控制语句

- `switch` 每个 `case` 必须有 `break`/`return`，必须有 `default`
- `if/else/for/while/do` 必须使用大括号，即使只有一行
- 高并发场景避免用 `==` 作为退出条件，改用区间判断
- `if-else` 层级不超过 3 层，超过用卫语句/策略模式/状态模式
- 复杂条件判断结果赋值给有意义的布尔变量，提高可读性
- 循环体内避免定义对象、获取 DB 连接等耗时操作
- 避免取反逻辑（用 `x < 628` 而非 `!(x >= 628)`）

---

## 八、注释规约

- 类、属性、方法注释用 Javadoc `/** */`，不用 `//`
- 抽象方法必须用 Javadoc 说明功能、参数、返回值、异常
- 所有类必须添加创建者和创建日期
- 方法内单行注释用 `//`，多行用 `/* */`，与代码对齐
- 枚举类型字段必须有注释说明用途
- 注释与代码同步更新
- 谨慎注释掉代码；无用代码直接删除（有版本控制）
- 待办用 `// TODO(作者, 日期)`，错误用 `// FIXME(作者, 日期)`

---

## 九、异常处理

- 可预检查的异常（NPE、越界等）用条件判断规避，不用 `catch` 处理
- 异常不用于流程控制
- `catch` 要区分异常类型，不要大段 `try-catch` 吞掉所有异常
- 捕获异常必须处理，不能空 `catch`
- `finally` 必须关闭资源，JDK7+ 用 `try-with-resources`
- `finally` 块中禁止 `return`
- 不直接抛 `new RuntimeException()`，使用有业务含义的自定义异常
- RPC 方法返回用 `Result` 封装（含 `isSuccess()`、错误码、错误信息）
- 防 NPE：级联调用用 `Optional`；包装类型自动拆箱注意 null

---

## 十、日志规约

- 使用 SLF4J 门面，不直接用 Log4j/Logback API
  ```java
  private static final Logger logger = LoggerFactory.getLogger(Xxx.class);
  ```
- 日志文件至少保存 15 天
- 日志文件命名：`appName_logType_logName.log`
- 输出 Trace/Debug/Info 级别日志时，先用 `isXxxEnabled()` 判断，或用占位符 `{}`
  ```java
  logger.debug("Processing id: {}", id);  // 正确
  logger.debug("Processing id: " + id);   // 错误，字符串拼接有性能损耗
  ```