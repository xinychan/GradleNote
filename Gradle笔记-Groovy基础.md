# Groovy 基础

Groovy 是构建在 JVM 上的一门的动态语言。

Groovy 语法与 Java 语法类似, 可以完全的用 Java 的方式来编写 Groovy 的代码，也能使用 Groovy 的自身特性来编写 Groovy 代码，相对于 Java, 它在编写代码的灵活性上有非常明显的提升。

## 基本语法

- 注释标记和 Java 一样，支持单行和多行注释
- 代码编写完成，行末不需要加分号结尾
- 方法体中，可以不用 return 关键字返回结果，默认最后一行作为返回值
- 方法调用的时候可以和 Java 一样，使用括号添加参数，也支持不使用括号直接使用参数在写在方法后面

## 数据类型

Groovy 能使用 Java 中所有的类型，包括基本类型和对象类型；

但 Groovy 的特点之一是所有的类型都是对象类型，在使用 Java 中的基本类型时，其类型是 Java 中的装箱类型；

比如 int 类型变量，实际使用的是 Java 中的 Integer 类型；

Groovy 支持强类型定义方式，也支持弱类型定义方式；

    // 强类型定义
    // 此时变量 x 类型为 Integer，且类型不可变
    int x = 10

    // 弱类型定义
    // 使用关键字 def 定义弱类型，其类型可变，可为其动态的设置不同类型
    // def 关键字，通常也用来定义闭包，以及方法的返回值
    def mydef = 11 // 此时为 Integer
    mydef = 3.1415 // 此时为 BigDecimal
    mydef = "Groovy" // 此时为 String

## 字符串

**字符串常用的三种定义方式**

使用单引号定义：无格式字符串；需要自己用转义符制定格式
使用双引号定义：可扩展字符串；通过\${}扩展任意表达式
使用三引号定义：带格式的字符串；会展示引号中的原格式

**String 与 GString**

Groovy 中字符串分 String 与 GString 类型，这两种类型使用时可以互相转换
使用单引号定义，与使用三引号定义的字符串，是 String 类型
使用双单引号定义，没有使用表达式的字符串，是 String 类型
使用双单引号定义，使用表达式的字符串，是 GString 类型

**字符串的使用**

    // 无格式字符串
    def name = '单引号String'
    // 带格式的字符串
    def tname = '''
    三引号
    String'''
    // 可扩展字符串；通过${}扩展任意表达式
    // 可以使用表达式，使用表达式后类型为GStringImpl
    // 不使用表达式类型仍然为String
    def dname = "双引号String"
    def sayName = "name:${name.length()}"

## 逻辑控制

**if/else**
if/else 的使用与 Java 中的 if/else 没有区别

**switch/case**
groovy 中的 switch/case 可以传入任意变量进行控制判断

    // switch/case 的用法
    def x = 1.23
    def result
    // groovy 中的 switch/case 可以传入任意变量进行控制判断
    switch (x) {
        case 'foo':
            result = 'result foo'
            break
        case 'bar':
            result = 'result bar'
            break
        case [1.23, 4, 5, 6, 'list']:// 是否在集合中
            result = 'list'
            break
        case 12..30: // 是否在区间
            result = 'range'
            break
        case Integer:
            result = 'Integer'
            break
        case BigDecimal:
            result = 'BigDecimal'
            break
        default: result = 'default'
    }

**for/while 循环**
for/while 循环可以沿用 Java 中的方式进行使用
但是因为 Groovy 中有对数据容器类的简化处理，在 for 循环上有一定的简化操作

    // for循环使用
    // 范围循环
    def sum = 0
    for (i in 0..10) {
        sum += i
    }
    println sum
    // 集合循环
    sum = 0
    for (i in [1, 2, 3]) {
        sum += i
    }
    println sum
    // map循环
    sum = 0
    for (i in ['z1': 1, 'z2': 3, 'z3': 5]) {
        sum += i.value
    }
    println sum

## 闭包

Groovy 中有一种特殊的类：Closure
其包含的内容是一段可执行的代码

