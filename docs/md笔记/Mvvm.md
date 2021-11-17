

Databinding或者ViewModel+LiveData+Repository 实现Mvvm

## ViewModel+LiveData+Repository 实现Mvvm

![img](https://upload-images.jianshu.io/upload_images/6444381-1561e4fc404d6f0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/884/format/webp)



##### View层：

View层由Activity/Fragment/自定义View组成。在View层中，View不做具体的数据和逻辑处理操作，View层只会将数据和事件操作和ViewModel进行绑定。

View 监听数据的变化，当ViewModel中的LiveData数据发生变化时，进行Ui的更新操作。同时，View层将事件绑定以命令的方式交给ViewModel去处理。

View层只做UI相关操作，逻辑处理数据获取等交给ViewModel。

##### ViewModel：

ViewModel是逻辑处理类，负责数据处理和View层与Model层的交互。ViewModel通过数据仓库Repository获取数据来源，处理来自View的事件命令，同时更新数据。

##### Repository：

数据仓库： 一个数据仓库负责获取同类型的数据来源。比如图书数据仓库能够获取各种条件筛选的图书数据，这份数据可以来自网络（Retrofit + OKHttp），本地Database（Room），缓存 （HashMap）等等，ViewModel在从Repository获取数据时，不需关注数据具体是怎么来的。

##### Model ：

因为有了Repository的概念，所以这里的Model的定义相对简单，就是JavaBean，即网络请求对应的实体数据。

