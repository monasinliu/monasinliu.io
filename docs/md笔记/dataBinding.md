# DataBinding 学习记录



## 构建环境

### 环境要求

* 系统版本： Android 2.1(API level 7)及以上
* Gradle 版本 ：  1.5.0-alpha1及以上
* Android Studio 版本： 1.3 以上

### 引用

在`build.gradle`文件中添加

```
//在build.gradle文件添加
android{
   dataBinding{
      enabled true
   }
}
```

> 使用kotiln时，引用该代码段将提示需要引用 plugins  'kotlin-kapt' 在插件中引用后，编译如果抛出 Null extracted folder for artifact 的错误，可以通过下载 ndk 来解决。
>
> Ndk 下载地址： https://www.androiddevtools.cn/
>
> 下载解压后，在local.properties 文件中放入本地地址如：
>
> ` ndk.dir=/Users/ruiyu/Library/Android/android-ndk-r16-beta1`

**布局文件设置**

光标在布局文件的根布局-->点击Alt + Enter-->点击 “**Convert to data binding layout**”

```xml
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
    </data>
    <!--原先的根节点（Root Element）-->
    <LinearLayout>
    ....
    </LinearLayout>
</layout>
```

数据绑定库提供了诸如 Imports, variables, 以及 includes等功能，Imports 可以轻松引用类到你的布局文件中，Variables 允许你描述可用于绑定表达式的属性，Includes 可以让你在整个应用中重复使用复杂的布局。

### imports

Imports 可以让我们轻松地在布局文件中引用类，在`data`元素内可以使用零个或多个`import`元素。下面的代码将`View`类导入到了布局文件中：

```xml
<import type="android.view.View"/>
```

**重命名类型**

当有类名冲突时，可以给其中一个类定义个**别名**。以下示例将`com.example.real.estate`包中的`View`类重命名为`Vista`：

```java
<import type="android.view.View"/>
<import type="com.example.real.estate.View" alias="Vista"/>
```

这样我们就可以使用`Vista`来引用`com.example.real.estate.View`，而`View`则可以用来在布局文件中引用`android.view.View`。

你也可以使用导入的类型来转换部分表达式。以下示例将`connection`属性转换为`User`类型：

```xml
<TextView
   android:text="@{((User)(user.connection)).lastName}"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
```

在表达式中引用静态字段和方法时，也可以使用导入的类型。下面的代码将导入`MyStringUtils`类并引用其`capitalize`方法：

```xml
<data>
    <import type="com.example.MyStringUtils"/>
    <variable name="user" type="com.example.User"/>
</data>
…
<TextView
   android:text="@{MyStringUtils.capitalize(user.lastName)}"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
```

### Variables

我们可以在`data`元素内使用多个`variable`元素，每个`variable`元素描述了可以在布局上设置的属性，以用于布局文件中的绑定表达式。

```xml
<data>
    <import type="android.graphics.drawable.Drawable"/>
    <variable name="user" type="com.example.User"/>
    <variable name="image" type="Drawable"/>
    <variable name="note" type="String"/>
</data>
```

如果一个变量实现了`Observable`或者是一个可观察的集合，那么它应该被反映在类型中（可观察）。

基本类型（`int`，`float`等）不是可观察变量。

生成的绑定类对每个描述的变量都有一个 setter 和 getter，这些变量会有一个默认值（引用类型为`null`，`int`为`0`，`boolean`为`false`等），直到 setter 被调用。

对于变量，数据绑定库在模块包中生成一个名为 `BR` 的类，其中包含用于数据绑定的资源的 ID。

如： BR.user等。

### Includes

 app 命名空间的布局中，可以将变量传递到`include`布局的绑定中

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:bind="http://schemas.android.com/apk/res-auto">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <include layout="@layout/name"
           bind:user="@{user}"/>
       <include layout="@layout/contact"
           bind:user="@{user}"/>
   </LinearLayout>