闭包的定义：可直接使用{}定义闭包
闭包的调用：closure.call()/closure()

    // 闭包的定义和调用
    def closure = {
        println "Hello Closure"
        "closure"
    }
    closure()

    // 带参数的闭包
    def closure2 = { String name -> println "Hello ${name}" }
    closure2.call("groovy")

    // 闭包的返回值
    def result = closure.call()
    println result
    // 闭包的最后一行代码为返回值，若最后一行代码没有类型，则返回null

**闭包的三个重要变量：this,owner,delegate**

_普通情况下使用闭包_

    def testClosure = {
        println "testClosure this:${this}" // 代表闭包定义处的类
        println "testClosure owner:${owner}" // 代表闭包定义处的类或者对象
        println "testClosure delegate:${delegate}" // 代表任意对象，默认与owner一致
    }
    testClosure.call()
    // 此时 this/owner/delegate 都指向closureadvance这个脚本对象
    testClosure this:closureadvance.closureadvance@48e4374
    testClosure owner:closureadvance.closureadvance@48e4374
    testClosure delegate:closureadvance.closureadvance@48e4374

_闭包中定义闭包_

    def outClosure = {
        def inClosure = {
            // 不能再调用 ${owner}/${delegate}，否则会无限循环调用自身
            // println "actionClosure this:${delegate}"
            println "inClosure this:" + this
            println "inClosure owner:" + owner
            println "inClosure delegate:" + delegate
        }
        inClosure.call()
    }
    outClosure.call()
    // 此时 this 代表当前脚本类 closureadvance 的实例对象
    //inClosure this:closureadvance.closureadvance@72a7c7e0
    // 此时 owner 代表当前脚本类 closureadvance 的内部 outClosure
    //inClosure owner:closureadvance.closureadvance$_run_closure1@19bb07ed
    // 此时 delegate 默认与 owner 一致
    //inClosure delegate:closureadvance.closureadvance$_run_closure1@19bb07ed

_修改 delegate_

    class GroovyDemo {}
    GroovyDemo demo = new GroovyDemo()
    def outClosure = {
        def inClosure = {
            println "inClosure this:" + this
            println "inClosure owner:" + owner
            println "inClosure delegate:" + delegate
        }
        // 修改默认的delegate
        inClosure.delegate = demo
        inClosure.call()
    }
    outClosure.call()
    // 此时 this 代表当前脚本类 closureadvance 的实例对象
    //inClosure this:closureadvance.closureadvance@13e39c73
    // 此时 owner 代表当前脚本类 closureadvance 的内部 outClosure
    //inClosure owner:closureadvance.closureadvance$_run_closure1@1700915
    // 此时 delegate 代表 GroovyDemo 类的实例对象
    //inClosure delegate:closureadvance.GroovyDemo@21de60b4

闭包 this/owner/delegate 总结：

1. 大多数情况下，this/owner/delegate 值是相同的
2. 在闭包中使用闭包时，this 与 owner 不同
3. 修改 delegate 的值，owner 与 delegate 不同
4. 闭包中 this/owner 不可修改，delegate 可任意修改

**闭包委托策略**

    class Student {
        String name
        def stuClosure = { "my name is ${name}" }

        @Override
        String toString() {
            return stuClosure.call()
        }
    }

    class Teacher {
        String name
    }

    def stu = new Student(name: "stu1")
    def tea = new Teacher(name: "tea1")
    // 直接展示stu1名称
    // println stu.toString()
    // 修改委托策略，展示tea1名称
    stu.stuClosure.delegate = tea
    stu.stuClosure.resolveStrategy = Closure.DELEGATE_FIRST
    println stu.toString()
    // 优先找 DELEGATE；先从 delegate 中找 name 属性，没有则从 OWNER 中找
    //stu.stuClosure.resolveStrategy = Closure.DELEGATE_FIRST
    // 只找 DELEGATE；只从 delegate 中找 name 属性，没有找到则报错
    // 若Teacher中没有 name 属性（比如改成name1），则报错：groovy.lang.MissingPropertyException
    //stu.stuClosure.resolveStrategy = Closure.DELEGATE_ONLY
    // 优先找 OWNER；
    //stu.stuClosure.resolveStrategy = Closure.OWNER_FIRST
    // 只找 OWNER；只从 OWNER 中找 name 属性，没有找到则报错
    //stu.stuClosure.resolveStrategy = Closure.OWNER_ONLY

