# EasyPermissionsActivity

## 项目介绍
超级简单的 Android6.0动态权限申请程序

Github地址：https://github.com/lhm0603/EasyPermissionsActivity

Gitee 地址：https://gitee.com/hmOS/EasyPermissionsActivity

### 使用说明

EasyPermissionsActivity 支持 Android 4.0及更高版本，但只有在 Android6.0动态权限 Api 才有效。

## 开始使用

### 引入 EasyPermissionsActivity

这里告诉你如何在项目中引入 EasyPermissionsActivity

#### 使用 Android Studio 或者其他 Gradle 构建的项目

如果您使用Gradle构建，只需将以下行添加到文件的`dependencies`部分`build.gradle`：

```groovy
dependencies {
    //其他依赖
	implementation 'com.ds.lhm:easypermissionsactivity:1.0.0'
}
```

### 使用 EasyPermissionsActivity

![](https://raw.githubusercontent.com/lhm0603/FigureBed/master/image_20190124191501.png)

如果你仅仅是想把所有需要动态申请的权限开启，那么您只需要简单使用 EasyPermissionsActivity 即可。

在AndroidMainifest.xml 文件中加入你的应用程序需要使用到的权限

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest package="com.lin.example.epa"      				
	xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
    <uses-permission android:name="android.permission.CALL_PHONE"/>
    <uses-permission android:name="android.permission.WRITE_CALL_LOG"/>

    <application>
    </application>
</manifest>
```

在您需要动态权限申请的 Activity 中，让该 Activity 继承 EasyPermissionsActivity(继承自AppCompatActivity)，然后在需要动态权限申请的时刻(如:onCreate()方法中)，调用 `super.requestPermissions();` EasyPermissionsActivity 会自动帮您完成所有需要动态的权限的申请！

```java
public class MainActivity extends EasyPermissionsActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //开始请求权限
        super.requestPermissions();
    }
}

```

你一定关心权限申请成功或者失败的回调方法，很简单，你可以在 EasyPermissionsActivity 的派生类中重写以下方法

```java
    @Override
    protected void onRequestPermissionsBefore() {
        //权限请求之前,该方法被调用
    }

    /**
     * 权限请求之后
     *
     * @param success true 成功/ false失败
     */
    @Override
    protected void onRequestPermissionsAfter(boolean success) {
        if (success) {
            setContentView(R.layout.activity_main);
            //do something...
        } else {
            Toast.makeText(this, "还有权限没有请求到!", Toast.LENGTH_SHORT).show();
            //do something...
        }
    }
```

> 注意：
>
> ​	如果一个应用程序先前已经请求并被授予 READ_EXTERNAL_STORAGE 权限，然后它再请求WRITE_EXTERNAL_STORAGE (同属于 STORAGE 权限组)，系统会立即授予该权限，不会再弹出权限授予询问的对话框。当然，这您不需要操心，EasyPermissionsActivity 已经帮您都做好了。
>
> 如果您不清楚哪个权限属于哪个组，可以查阅 EasyPermissionsActivity 源码，我里面有将每个权限组的权限列出来。或者您也可以翻阅 [google API](https://developer.android.com/guide/topics/permissions/requesting.html#normal-dangerous) 。

### 添加权限需求描述

在 `super.requestPermissions();` 之前，使用 `super.addPermissionGroupDescription(@PermissionGroup String permissionGroup, String description)` 方法 添加权限用途描述

```java
//添加权限使用描述
super.addPermissionGroupDescription(EasyPermissionActivity.STORAGE, "下载书籍，节省流量。");
super.addPermissionGroupDescription(EasyPermissionActivity.LOCATION, "获取位置信息，智能推荐。");
super.addPermissionGroupDescription(EasyPermissionActivity.PHONE, "检验IMEI码，保证账号安全，防止账号被盗。");
//开始请求权限
super.requestPermissions();
```

### 修改权限提示名称

如果您有更好的权限提示名称，可以在`super.requestPermissions();` 之前，调用 `updatePermissionGroupName(@PermissionGroup String permissionGroup, String name)` 方法 进行修改

```java
//修改权限提示名称
super.updatePermissionGroupName(EasyPermissionActivity.STORAGE, "文件存储");
super.updatePermissionGroupName(EasyPermissionActivity.LOCATION, "位置信息");
//开始请求权限
super.requestPermissions();
```

### 无限(必须)请求权限，直到所有权限都通过

如果你的 App 需要获取所有权限后方可运行，则您可以在`super.requestPermissions();` 之前，调用 `super.isRequestAgain(true);` 方法 开启无限模式（即：没有取消按钮，只有所有权限都被允许后才会调用 `onRequiresPermissionsAfter`）。

```java
//开启无限请求权限模式
isRequestAgain(true);
//开始请求权限
super.requestPermissions();
```

## Example 完整的代码：

```java

public class MainActivity extends EasyPermissionsActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //添加存储权限使用用途描述
        super.addPermissionGroupDescription(EasyPermissionActivity.STORAGE, "下载书籍，节省流量。");
        super.addPermissionGroupDescription(EasyPermissionActivity.LOCATION, "获取位置信息，智能推荐。");
        super.addPermissionGroupDescription(EasyPermissionActivity.PHONE, "检验IMEI码，保证账号安全，防止账号被盗。");
        super.updatePermissionGroupName(EasyPermissionActivity.STORAGE, "文件存储");
        super.updatePermissionGroupName(EasyPermissionActivity.LOCATION, "位置信息");
        //开启无限请求权限模式
        isRequestAgain(true);
        //开始请求权限
        super.requestPermissions();
    }

    @Override
    protected void onRequestPermissionsBefore() {
        //权限请求之前,该方法被调用
    }

    /**
     * 权限请求之后
     *
     * @param success true 成功/ false失败
     */
    @Override
    protected void onRequestPermissionsAfter(boolean success) {
        if (success) {
            setContentView(R.layout.activity_main);
            //do something...
        } else {
            Toast.makeText(this, "还有权限没有请求到!", Toast.LENGTH_SHORT).show();
            finish();
        }
    }
}

```

#### 许可证

```
Copyright 2018 Huaming Lin.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```