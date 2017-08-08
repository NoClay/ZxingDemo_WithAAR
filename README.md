# 如何将zxing导入到Android Studio项目

我们这里新建一个demo来示范导入的动作！

首先这里我们所依赖的是一个zxing的[项目：](https://github.com/NoClay/ZXingDemo.git)

# 建立Module导入zxing

## 1. 下载这个zxing项目源码

这个项目中有一些文件是我们所需要的，所以我们进行下载后解压放到某个文件夹

## 2. 创建一个新的module

具体的创建方法是File > new > new Module > Android Library

![图片](http://storage1.imgchr.com/Ehv8O.png)

将对应的文件直接放置到对应的我们创建的module的文件夹中，比如src底下的，

1. raw下的beep.ogg。音频文件CaptureActivity中使用，若不想用则可以自行修改CaptureActivity
2. value 下的attrs文件
3. value下的ids.xml文件 
4. value下的string内容
5. value下的color内容
6. layout下的Activity_scanner 等复制到你的layout下

建议的是com.google.zxing底下的文件全部都复制到对应的java目录底下，res底下直接复制粘贴到对应的res底下，复制并替换，因为这是我们新建的module，所以完全不要有负担，直接复制粘贴即好。

## 3. 修改AndroidManifest文件(module的)

需要加上对应的权限：

```xml
    <uses-permission android:name="android.permission.VIBRATE" /> <!-- 震动权限 -->
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-feature android:name="android.hardware.camera" /> <!-- 使用照相机权限 -->
    <uses-feature android:name="android.hardware.camera.autofocus" /> <!-- 自动聚焦权限 -->

```

**当然因为Android6.0之后的动态权限申请的缘故，我们需要尝试手动去申请照相机权限，不然也是不行的。**

注册对应的活动：

```xml
    <application
        android:allowBackup="true"
        android:label="@string/app_name"
        android:supportsRtl="true">
        <activity
            android:theme="@style/AppTheme.NoActionBar"
            android:name=".activity.CaptureActivity">

        </activity>
    </application>
```

需要注意的是这里可能会发生一个错误就是toolbar的错误，所以我们需要给默认的CaptureActivity设置一个没有ActionBar的主题。

## 4. 添加依赖

具体的步骤是: File > Project Structure

![structure](http://storage1.imgchr.com/Ehx2D.png)

点击app ， dependencies：

![dependencies](http://storage1.imgchr.com/Ehzxe.png)

直接搜索添加依赖如下：

```xml
	compile 'com.google.zxing:core:3.3.0'
    compile 'com.google.zxing:android-core:3.3.0'
    compile 'com.google.zxing:android-core:3.3.0'
```

## 5.在app主module中添加一个module依赖

添加依赖后如下：

```xml
dependencies {
    compile fileTree(include: ['*.jar'], dir: 'libs')
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:26.+'
    compile 'com.android.support.constraint:constraint-layout:1.0.2'
    testCompile 'junit:junit:4.12'
    compile project(':zxing')
}
```

# 利用aar导入

博主在制作demo的时候，利用module导出了一个aar文件，整个项目放在git上：

https://github.com/NoClay/ZxingDemo_WithAAR

在aar文件夹下选择一个aar文件下载

## 1.利用aar文件创建一个module

具体的操作是: File > new Module > import .jar / .aar package

![选择](http://storage1.imgchr.com/E49rd.png)

## 2. 添加module依赖到项目中

```java
    compile project(':zxing-debug')
    compile 'com.google.zxing:core:3.3.0'
    compile 'com.google.zxing:android-core:3.3.0'
    compile 'com.google.zxing:android-integration:3.3.0'
```

这里还需要加入的是zxingcore的依赖，因为module是依赖于这些核心依赖的，没办法，(￣▽￣)~*

## 3. 如何使用

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        startActivityForResult(new Intent(this, CaptureActivity.class), 0);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (resultCode == RESULT_OK) { //RESULT_OK = -1
            Bundle bundle = data.getExtras();
            String scanResult = bundle.getString("result");
            Toast.makeText(MainActivity.this, scanResult, Toast.LENGTH_LONG).show();
        }
        super.onActivityResult(requestCode, resultCode, data);
    }
}
```

这是一个简单粗暴的例子。

具体的使用请参考博客：

[zxing二维码扫描]:http://www.jianshu.com/p/7d422c2c6c9b
[zxing解析]:http://www.jianshu.com/p/f862b73d07f7
[本例子的git地址]:https://github.com/NoClay/ZxingDemo_WithAAR
