# react-mpa-mobile-template

***注意：本项尚处于开发中，请先添加到浏览器收藏夹，过几天再过来看。***

## 项目说明

这是一个根据本人实际开发经验，建立的具有以下特点/解决了以下痛点的React移动端项目模板：

### 1、单页应用+多页应用 => SEO友好

对搜索引擎来说是多页应用（***SEO***友好）。

对用户来说是单页应用，不论从多页中的哪一个页面为入口访问项目，都可以拥有单页应用的使用体验）。

### 2、支持打APP本地H5资源包和远程wap站H5资源包

可根据路由配置同时打APP本地H5资源包和远程wap站H5资源包（建议将使用频率较高的页面直接打包到APP本地，将使用频率较低的如协议页等页面放置于服务器端，以便在尽可能不影响用户体验的前提下减小APP资源包的大小）。

***如果你不需要区分APP场景和wap站场景，配置路由时统一使用同一种配置即可。***

### 3、强制性使用统一的node包管理工具

如果团队成员在开发同一个项目时，各自使用不同的node包管理工具，会造成潜在的包版本风险，具体可以参考这篇文章：[浅谈项目中node和依赖的node包的版本管理](http://www.orzzone.com/version-management-node-package.html)。所以本项目会根据用户在`config/index.js`文件的`nodePackageManager`字段的值（`yarn`或者`npm`，默认是`yarn`）来 ***强制性约束*** 团队成员使用yarn或者npm来进行node包的版本管理。下面举两个强制性约束的例子：

1.如果`nodePackageManager`的值为`yarn`，那么在你运行`yarn start`等命令时，如果项目根目录存在`package-lock.json`这种只有使用`npm`作为包管理器时才会生成的文件的话，`yarn start`等进程将会直接结束。

2.如果`nodePackageManager`的值为`npm`，那么在你运行`npm run start`等命令时，如果项目根目录存在`yarn.lock`这种只有使用`yarn`这种只有使用`yarn`作为包管理器时才会生成的文件的话，`npm run start`等进程将会直接结束。

### 4、支持页面性能/接口性能、页面脚本异常/接口异常信息上报

这是一个高级配置项，若要开启，需要开发者配置以下数据：
- 性能/异常信息上报的上报率（设置的值需在[0, 1]闭区间内，若设置为0则表示关闭对应上报功能，注意这里除了直接写死一个上报率，你也可以传入一个函数，函数会提供三个入参，依次为回调函数、jsonp方法、axios实例，你可以发送用jsonp或axios来发送请求到服务端拉取云端的配置信息，然后调用回调函数动态返回上报率数值——用户本地会存有一份上报率数值和存入本地时的客户端时间戳，每次成功更新用户本地上报率数值后两小时内不会尝试重新通过接口更新上报率数值）；
- 上报接口地址；
- 上报接口的请求方式（GET+JSON, GET+JSONP, POST+JSON）;
- 转换header头部信息的中间件函数（可以自定义请求的头部信息）；
- 转换请求参数的中间件函数（项目默认可提供的信息已经列在下面了，你可以自行选择其中你需要的信息，并且在这个函数将字段名修改为服务端实际需要的字段名，将字段值修改为与服务端约定好的格式）。

***性能上报和异常上报时均会提供的信息有：***
- 浏览器UA；
- 屏幕尺寸；
- 上报时间（注意这个时间是客户端本地时间，并不表示真实时间，只表示用户在所用移动设备上设置的时间，服务端需要自行添加一个服务端时间作为真实时间数据）；
- 当前页面url。

***页面性能上报时可提供的其他信息有：***
- 页面DOM加载完毕所花时间；
- 页面组件渲染完毕所花时间；
- 页面初始图片加载完毕所花时间（延迟加载的图片等不计入内，因为不影响初始页面渲染）；

***接口性能上报时刻提供的其他信息有：***
- 接口地址；
- 请求开始时间；
- 请求结束时间；
- 请求总耗时；

***页面脚本异常上报时可提供的其他信息有：***
- 脚本错误信息（描述信息、脚本文件名、代码行列数）；

***接口异常上报时可提供的其他信息有：***
- 接口地址；
- 入参；
- 接口返回数据；
- 请求开始时间；
- 请求结束时间；
- 服务器响应状态码（200、400、404、500等）；
- 客户端设置的最大请求等待时间；
- 是否超时（是否超过客户端设定的最大请求等待时间）；

***一些说明：***

- 上报率：在用户访问量较大的情况下，如果所有性能/异常情况都进行上报显然是不现实的，对服务端的请求量也会激增，白白浪费带宽，所以需要设置一个上报率。建议只在公司内部测试阶段或者用户量很小的情况下才将上报率设置为1。记住，生产环境不是用来进行测试的地方。

- 接口异常时是否上报接口性能数据：在接口异常和接口性能开关都开启的情况下（即上报率>0），若接口存在报错则只会 ***“尝试”*** 上报接口异常信息，不论是否命中上报率成功上报，都不会再尝试发送接口性能数据。

- 不发送上报接口的性能、异常信息：不对上报接口进行性能、异常数据上报。

- 容错处理：对性能和异常上报代码做了try...catch处理，避免因为这些代码报错影响实际业务功能正常进行。


## 模版使用方式

这里用npm进行示例，你当然可以选择用yarn，请自行决定。

```bash
#1 全局安装djax-cli命令行工具（安装完后在命令行中可以使用djax命令）
npm install -g djax-cli

#2 用djax-cli来拉取本项目模版文件（将“<project-name>”替换为你自己的项目名）
djax init react-mpa-mobile-template <project-name>

#3 进入项目根目录
cd <project-name>

#4 安装项目依赖（你也可以使用yarn）
npm install

#4 本地开发
yarn start

#5 打包
yarn build
```

## 免责声明

你可以随意使用本项目代码，但是由此产生的问题请自行负责。
