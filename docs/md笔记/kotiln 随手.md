

## getter setter

在kotlin中会自动生成getter和setter

```kotlin
class Account {
    var name: String = "" 
    var age: Int = 0
    var balance: Double = 0.0
}
```

相当于

```kotlin
class Account {
    var name: String = "" 
    var age: Int = 0
    var balance: Double = 0.0
    // 这种set和get方法不推荐在代码中自己手动去写
    set(vaule){
        field = value 
    }
    get() = field
}
```

如果添加了修饰符，可手动写：

```kotlin
class Account {

    private var name: String = ""
    private var age: Int = 0
    private var balance: Double = 0.0

    fun setName(name: String) {
        this.name = name
    }

    fun getName(): String {
        return this.name
    }

    fun setAge(age: Int) {
        this.age = age
    }

    fun getAge(): Int {
        return this.age
    }

    fun setBalance(balance: Double) {
         this.balance = balance
    }

    fun getBalance(): Double {
         return this.balance
    }
 }
```

如果不加修饰符的情况下，推荐使用第一种方案，kotlin对象中的属性默认为public所修饰。