# DimensAuto
[![](https://img.shields.io/badge/version-1.2.0-orange?branch=master)](https://img.shields.io/badge/version-1.2.0-orange)
[![](https://img.shields.io/github/issues/developer-wgl/DimensAuto?branch=master)](https://img.shields.io/github/issues/developer-wgl/DimensAuto)
[![](https://img.shields.io/github/stars/developer-wgl/DimensAuto?branch=master)](https://img.shields.io/github/stars/developer-wgl/DimensAuto)
[![](https://img.shields.io/github/forks/developer-wgl/DimensAuto?branch=master)](https://img.shields.io/github/forks/developer-wgl/DimensAuto)
[![](https://img.shields.io/github/license/developer-wgl/DimensAuto?branch=master)](https://img.shields.io/github/license/developer-wgl/DimensAuto)

一款开发无感知、即时编译的 `dimens` 自动化转换工具、 `gradle` 插件脚本

## 背景
去年刚加入小爱，对于小米这种适配`values-nxhdpi`情况很不适应，有几点写业务的时候很不爽：
- 每次人工映射效率低；
- 更改一处值，另一处忘记更改时，UI/测试对于部分机型都不会看出问题，容易出错；
- 新同事可能并不了解此适配，项目中有很多并未适配的 `dimens` 键值，另外沟通还需要额外工作；   

<br>
其实网上对于这种自动化脚本已经很多了，而且小爱这边也是有同事写过这种类似脚本的，那我为什么还要决定在搞一个呢？

- 冲浪了很久。发现都需要单独运行，然后拷贝到项目中、每次改值重新生成，对于有强迫症、比较关心开发效率的我总是感觉很不爽（就是懒、嫌麻烦~）
<br>

所以针对以上几点，在工作之余花了几个小时写了 Android Studio 插件 [DimensPlugin](https://github.com/developer-wgl/DimensPlugin)，可在 IDE 中通过 `‘划词’` 这种形式，划取想要的 `dimens` 再使用 `快捷键` 就可以将选中的键值进行自动转换。

但是在组内推广过程中，其实还是有额外成本的和常见的插件并未有很大差别。经过和同事讨论，得到了一些建议，于是就有了此完全自动化的脚本插件，也经过小爱这边线上项目实践未出现过问题，收到同事一致好评~

说了这么多废话，这东西到底哪点方便呢？

## 与其他相比，优势在哪？

1. Gradle 插件脚本，在编译时会自动转换；
    - 编译和 `dimens` 数量有关，大数量时，也能在 10ms 以内搞定。编译时会有耗时输出；
    - 编译时会自动生成对应文件夹及文件，对项目旧版 `dimens` 兼容；
    - 编译时会有提示、警告、错误输出；
    - 可针对 `module` 单独配置，也可以全局配置；
    - 开发只需填写一份 `dimens` 无感知其他`dpi`生成；
    - 配置简单，只需要一行代码；
2. 支持自定义配置扩展，可全局配置； 
3. 脚本大小 8K，不会被编入`APP`包内；
4. 支持跨平台开发，已适配 linux、mac、window 平台；
5. 支持 `git` 等代码管理工具动态管理；


## 如何使用

使用常见设计稿中的 `xxhdpi@3x` 中的值（如果设计稿中没有@3x选项，可按照此方式处理：`xxhdpi@3x` = px值/3）放到默认 `values/dimens_auto.xml` 里面按照正常开发去使用就可以了，没了..  

其实就是把正常开发放到 `dimens.xml` 里面的键值改放到 `dimens_auto.xml` 里面（注意两个文件的 `dimens` 键不要重复哦，会有编译报错的）开发了，其他的与正常开发没有任何区别。插件会自动处理转换 `dimens_auto.xml` ，开发者只是换了个位置存放而已，所以才有了开发无感知的说法~

<p align="center">
    <img src="https://img-blog.csdnimg.cn/20200810212929667.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2d1YW5nX2xpYW5nXw==,size_16,color_FFFFFF,t_7" height="400px"/>
</p>


## 如何配置

1. 下载 [dimens_auto.gradle](./dimens_auto.gradle)
2. 在需要使用此插件的 `module` 中的 `build.gradle` 文件头部按照以下操作加入代码：

``` gradle
// module内使用此方式使用
// 若将 `dimens_auto.gradle` 以拷贝至项目根目录时（其他位置注意路径引入即可），引入方式如下：
apply from: "../dimens_auto.gradle" 
```
3. 打开项目内的 `dimens_auto.gradle` 自定义配置参数，可配置参数如下： 
`dimens_auto.gradle` 针对自己项目更改配置参数，可配置参数如下：

```gradle
    /* 参数配置 */
    def config = [
            "dimens_path"  : "",       // 要转换的 `dimens_auto.xml` 相对、`module`的路径. 
            "convert_rules": [],       // 转换规则，可自定义扩展。 如 ：["hdpi": 3/1.5,"xhdpi": 3/2,"sw600dp-xhdpi": 3/2,"nxhdpi": 3/2.75,"xxxhdpi": 3/4] 
            "convert_unit" : [],       // 要转换的单位。 如 ["dp", "dip", "sp"]
            "decimal"      : 2,        // 转换后小数点保留位数
            "init_hint"    : "",       // 文件初次创建时的提示语句
            "help_url"     : "",       // 帮助文档
            "debug"        : false
    ]
```

4. 点击 IDE “运行“ 按钮运行项目，build过程中可以看到 `dimens auto:`  日志输出。如果没有异常输出并且 `src/res` 中已经存在了你配置的 `dimens_auto` 文件则说明配置成功。

    <img src="https://img-blog.csdnimg.cn/20200810212929815.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2d1YW5nX2xpYW5nXw==,size_16,color_FFFFFF,t_70" width="500px">


## 遇到问题？

1.  何时被执行（转换）
* 自动执行：Android Studio 编译时会被默认执行   
    ![自动运行](https://img-blog.csdnimg.cn/20200810212929552.jpg)

* 手动执行：打开 `dimens_auto.gradle` 点击task 左侧按钮运行 
    <img src="https://img-blog.csdnimg.cn/20200810212929616.png" width="400px"/>

* 手动执行：打开Android Studio 打开菜单 View → Tool Windows → Gradle → Project Name → Tasks → other → dimens_auto 双击执行
    <img src="https://img-blog.csdnimg.cn/20200810212929829.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2d1YW5nX2xpYW5nXw==,size_16,color_FFFFFF,t_70" width="400px"/>

2. 不想被转换怎么办
-  dimens_auto.gradle中会有一些参数配置，并不是所有放在 `dimens_auto.xml` 的值都会被转换。默认配置为只处理`【dp、dip、sp】`，未被转换时会抛出警告。如果你不想被转换的值就是dp、dip、sp，那请看下一条

- 当前转换逻辑为处理当前module中的默认名为 `dimens_auto.xml` 的文件进行转换，所以把自己不想自动转换的值放到其他dimen文件中即可，如默认的dimens.xml


4. 原理是什么
把默认的dimens值设为1，在代码编译时会根据配置比例进行转换。
至于匹配规则通过正则编写，至于生成使用的是gradle库进行的生成。 

5. 转换需要多长时间
自动转换了5个对应的dimens文件，耗时 118 ms .   只转换 default → nxhdpi 耗时 25 ms.，根据电脑性能有关。
 
6. 只想局部转换怎么办
我写的一个Idea/Android Studio 插件，可以划词选中后按快捷键自动转换：[DimensPlugin](https://github.com/developer-wgl/DimensPlugin)


7. 目前支持哪种格式的dimens转换
目前已做适配的有以下三种。其他格式的可以自己编译看下转换后的结果，可能也会支持。
    ![支持dimens转换](https://img-blog.csdnimg.cn/20200810212929547.png)

8. 关于跨平台问题（linux/MAC/windows）
- windows 运行后不能运行，有乱码产生，已修复;
- windows 平台运行后有系统换行符（LF/CRLF）change问题，已修复;

9. 为什么是转换 `dimens_auto.xml` 不是直接转换 `dimens.xml`  呢？
如果你是全新项目，完全可以尝试更改配置文件直接转换 `dimens.xml` ，这样更省去了同事间推广、同步的成本。
对于这个问题， 其实读者应该很有疑惑。主要是出于以下几点考虑，才使用了新的 `dimens_auto.xml` 文件用于开发者填写：
-  主要是出于兼容旧版 `dimen` 文件考虑的。项目中已经现有很多 `dimen`，如果冒然直接更改还是风险的，如果你的项目确认没风险，不妨可以一试
- 出于同事接收新鲜事物的接受缓冲考虑。毕竟程序员同事如果某一天发现代码消失不见，而且看不懂新的插件运行规则还是很崩溃的。留有旧的 `dimen` 他们可以有缓冲的余地，当某一天尝到了 ”螃蟹的香味“ 也就会开心的转移阵地了
-  出于上述 `问题6` 考虑。还是需要一个 `dimen.xml` 文件用于存放不想转换的键值的

## 沟通交流
写一个开源工具和一套完整的文档实属不易。如果觉得还可以，跪求留下你的 `Star` 鼓励下~

欢迎 [issues](https://github.com/developer-wgl/DimensAuto/issues) ，留下你遇到的问题和宝贵的建议，我很愿意与你一起成长技术~

<br>

对应博客地址：https://blog.csdn.net/guang_liang_/article/details/107922973
