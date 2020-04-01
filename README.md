# 1. 项目指南

## 1.1 项目结构

新项目应遵循[Android Gradle插件用户指南](http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Project-Structure)中定义的Android Gradle项目结构。

## 1.2 文件名

### 1.2.1 类名

类名用[UpperCamelCase](http://en.wikipedia.org/wiki/CamelCase)编写。
对于扩展Android组件的类，该类的名称应以该组件的名称结尾；例如：`SignInActivity`，`SignInFragment`，`ImageUploaderService`，`ChangePasswordDialog`。

### 1.2.2 资源文件

资源文件名写在 __lowercase_underscore__.

#### 1.2.2.1 Drawable 文件

drawables命名约定:


| Asset Type   | Prefix            |		Example               |
|--------------| ------------------|-----------------------------|
| Action bar   | `ab_`             | `ab_stacked.9.png`          |
| Button       | `btn_`	            | `btn_send_pressed.9.png`    |
| Dialog       | `dialog_`         | `dialog_top.9.png`          |
| Divider      | `divider_`        | `divider_horizontal.9.png`  |
| Icon         | `ic_`	            | `ic_star.png`               |
| Menu         | `menu_	`           | `menu_submenu_bg.9.png`     |
| Notification | `notification_`	| `notification_bg.9.png`     |
| Tabs         | `tab_`            | `tab_pressed.9.png`         |

icons命名规范 (参考 [Android iconography guidelines](http://developer.android.com/design/style/iconography.html)):

| Asset Type                      | Prefix             | Example                      |
| --------------------------------| ----------------   | ---------------------------- |
| Icons                           | `ic_`              | `ic_star.png`                |
| Launcher icons                  | `ic_launcher`      | `ic_launcher_calendar.png`   |
| Menu icons and Action Bar icons | `ic_menu`          | `ic_menu_archive.png`        |
| Status bar icons                | `ic_stat_notify`   | `ic_stat_notify_msg.png`     |
| Tab icons                       | `ic_tab`           | `ic_tab_recent.png`          |
| Dialog icons                    | `ic_dialog`        | `ic_dialog_info.png`         |

selector states命名规范:

| State	       | Suffix          | Example                     |
|--------------|-----------------|-----------------------------|
| Normal       | `_normal`       | `btn_order_normal.9.png`    |
| Pressed      | `_pressed`      | `btn_order_pressed.9.png`   |
| Focused      | `_focused`      | `btn_order_focused.9.png`   |
| Disabled     | `_disabled`     | `btn_order_disabled.9.png`  |
| Selected     | `_selected`     | `btn_order_selected.9.png`  |


#### 1.2.2.2 Layout 文件

布局文件应与要使用的Android组件的名称匹配，但将顶级组件的名称移到开头。例如，如果我们正在为创建布局`SignInActivity`，则布局文件的名称应为`activity_sign_in.xml`。

| Component        | Class Name             | Layout Name                   |
| ---------------- | ---------------------- | ----------------------------- |
| Activity         | `UserProfileActivity`  | `activity_user_profile.xml`   |
| Fragment         | `SignUpFragment`       | `fragment_sign_up.xml`        |
| Dialog           | `ChangePasswordDialog` | `dialog_change_password.xml`  |
| AdapterView item | ---                    | `item_person.xml`             |
| Partial layout   | ---                    | `partial_stats_bar.xml`       |

稍有不同的情况是，当我们创建一个布局时，该布局将被Adapter填充，例如填充ListView。在这种情况下，布局的名称应以开头`item_`。

请注意，在某些情况下将无法应用这些规则。例如，在创建其他布局一部分的布局文件时。在这种情况下，您应该使用前缀`partial_`

#### 1.2.2.3 Menu 文件

与布局文件类似，菜单文件应与组件名称匹配。例如，如果我们定义要在中使用的菜单文件，则`UserActivity`文件名应为`activity_user.xml`

好的做法是不要将单词`menu`作为名称的一部分，因为这些文件已经位于`menu`目录中

#### 1.2.2.4 Values 文件

在文件夹应该是值的资源文件复数，例如 `strings.xml`, `styles.xml`, `colors.xml`, `dimens.xml`, `attrs.xml`

# 2 代码指南

## 2.1 Java 语言规范

### 2.1.1 不要忽略异常

禁止执行以下的操作:

```java
void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) { }
}
```
尽管您可能认为您的代码永远不会遇到这种错误情况，或者处理它并不重要，但是忽略上述异常会在代码中产生隐患，让其他人有一天会跳入坑中。您必须以某种原则处理代码中的每个Exception。具体处理方式视情况而异。-[Android程式码规范](https://source.android.com/setup/contribute/code-style)

[在这里](https://source.android.com/source/code-style.html#dont-ignore-exceptions)查看替代方案


### 2.1.2 不要捕获通用异常

不建议执行以下的操作:

```java
try {
    someComplicatedIOFunction();        // may throw IOException
    someComplicatedParsingFunction();   // may throw ParsingException
    someComplicatedSecurityFunction();  // may throw SecurityException
    // phew, made it all the way
} catch (Exception e) {                 // I'll just catch all exceptions
    handleError();                      // with one generic handler!
}
```

在[这里](https://source.android.com/source/code-style.html#dont-catch-generic-exception)查看原因和一些替代方案

### 2.1.3 不要使用终结器

终结器是一种在垃圾回收对象时执行大量代码的方法。虽然终结器可以很方便地进行清理（尤其是外部资源），但无法保证何时调用终结器（甚至根本不会调用终结器）。

Android不使用终结器。在大多数情况下，您可以使用良好的异常处理。如果您绝对需要终结器，请定义一个close()方法（或类似方法）并确切记录何时需要调用该方法（有关示例，请参见 InputStream）。在这种情况下，从终结器打印一条简短的日志消息是适当的，但不是必需的，只要不希望它淹没日志即可。 - ([Android code style guidelines](https://source.android.com/source/code-style.html#dont-use-finalizers))


### 2.1.4 使用完整的 imports

不好的: `import foo.*;`

好的: `import foo.Bar;`

更多 [参考](https://source.android.com/source/code-style.html#fully-qualify-imports)

## 2.2 Java 样式规则

### 2.2.1 字段定义和命名

字段应在文件顶部定义，并且应遵循下面列出的命名规则。

- 私有非静态字段名称以m开头。
- 私有静态字段名称以s开头。
- 其他字段以小写字母开头。
- 静态最终字段（常量）为ALL_CAPS_WITH_UNDERSCORES。

Example:

```java
public class MyClass {
    public static final int SOME_CONSTANT = 42;
    public int publicField;
    private static MyClass sSingleton;
    int mPackagePrivate;
    private int mPrivate;
    protected int mProtected;
}
```

### 2.2.3 将缩写词视为单词

| Good           | Bad            |
| -------------- | -------------- |
| `XmlHttpRequest` | `XMLHTTPRequest` |
| `getCustomerId`  | `getCustomerID`  |
| `String url`     | `String URL`     |
| `long id`        | `long ID`        |

### 2.2.4 使用空格进行缩进

对块使用4个空格缩进:

```java
if (x == 1) {
    x++;
}
```

使用8个空格缩进进行换行：

```java
Instrument i =
        someLongExpression(that, wouldNotFit, on, one, line);
```

### 2.2.5 使用标准花括号样式

花括号与它们前面的代码在同一行。

```java
class MyClass {
    int func() {
        if (something) {
            // ...
        } else if (somethingElse) {
            // ...
        } else {
            // ...
        }
    }
}
```

除非条件和主体合而为一，否则必须在语句周围使用大括号。

如果条件和主体合在一条线上，并且那条线比最大线长短，则不需要大括号，例如

```java
if (condition) body();
```

不好的:

```java
if (condition)
    body();  // bad!
```

### 2.2.6 Annotations

#### 2.2.6.1 Annotations 

- @Override：当某个方法替换了超类中的声明或实现时，必须使用 @Override 注释。例如，如果您使用 @inheritdocs Javadoc 标记，并且派生于某个类（而非接口），则必须再为方法添加 @Override 注释，说明该方法替换了父类的方法。

- @SuppressWarnings：@SuppressWarnings 注释应该仅在无法消除警告的情况下使用。如果某个警告通过了“无法消除”测试，则必须使用 @SuppressWarnings 注释，以确保所有警告都会反映出代码中的实际问题。

有关注释准则的更多信息，请参见[此处](http://source.android.com/source/code-style.html#use-standard-java-annotations)。

#### 2.2.6.2 Annotations 风格

__Classes, Methods and Constructors__

将Annotation应用于类，方法或构造函数时，每行显示一个Annotation。

```java
/* This is the documentation block about the class */
@AnnotationA
@AnnotationB
public class MyAnnotatedClass { }
```

__Fields__

除非行达到最大行长，否则应用于字段的 Annotation应在同一行上列出。

```java
@Nullable @Mock DataManager mDataManager;
```

### 2.2.7 限制变量的作用域

尽可能缩小局部变量的作用域。这样做有助于提高代码的可读性和可维护性，并降低出错的可能性。在包含变量所有使用情形的最内层的块中声明每个变量。

在首次使用的位置声明局部变量。几乎每个局部变量声明都应该包含一个初始化程序。如果您还没有足够的信息来合理地初始化某个变量，请推迟到信息充足时再声明。 - ([Android code style guidelines](https://source.android.com/source/code-style.html#limit-variable-scope))

### 2.2.8 import 顺序

如果您使用的是Android Studio之类的IDE，则不必担心这一点，因为您的IDE已经遵守了这些规则。如果没有，请在下面查看。

导入语句的顺序为：

1. Android导入
2. 从第三方（com，junit，net，org）导入
3. java和javax
4. 同一项目进口


为了与IDE设置完全匹配，导入应为：

- 在每个分组中按字母顺序排列，大写字母在小写字母之前（例如Z在a之前）。
- 每个主要分组（android，com，junit，net，org，java，javax）之间应该有一个空白行。
更多信息参考[这里](https://source.android.com/source/code-style.html#limit-variable-scope)

### 2.2.9 Logging 指南

使用Log该类提供的日志记录方法可以打印出错误消息或其他信息，这些信息可能对开发人员识别问题很有用：

* `Log.v(String tag, String msg)` (verbose)
* `Log.d(String tag, String msg)` (debug)
* `Log.i(String tag, String msg)` (information)
* `Log.w(String tag, String msg)` (warning)
* `Log.e(String tag, String msg)` (error)

通常，我们将类名称用作标记，并将其定义为`static final`文件顶部的字段。例如：

```java
public class MyClass {
    private static final String TAG = MyClass.class.getSimpleName();

    public myMethod() {
        Log.e(TAG, "My error message");
    }
}
```

在发行版本上必须禁用VERBOSE和DEBUG日志。还建议禁用“信息”，“警告”和“错误”日志，但是如果您认为它们对于确定发行版本中的问题可能有用，则可能希望使其保持启用状态。如果决定让它们保持启用状态，则必须确保它们没有泄漏私人信息，例如电子邮件地址，用户ID等。

仅在调试版本上显示日志：

```java
if (BuildConfig.DEBUG) Log.d(TAG, "The value of x is " + x);
```

### 2.2.10 类成员排序


使用逻辑和一致的顺序将提高代码的可学习性和可读性。建议使用以下顺序：

1. Constants
2. Fields
3. Constructors
4. Override methods and callbacks (public or private)
5. Public methods
6. Private methods
7. Inner classes or interfaces

示例:

```java
public class MainActivity extends Activity {

    private static final String TAG = MainActivity.class.getSimpleName();

    private String mTitle;
    private TextView mTextViewTitle;

    @Override
    public void onCreate() {
        ...
    }

    public void setTitle(String title) {
    	mTitle = title;
    }

    private void setUpView() {
        ...
    }

    static class AnInnerClass {

    }

}
```

如果您的类扩展了诸如Activity或Fragment之类的Android组件，则最好对覆盖方法进行排序，以使其与组件的生命周期相匹配。举例来说，如果你有一个活动实现`onCreate()`，`onDestroy()`，`onPause()`和`onResume()`，然后按照正确的顺序是：

```java
public class MainActivity extends Activity {

	//Order matches Activity lifecycle
    @Override
    public void onCreate() {}

    @Override
    public void onResume() {}

    @Override
    public void onPause() {}

    @Override
    public void onDestroy() {}

}
```

### 2.2.11 方法中的参数排序

在为Android编程时，定义带有的方法是很普遍的Context。如果要编写这样的方法，则Context必须是第一个参数。

相反的情况是CallBack始终为最后一个参数。

示例:

```java
// Context always goes first
public User loadUser(Context context, int userId);

// Callbacks always go last
public void loadUserAsync(Context context, int userId, UserCallback callback);
```

### 2.2.13 字符串常量，命名和值

例如Android SDK中的许多元素SharedPreferences，Bundle或Intent使用一个键值对的方式，因此很可能，即使是一个小应用程序，你最终不得不写了很多字符串常量。

使用这些组件之一时，必须将键定义为static final字段，并且它们的前缀应如下所示。

| Element            | Field Name Prefix |
| -----------------  | ----------------- |
| SharedPreferences  | `PREF_`             |
| Bundle             | `BUNDLE_`           |
| Fragment Arguments | `ARGUMENT_`         |
| Intent Extra       | `EXTRA_`            |
| Intent Action      | `ACTION_`           |

请注意，Fragment的参数Fragment.getArguments()也是Bundle。但是，由于这是Bundle的非常普遍的用法，因此我们为它们定义了不同的前缀。

Example:

```java
// Note the value of the field is the same as the name to avoid duplication issues
static final String PREF_EMAIL = "PREF_EMAIL";
static final String BUNDLE_AGE = "BUNDLE_AGE";
static final String ARGUMENT_USER_ID = "ARGUMENT_USER_ID";

// Intent-related items use full package name as value
static final String EXTRA_SURNAME = "com.myapp.extras.EXTRA_SURNAME";
static final String ACTION_OPEN_USER = "com.myapp.action.ACTION_OPEN_USER";
```

### 2.2.14  Fragments 和 Activities

通过我们会定义一个 `getStartIntent()`方法用于启动Activity:

```java
public static Intent getStartIntent(Context context, User user) {
	Intent intent = new Intent(context, ThisActivity.class);
	intent.putParcelableExtra(EXTRA_USER, user);
	return intent;
}
```

对于Fragments，它被命名`newInstance()`并使用正确的参数处理Fragment的创建：

```java
public static UserFragment newInstance(User user) {
	UserFragment fragment = new UserFragment();
	Bundle args = new Bundle();
	args.putParcelable(ARGUMENT_USER, user);
	fragment.setArguments(args)
	return fragment;
}
```

__注意1__：这些方法应该放在类的顶部onCreate()。

__注意2__：如果我们提供上述方法，则Extras和arguments的键应该是private因为不需要将它们暴露在类之外。

### 2.2.15 代码行长度限制

代码行不得超过100个字符。如果行的长度超过此限制，通常有两种方法可以减小其长度：

- 提取局部变量或方法（首选）。
- 应用换行将单行分成多行。

可能有超过100行的行有两个例外：

- 不能拆分的行，例如注释中的长URL。
- package和import声明。

#### 2.2.15.1 换行策略

没有一个确切的规则可以约定换行，通常有不同的定义方式。但是，有一些规则可以应用于常见情况

__Break at operators__


当行的结束是操作符时，在操作符之前换行，例如：

```java
int longName = anotherVeryLongVariable + anEvenLongerOne - thisRidiculousLongOne
        + theFinalOne;
```

__Assignment Operator Exception__

`break at operators`规则的一个例外是赋值运算符=，其中换行符应在运算符之后发生。

```java
int longName =
        anotherVeryLongVariable + anEvenLongerOne - thisRidiculousLongOne + theFinalOne;
```

__方法链式调用__

当多个方法链接在同一行中时（例如，使用Builders时），对方法的每次调用都应在自己的行中进行，在  `.`

```java
Picasso.with(context).load("http://ribot.co.uk/images/sexyjoe.jpg").into(imageView);
```

```java
Picasso.with(context)
        .load("http://ribot.co.uk/images/sexyjoe.jpg")
        .into(imageView);
```

__长参数情况__

当一个方法有很多参数或它的参数很长时，我们应该在每个逗号后换行 `,`

```java
loadPicture(context, "http://ribot.co.uk/images/sexyjoe.jpg", mImageViewProfilePicture, clickListener, "Title of the picture");
```

```java
loadPicture(context,
        "http://ribot.co.uk/images/sexyjoe.jpg",
        mImageViewProfilePicture,
        clickListener,
        "Title of the picture");
```

### 2.2.16 RxJava 链式风格

运算符的Rx链需要换行。每个操作员都必须换行，并且在换行之前用`.`断开

```java
public Observable<Location> syncLocations() {
    return mDatabaseHelper.getAllLocations()
            .concatMap(new Func1<Location, Observable<? extends Location>>() {
                @Override
                 public Observable<? extends Location> call(Location location) {
                     return mRetrofitService.getLocation(location.id);
                 }
            })
            .retry(new Func2<Integer, Throwable, Boolean>() {
                 @Override
                 public Boolean call(Integer numRetries, Throwable throwable) {
                     return throwable instanceof RetrofitError;
                 }
            });
}
```

## 2.3 XML 样式规则

### 2.3.1 使用自动关闭标签

当XML元素没有任何内容时，必须使用自闭标签.

好的:

```xml
<TextView
	android:id="@+id/text_view_profile"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content" />
```

不好 :

```xml
<!-- Don\'t do this! -->
<TextView
    android:id="@+id/text_view_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" >
</TextView>
```


### 2.3.2 Resources 名称

资源ID和名称写在 __lowercase_underscore__.

#### 2.3.2.1 ID 名称

ID应该在元素的名称前加上小写的下划线。例如：


| Element            | Prefix            |
| -----------------  | ----------------- |
| `TextView`           | `text_`             |
| `ImageView`          | `image_`            |
| `Button`             | `button_`           |
| `Menu`               | `menu_`             |

Image view example:

```xml
<ImageView
    android:id="@+id/image_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
```

Menu example:

```xml
<menu>
	<item
        android:id="@+id/menu_done"
        android:title="Done" />
</menu>
```

#### 2.3.2.2 Strings

字符串名称以标识它们所属部分的前缀开头。例如`registration_email_hint`或`registration_name_hint`。如果字符串不属于任何部分，则应遵循以下规则：


| Prefix             | Description                           |
| -----------------  | --------------------------------------|
| `error_`             | An error message                      |
| `msg_`               | A regular information message         |
| `title_`             | A title, i.e. a dialog title          |
| `action_`            | An action such as "Save" or "Create"  |



#### 2.3.2.3 样式和主题

与其他资源不同，样式名称是使用 __UpperCamelCase__ 编写的

### 2.3.3 属性排序

通常，您应该尝试将相似的属性分组在一起。排序最常见属性的一种好方法是：

1. View Id
2. Style
3. Layout width and layout height
4. Other layout attributes, sorted alphabetically
5. Remaining attributes, sorted alphabetically

## 2.4 测试规则

### 2.4.1 单元测试

测试类应与测试所针对的类的名称相匹配，后跟Test。例如，如果我们创建一个包含针对的测试的测试类，DatabaseHelper则应将其命名为DatabaseHelperTest。

测试方法带有注释，@Test并且通常应以被测试方法的名称开头，后跟前提条件和/或预期行为。

模板：@Test void methodNamePreconditionExpectedBehaviour()

例： @Test void signInWithEmptyEmailFails()

如果没有它们，测试就足够清楚，则不一定总是需要前提条件和/或预期的行为。

有时，一个类可能包含大量方法，而同时每种方法需要进行多次测试。在这种情况下，建议将测试类分成多个类。例如，如果DataManager包含了很多我们可能需要把它分成方法DataManagerSignInTest，DataManagerLoadUsersTest等等。一般来说，你将能够看到哪些测试属于一个整体，因为他们有共同的[test fixtures](https://en.wikipedia.org/wiki/Test_fixture).

### 2.4.2 Espresso 测试

每个Espresso测试类通常都以Activity为目标，因此名称应与目标Activity的名称匹配，后跟Test，例如`SignInActivityTest`

使用Espresso API时，通常将链接方法放在新行中。

```java
onView(withId(R.id.view))
        .perform(scrollTo())
        .check(matches(isDisplayed()))
```