## 数据容器类

Groovy 中的容器类有三种：

- List：链表，其底层对应 Java 中的 List 接口，一般用 ArrayList 作为真正的实现类。
- Map：键-值表，其底层对应 Java 中的 LinkedHashMap。
- Range：范围，它其实是 List 的一种拓展。

### List

    /**
    * 列表的定义
    */
    def list = new ArrayList()
    def list2 = [1, 2, 3, 4]
    println list2.class
    // java.util.ArrayList
    // 数组定义
    def list3 = [1, 2, 3, 4] as int[]
    int[] list4 = [1, 2, 3, 4]

    /**
    * list添加元素
    */
    def list = [1, 2, 3, 4]
    list.add(5)
    list.leftShift(6)
    list << 7
    println list.toListString()
    def plusList = list + 8
    println plusList.toListString()

    /**
    * list删除元素
    */
    def list = [1, 2, 3, 4, 5, 6, 7, 8]
    list.remove(0) // 移除指定下标的数据
    list.remove((Object) 7) // 移除指定元素
    list.removeAt(0) // 移除指定下标的数据
    list.removeElement(5) // 移除指定元素
    // 移除满足条件的数据
    list.removeAll {
        it % 2 == 0
    }
    def subList = list - [3,4] // 删除指定元素
    println subList.toListString()

    /**
    * 列表的排序
    */
    def sortList = [1, 3, 5, -6, 0, -9, 8]
    Collections.sort(sortList) // 升序排列
    sortList.sort() // 升序排列
    // 自定义排序规则
    sortList.sort { // 按绝对值从大到小排列
        a, b ->
            a == b ? 0 : Math.abs(a) < Math.abs(b) ? 1 : -1
    }
    println sortList.toListString()

    // 字符串排序
    def sortStrList = ['aac', 'ez', 'groovy']
    sortStrList.sort {
        it.size()
    }
    println sortStrList

    /**
    * 列表的查找
    * */
    def intList = [1, 3, 5, -6, 0, -9, 8]
    // 满足条件的第一个元素
    int result =  intList.find {
        it % 2 == 0
    }
    // 满足条件的所有元素
    def result = intList.findAll {
        it % 2 != 0
    }
    // 判断某个元素是否满足条件
    def result = intList.any {
        it % 2 != 0
    }
    // 判断里面所有元素是否满足条件
    def result = intList.every {
        it % 2 != 0
    }
    // 最大值最小值
    println intList.min()
    println intList.max()
    // 统计
    def result = intList.count {
        it > 0
    }
    println result

### Map

    /**
    * Map的定义
    * 注：Map的key一般用不可变String（单引号字符串）或者数字
    * 字符串未使用单引号时，编译时会编译成单引号字符串
    * */
    def colors = [red: 'ff0000', green: '00ff00', blue: '0000ff']
    // 获取元素
    println colors['red']
    println colors.red
    // 添加相同类型元素
    colors.yellow = '00ffff'
    // 添加不同类型元素
    colors.newMap = [name: 'groovy', feature: 'easy']
    println colors.toMapString()
    println colors.getClass()
    // 默认使用：java.util.LinkedHashMap
    // 删除元素
    colors.remove('red')
    println colors.toMapString()

    /**
    * Map操作详解
    * */
    def students = [1: [name: 's1', score: 50, sex: 'man'],
                    2: [name: 's2', score: 60, sex: 'woman'],
                    3: [name: 's3', score: 70, sex: 'man'],
                    4: [name: 's4', score: 80, sex: 'woman']]
    // Map遍历
    students.each { def student ->
        println "key is ${student.key},value is ${student.value}"
    }

    students.eachWithIndex { def student, int index ->
        println "index is $index,key is ${student.key},value is ${student.value}"
    }

    // Map查找
    def entry = students.findAll { def student ->
        return student.value.score >= 60
    }
    println entry
    println entry.size()
    def entry2 = students.find { def student ->
        return student.value.score >= 60
    }
    println entry2
    // 依据条件统计，返回size
    def count = students.count {
        it.value.score >= 60
    }
    println count
    // 依据条件统计，返回list
    def names = students.findAll { def student ->
        student.value.score >= 60
    }.collect {
        it.value.name
    }
    println names.toListString()
    // 对数据分组
    def group = students.groupBy { def student ->
        student.value.score >= 60 ? 'PASS' : 'FAIL'
    }
    println group.toMapString()

    /**
    * 排序
    * */
    def sort = students.sort { def student1, def student2 ->
        Number score1 = student1.value.score
        Number score2 = student2.value.score
        if (score1 == score2) {
            return 0
        }
        if (score1 > score2) {
            return -1
        }
        return 1
    }
    println sort.toMapString()