</layout>
```

数据绑定不支持 `include`直接作为`merge`的子元素。



绑定：

![image-20211116170544048](/Users/ruiyu/Library/Application Support/typora-user-images/image-20211116170544048.png)

报错：

![image-20211116170559373](/Users/ruiyu/Library/Application Support/typora-user-images/image-20211116170559373.png)

## dataBinding 事件绑定

事件可以直接被绑定到处理方法上，类似于将android：onClick分配给 activity 中的方法。与 View 的 onClick属性相比，主要优势是表达式在编译时就会处理，所以如果该方法不存在或其签名不正确，则会收到编译时错误。

方法引用和监听器绑定之间的主要区别在于实际的监听器实现是在数据绑定时创建的，而不是在事件触发时创建的。如果你希望在事件发生时 evaluate 表达式，则应使用监听器绑定。

Binding表达式可以为View添加一个ClickListener:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
   <data>
       <variable name="handlers" type="com.example.MyHandlers"/>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.firstName}"
           android:onLongClick="@{handlers::onLongClickFriend}"
           android:onClick="@{handlers::onClickFriend}"/>
   </LinearLayout>
</layout>
```

方法签名需要和Listener中的方法签名保持一致

### 监听器绑定



## 表达式语言

### databinding支持像Java一样，直接引用其他的类。

```xml
<data>
    <import type="com.example.MyStringUtils"/>
    <import type="android.view.View"/>
    <variable name="user" type="com.example.User"/>
</data>
…
<TextView
   android:text="@{MyStringUtils.capitalize(user.lastName)}"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"
   android:visibility="@{user.isAdult ? View.VISIBLE : View.GONE}"/>
```

 ### Databinding支持表达式：

可以在layout中 像Java一样使用如下的一些表达式：

* 数学表达式 `+ – / * %`

* 字符串链接 ` +`

* 逻辑操作符 `&& ||`

* 二元操作符 `& | ^`

* 一元操作符 `+ – ! ~`

* 移位操作符 `>> >>> <<`

* 比较 `== > < >= <=`

* `instance of`

* `Grouping ()`

* 字面量 – `character, String, numeric, null`

* `Cast`

* 函数调用

* 值域引用（Field access）

* 通过`[]`访问数组里面的对象

* 三元操作符`?:`

### 不支持的操作

表达式语法不支持以下操作：

- `this`
- `super`
- `new`
- 显式泛型调用`<T>`

### 空合并操作符（Null coalescing operator）

空合并运算符`(??)`：如果操作符的左操作数不为`null`就选择左操作数否则选择右操作数：

```java
android:text="@{user.displayName ?? user.lastName}"
```

功能上等同于：

```java
android:text="@{user.displayName != null ? user.displayName : user.lastName}"
```

### 属性引用

可以通过使用以下格式来引用类中的属性，对于 **fields, getters,和ObservableField对象**

```java
android:text="@{user.lastName}"
```

### 避免空指针异常

生成的数据绑定代码会自动检查`null`值并避免空指针异常。例如，在表达式`@{user.name}`中，如果`user`是空，`user.name`将默认为`null`。如果你引用了`int`类型的`user.age`，那么数据绑定使用默认值`0`；

## 可观察的数据对象

![image-20211011171324544](/Users/ruiyu/Library/Application Support/typora-user-images/image-20211011171324544.png)

以下是`基本类型`的可观察字段：

- `ObservableBoolean`
- `ObservableByte`
- `ObservableChar`
- `ObservableShort`
- `ObservableInt`
- `ObservableLong`
- `ObservableFloat`
- `ObservableDouble`
- `ObservableParcelable`

## Binding 的类

### 创建 Binding 的对象



## LiveData

LiveData 是一种可观察的数据存储器类。与常规的可观察类不同，LiveData 具有生命周期感知能力，意指它遵循其他应用组件（如 Activity、Fragment 或 Service）的生命周期。这种感知能力可确保 LiveData 仅更新处于活跃生命周期状态的应用组件观察者。

```kotlin
class TestViewModel : ViewModel() {
    var name = MutableLiveData<String>()
    var clickNum = MutableLiveData<Int>()
    fun showToast() {
        Toast.makeText(DemoApplication.mContext, "点击了文本", Toast.LENGTH_SHORT).show()
        clickNum.value = (clickNum.value ?: 0) + 1
    }

    init {
        name.value = "默认文案"
        clickNum.value = 0
    }
}
```



