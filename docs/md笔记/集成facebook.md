**build.gradle (Project)** 文件中 `buildscript { repositories {}}` 代码段中 添加：

```
mavenCentral()
```

**build.gradle (Module: app)** 文件中， `dependencies{}`中添加：

```
implementation 'com.facebook.android:facebook-android-sdk:latest.release'
```

 `/app/res/values/strings.xml` 文件中，添加你的actual app id：

```
<string name="facebook_app_id">[APP_ID]</string>
<string name="fb_login_protocol_scheme">fb[APP_ID]</string>
```

**AndroidManifest.xml** 文件中，`application` 中添加 `meta-data` 元素

```

<application android:label="@string/app_name" ...>
    ...
    <meta-data android:name="com.facebook.sdk.ApplicationId" android:value="@string/facebook_app_id"/>
    ...
</application>

```

添加权限：

```
<uses-permission android:name="android.permission.INTERNET"/>
```