### Range

    /**
    * Range 定义
    * 使用不带空格的连续两个小数点定义Range
    */
    def range = 1..10
    println range[0]
    println range.contains(10)
    println range.from
    println range.to
    // Range 继承自 List，本质上是一个List

    // 遍历
    range.each {
        println it
    }

    for (index in range) {
        println index
    }

    def getGrade(Number number) {
        def result
        switch (number) {
            case 0..<60:
                result = 'Fail'
                break
            case 60..<80:
                result = 'PASS'
                break
            case 80..100:
                result = 'GOOD'
                break
        }
        return result
    }

    println getGrade(85)

## 面向对象

### Groovy 中类与接口等定义与使用

1. groovy 的类默认 public；
2. 类中的属性自带 get/set 方法，无论是通过小数点直接调用属性，还是通过 get/set 方法调用属性，本质上都是使用 get/set 方法
3. groovy 的接口中不允许定义非 public 方法
4. 有一个比较特殊的接口类：trait

**普通类的使用**

    /**
    * groovy的类默认public
    */
    class Person {
        String name
        Integer age

        // 定义方法
        def addAge(Integer years) {
            this.age + years
        }

        /**
        * 重写了 invokeMethod 方法之后，如果方法在类中没有定义，会调用 invokeMethod 方法
        */
        def invokeMethod(String name, Object args) {
            return "the method is $name,the params is $args"
        }

        /**
        * 重写了 methodMissing 方法之后，如果方法在类中没有定义，会调用 methodMissing 方法
        * 注：methodMissing 方法的调用，优先于 invokeMethod 方法
        */
        def methodMissing(String name, Object args) {
            return "the method $name is missing"
        }
    }

    /**
    * 无论是通过小数点直接调用属性，还是通过get/set方法调用属性，本质上都是使用get/set方法
    */
    def person = new Person(name: 'groovy', age: 18)
    println "name is ${person.name},age is ${person.age}"

**接口的使用**

    /**
    * 接口中不允许定义非public方法
    */
    interface Action {

        void eat()

        void drink()

        void play()

    }

**trait 的使用**

    /**
    * groovy 特有的类型
    * 1-类似接口，必须使用implements来实现
    * 2-接口中的方法都是抽象的，但trait中允许抽象方法，也允许已实现的方法
    * 方法如果是抽象的，必须要用abstract修饰
    * 相当于允许有方法实现的特殊接口
    */
    trait DefaultAction {

        abstract void eat()

        void play() {
            println 'DefaultAction play'
        }

    }

### Groovy 的元编程

**Groovy 调用类中的方法逻辑**

Java 调用类中的方法，如果类中没有该方法，会直接抛出异常；
Groovy 调用类中的方法，则要经过几重判断，才决定是否抛出异常；

Groovy 调用类中的方法逻辑：

1. 类中是否有该方法

- 是：直接调用该方法
- 否：判断 MetaClass 中是否有该方法

2. 判断 MetaClass 中是否有该方法

- 是：调用 MetaClass 中的该方法
- 否：判断是否重写了 methodMissing()方法

3. 判断是否重写了 methodMissing()方法

- 是：调用 methodMissing()方法
- 否：判断是否重写了 invokeMethod()方法

4. 判断是否重写了 invokeMethod()方法

- 是：调用 invokeMethod()方法
- 否：抛出 MissingMethodException

代码示例：

    def person = new Person(name: 'groovy', age: 18)
    println person.cry()
    // 此时虽然 Person 中没有定义 cry 方法，但是也不会报错

