# RxDownload
基于RxJava打造的下载工具, 支持多线程下载和断点续传, 智能判断是否支持断点续传等功能


标签（空格分隔）： Android  RxJava  Download Tools



---

## 基于RxJava打造的下载工具, 支持多线程和断点续传


### 效果图

![demo](https://github.com/ssseasonnn/RxDownload/blob/master/demo.gif)

### 大文件下载测试

![王者荣耀下载测试](https://github.com/ssseasonnn/RxDownload/blob/master/王者荣耀下载测试.gif)

![内存占用图](https://github.com/ssseasonnn/RxDownload/blob/master/memory.png)

> 大文件下载测试中，内存占用一直趋于平稳

### 主要功能:

- 使用Retrofit+OKHTTP来进行网络请求
- 基于RxJava打造, 支持RxJava各种操作符链式调用
- 若服务器支持断点续传, 则使用多线程断点下载
- 若不支持断点续传,则进行传统下载
- 多线程下载, 可以设置最大线程, 默认值为3线程
- 网络连接失败自动重连, 可配置最大重试次数, 默认值为3次
- 利用Java NIO 中的 MappedByteBuffer内存映射进行高效读写文件
- 流式下载，再大的文件也不会造成内存泄漏
- 支持根据Last-Modified字段判断服务端文件是否变化
- 与服务器进行验证过程中,使用更轻便的HEAD请求方式仅获取响应头,减轻通信数据量


### 下载流程图

![流程图](https://github.com/ssseasonnn/RxDownload/blob/master/download.png)


### 使用方式

1.添加Gradle依赖

[![Download](https://api.bintray.com/packages/ssseasonnn/android/RxDownload/images/download.svg)](https://bintray.com/ssseasonnn/android/RxDownload/_latestVersion)

```groovy
	dependencies{
   		 compile 'zlc.season:rxdownload:1.1.1'
	}
```

2.代码调用

```java
Subscription subscription = RxDownload.getInstance()
                .download(url, "weixin.apk", null)
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Subscriber<DownloadStatus>() {
                     @Override
                    public void onCompleted() {

                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onNext(final DownloadStatus status) {

                    }
                });
```

> download(String url, String  saveName, String savePath)参数说明:
>
> 参数分别为下载地址,保存文件名,保存地址.
>
> url与saveName为必传参数, savePath为可选参数, 默认的下载地址为/storage/emulated/0/Download/目录下, 也就是内置存储的Download目录

3.参数配置

可以配置的参数如下:

```java
Subscription subscription = RxDownload.getInstance()
                .maxThread(10)     //设置最大线程
                .maxRetryCount(10) //设置下载失败重试次数
                .retrofit(myRetrofit)//若需要自己的retrofit客户端,可在这里指定
                .defaultSavePath(defaultSavePath)//设置默认的下载路径
                .download(url,savename,savepath) //开始下载
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Subscriber<DownloadStatus>() {
                    @Override
                    public void onCompleted() {

                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onNext(DownloadStatus status) {
					//Status表示的是当前的下载进度
                    }
                });
```

4.DownloadStatus 下载状态

```java
class DownloadStatus {
    private long totalSize;
    private long downloadSize;
    public boolean isChunked = false;
    //...
    //返回文件总大小,单位为byte
    public long getTotalSize() {}

    //返回已下载总大小,单位为byte
    public long getDownloadSize() {}

    //返回格式化的总大小,如:10MB
    public String getFormatTotalSize() {}

	//返回格式化的已经下载的大小,如:5KB
    public String getFormatDownloadSize() {}

    //返回格式化的状态字符串,如:2MB/36MB
    public String getFormatStatusString() { }

    //返回下载的百分比, 保留两位小数,如:5.25%
    public String getPercent() {}
}
```

5.取消或暂停下载

```java
Subscription subscription = RxDownload.getInstance()
                .download(url, null, null)
  				//...

//取消订阅, 即可暂停下载, 若服务端不支持断点续传,下一次下载会重新下载,反之会继续下载
if (subscription != null && !subscription.isUnsubscribed()) {
            subscription.unsubscribe();
}
```

6.更多功能后续将会逐步完善

若您对此项目有疑问,欢迎来提issues.

### 关于我

若您想对该项目来进行交流,可以通过以下方式:

QQ : 270362455

Gmail: ssseasonnn@gmail.com

### License

> ```
> Copyright 2016 Season.Zlc
>
> Licensed under the Apache License, Version 2.0 (the "License");
> you may not use this file except in compliance with the License.
> You may obtain a copy of the License at
>
>    http://www.apache.org/licenses/LICENSE-2.0
>
> Unless required by applicable law or agreed to in writing, software
> distributed under the License is distributed on an "AS IS" BASIS,
> WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
> See the License for the specific language governing permissions and
> limitations under the License.
> ```