**MetaClass 动态的添加属性或者方法**

    /**
    * MetaClass可以为类动态的添加属性或者方法
    * */
    // 动态添加属性
    // 为该类所有实例添加属性
    Person.metaClass.sex = 'man'
    def person = new Person(name: 'groovy', age: 18)
    def person2 = new Person(name: 'Java', age: 28)
    // 为该类具体的实例类添加属性
    person.metaClass.father = 'father'
    person2.metaClass.mother = 'mother'
    // 修改属性
    person2.metaClass.sex = 'woman'

    // 动态添加普通方法
    // 动态为所有类实例添加方法，必须在实例类创建之前添加，否则方法不会被添加
    Person.metaClass.nameUpperCase = { -> name.toUpperCase() }
    def person = new Person(name: 'groovy', age: 18)
    def person2 = new Person(name: 'Java', age: 28)
    // 动态为具体实例添加方法，在实例类创建之后添加，仅对具体实例有效
    person2.metaClass.nameDouble = { -> name + name }

    // 动态添加静态方法
    Person.metaClass.static.createPerson = {
        String n, int a -> new Person(name: n, age: a)
    }
    def person3 = Person.createPerson('Kotlin', 15)

    /**
    * 注意：虽然MetaClass可以为类动态的添加属性或者方法
    * 但动态添加的属性和方法，仅在当前脚本生效，到其他地方不再生效，需要重新动态添加
    * 为了使动态添加的属性和方法在全局生效，需要使用 ExpandoMetaClass
    * */
    // 将动态添加的属性和方法设置为全局可用
    ExpandoMetaClass.enableGlobally()
    Person.metaClass.nameUpperCase = { -> name.toUpperCase() }
    // 此时 nameUpperCase 将在所有 Person 中可用

## 文件操作

Groovy 对文件操作做了不少优化，提供了很多简便的 API 可以直接使用。

### Json 处理

JsonOutput/JsonSlurper 使用：

    class JsonBean {
        String name
        int age
    }

    /**
    * Bean 转 Json
    */
    def bean1 = new JsonBean(name: 'Groovy', age: 18)
    def bean2 = new JsonBean(name: 'Java', age: 28)
    def bean3 = new JsonBean(name: 'Kotlin', age: 15)
    def list = [bean1, bean2, bean3]
    println JsonOutput.toJson(list)
    // 美化输出Json
    println JsonOutput.prettyPrint(JsonOutput.toJson(list))

    /**
    * Json字符串转Bean
    */
    String jsonText = JsonOutput.toJson(list)
    def jsonSlurper = new JsonSlurper()
    jsonSlurper.parseText(jsonText)

### XML 处理

XmlSlurper/MarkupBuilder 使用：

    final String xmlText = '''
    <response>
        <value>
            <books id='1' feature='android'>
                <book price='20' id='1'>
                    <title>Android讲义</title>
                    <author id='1'>李刚</author>
                </book>
                <book price='25' id='2'>
                    <title>第一行代码</title>
                    <author id='2'>郭霖</author>
                </book>
                <book price='30' id='3'>
                    <title>Android开发艺术探索</title>
                    <author id='3'>任玉刚</author>
                </book>
            </books>
            <books id='2' feature='ios'>
                <book price='22' id='1'>
                    <title>iOS14初探</title>
                    <author id='1'>数码博主</author>
                </book>
                <book price='26' id='2'>
                    <title>iOS14再探</title>
                    <author id='2'>任玉刚</author>
                </book>
            </books>
        </value>
    </response>
    '''

    /**
    * 解析xml数据
    */
    def xmlSlurper = new XmlSlurper()
    def response = xmlSlurper.parseText(xmlText)
    // 获取节点的值；通过具体节点名称的text()方法获取节点值
    println response.value.books[0].book[0].title.text()
    // 获取节点的属性；通过 @xxx 获取属性
    println response.value.books[0].book[1].@price

    // 遍历和查找xml节点数据
    def list = []
    response.value.books.each { books ->
        books.book.each { book ->
            def anthor = book.author.text()
            if (anthor.equals('任玉刚')) {
                list.add(book.title.text())
            }
        }
    }
    println list.toListString()

    // 深度遍历xml数据；这里获取了所有符合条件的book
    def titles = response.depthFirst().findAll { book ->
        return book.author.text().equals('任玉刚')
    }
    titles.each {
        println it.title
    }

    // 广度遍历xml数据；这里获取了所有符合条件的book
    def titles = response.value.books.children().findAll { node ->
        node.name() == 'book' && node.@id == '2'
    }.collect { node ->
        return node.title.text()
    }
    println titles

    /**
    * 生成xml格式数据
    * */

    //待生成格式：
    //<langs type='JVM'>
    //    <language feature='origin'>Java</language>
    //    <language feature='metaclass'>Groovy</language>
    //    <language feature='function'>Kotlin</language>
    //</langs>

    // 通过静态编写数据生成xml格式数据
    def writer = new StringWriter()
    def xmlBuilder = new MarkupBuilder(writer)
    xmlBuilder.langs(type:'JVM'){
        language(feature:'origin','Java')
        language(feature:'metaclass','Groovy')
        language(feature:'function','Kotlin')
    }
    println writer

    // 通过实例类生成xml格式数据
    class Language {
        String feature
        String value
    }

    class Langs {
        String type = 'JVM'
        def languages = [
                new Language(feature: 'metaclass', value: 'Groovy'),
                new Language(feature: 'origin', value: 'Java'),
                new Language(feature: 'function', value: 'Kotlin')
        ]
    }

    def writer = new StringWriter()
    def xmlBuilder = new MarkupBuilder(writer)
    def langs = new Langs()
    xmlBuilder.langs(type: langs.type) {
        langs.languages.each {
            language(feature: it.feature, it.value)
        }
    }
    println writer

### File 处理

**操作文件主要用到的 API**

- 文件读取内容：File.withReader
- 文件写入内容：File.withWriter
- 将对象写入文件：File.withObjectOutputStream
- 从文件读取对象：File.withObjectInputStream

File 操作：

    /**
    * 文件的操作
    */
    def file = new File('../../GroovyBase.iml')
    // 遍历文件的每一行
    file.eachLine {
        println it
    }
    file.readLines().each {
        println it
    }

    // 直接获取文件所有内容
    println file.getText()

    // 读取内容使用file.withReader
    def content = file.withReader { reader ->
        char[] buffer = new char[50]
        reader.read(buffer)
        return buffer
    }
    println content

    // 写内容使用file.withWriter
    def writer = file.withWriter {
        // doWithWriter
    }

    // 具体实例：复制文件
    // withReader/withWriter等方法执行后封装了关闭流的操作，不需要再手动关闭流
    static def copy(String srcPath, String desPath) {
        try {
            // 创建目标文件
            def desFile = new File(desPath)
            if (!desFile.exists()) {
                desFile.createNewFile()
            }
            // 开始复制
            def srcFile = new File(srcPath)
            def content = srcFile.getText()
            desFile.withWriter { writer ->
                content.each { line ->
                    writer.append(line)
                }
            }
        } catch (IOException e) {
            e.printStackTrace()
        }
    }
    copy('../../GroovyBase.iml', '../../GroovyBase2.iml')

**序列化和反序列化**

    class FileBean implements Serializable{
        String name
        int age
    }

    // 对象的读写
    // 将对象保存到文件
    def saveObject(Object object, String desPath) {
        try {
            // 创建目标文件
            def desFile = new File(desPath)
            if (!desFile.exists()) {
                desFile.createNewFile()
            }
            // 写入数据
            desFile.withObjectOutputStream { output ->
                output.writeObject(object)
            }
            return true
        } catch (Exception e) {
            e.printStackTrace()
        }
        return false
    }
    // 从文件中读取对象
    def readObject(String srcPath) {
        def obj = null
        try {
            def file = new File(srcPath)
            if (file == null || !file.exists()) {
                return null
            }
            file.withObjectInputStream { input ->
                obj = input.readObject()
            }
            return obj
        } catch (Exception e) {
            e.printStackTrace()
        }
        return obj
    }

    // FileBean 要先实现 Serializable 序列化接口，否则读写操作时会抛出 NotSerializableException
    def bean = new FileBean(name: 'groovy', age: 18)
    saveObject(bean, '../../filebean.bin')

    def result = (FileBean) readObject('../../filebean.bin')
    println "result name is ${result.name},age is ${result.age}"

_参考资料_
<https://www.jianshu.com/p/7af24e1982e7>
慕课网 《Gradle3.0 自动化项目构建技术精讲+实战》