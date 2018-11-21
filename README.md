# Appcom Android Style Guide

This document describes the style guide applied to android projects for
appcom interactive GmbH. It describes rules how to organize your
project, packages and files, so that some best practises are held.
These best practises have developed over the last 5 years and are
brought to proof in many commercial products for well known clients. It is based on the [Appcom General Code Style Guide](https://github.com/appcom-interactive/android) which should be known to understand all parts of this guide.

So use it in your favor if you want to and/or override the style guide
in any way you want.

## Tables of Contents

1. [Gradle](#gradle)
1. [Artifacts](#artifacts)
1. [Libraries](#libraries)
1. [Modules](#modules)
1. [Package structure](#package-structure)
1. [Style](#style)
1. [Comments](#comments)
1. [Strings](#strings)
1. [Classes & Contructors](#classes--constructors)
1. [Naming Conventions](#naming-conventions)
1. [Asset Naming Conventions](#asset-naming-conventions)
1. [Logging](#logging)
1. [Live templates](#live-templates)
1. [Roadmap](#roadmap)
1. [Resources](#resources)
1. [Related work](#related-work)
1. [License](#license)

## Gradle 

<a name="gradle--version"></a><a name="1.1"></a>
- [1.1](#gradle--version) The android gradle plugin should use the [most recent version possible](https://developer.android.com/studio/releases/gradle-plugin.html). 
Use at least version 2.3.1 to support Instant run and the build-in annotationProcessor. 
You should not use dynamic dependencies in version numbers. Using this feature can cause unexpected version updates and difficulty resolving version differences.

```
// good
classpath 'com.android.tools.build:gradle:3.0.1'

// bad
classpath 'com.android.tools.build:gradle:3.+'
```

<a name="gradle--structure"></a><a name="1.2"></a>
- [1.2](#gradle--structure) Create one gradle file per module and one
file for the project. The project gradle file holds global
declarations used for the module gradle files. It is optional but
encouraged to create helper gradle files to outsource commonly used
functions.

<a name="gradle--global-declarations"></a><a name="1.3"></a>
- [1.3](#gradle--global-declarations) Declare commonly used variables
as global variables in the project gradle file. This concerns usually

* build tool version (buildToolsVersion)
* compile sdk version (compileSdkVersion)
* min sdk version (minSdkVersion)
* target sdk version (targetSdkVersion)
* version code (versionCode)
* version name (versionName)

<a name="gradle--tasks"></a><a name="1.4"></a>
- [1.4](#gradle--tasks) Declare your tasks globally in the project
gradle file. This helps you to use them in each module gradle file.

<a name="gradle--versioning"></a><a name="1.5"></a>
- [1.5](#gradle--versioning) The android version code and the android
version name should be created automatically. You should use git for
that. The android version name matches the current git tag. The
android version code matches the number of commits. The initial commit must be tagged with `0.0.0`. Each tag must
conform the [SemVer](http://semver.org/) Specification. 
Use our [Android Versioning Plugin](https://github.com/appcom-interactive/android-versioning) to automatically generate 
the version name and version code from git.

**[back to top](#table-of-contents)**

## Artifacts

<a name="artifact--name"></a><a name="2.1"></a>
- [2.1](#artifact--name) The artifact should be named after
the following scheme:

```
$COMPANYNAME$-$APPNAME$-$STAGE$-$VERSIONNAME$.$VERSIONCODE$-$BUILDTYPE$.apk
```

```
// good
appcom-swipe-production-0.0.2.127-debug.apk
appcom-swipe-develop-0.0.2.20-release.apk

// bad
appcom-swipe.apk
swipe-0.0.1.apk
```
You can use our [Android Versioning Plugin](https://github.com/appcom-interactive/android-versioning) for Gradle to add 
the specified version name to the output apk automatically.

**[back to top](#table-of-contents)**

## Libraries

<a name="libraries--version"></a><a name="3.1"></a>
- [3.1](#libraries--version) Always use the latest version if possible.
Make sure to migrate also major releases if possible.
You should not use dynamic dependencies in version numbers. Using this feature can cause unexpected version updates and 
difficulty resolving version differences.

<a name="libraries--grouping"></a><a name="3.2"></a>
- [3.2](#libraries--grouping) Group library definitions in their
respective cluster. Clustering should be done by components. Add a
newline after each declaration cluster. Available components are:

* Android
* UI
* Database
* Network
* Depdency Injection
* Debug Tools
* Misc

Example:
```
libs = [
    // Android
    appcompat : ('com.android.support:appcompat-v7:' + supportLibraryVersion),
    supportv4 : ('com.android.support:support-v4:' + supportLibraryVersion),
    
    // Debug Tools
    timber : ('com.jakewharton.timber:timber:4.5.1')
]
```

<a name="libraries--version-grouping"></a><a name="3.3"></a>
- [3.3](#libraries--version-grouping) You should declare versions of library clusters (e.g. retrofit, google libraries, 
rxjava) to make library invocations easier.

```
googleLibraryVersion = "25.0.1"

libs = [
    appcompat : ('com.android.support.appcompat-v7:' + googleLibraryVersion),
    supportv4 : ('com.android.support.support-v7:' + googleLibraryVersion)
]
```

**[back to top](#table-of-contents)**

## Modules

<a name="modules--app"></a><a name="4.1"></a>
- [4.1](#modules--app) Use the `app` module as the primary module,
which holds the android context objects (i.e. Application, Activities,
Fragments, ...).

<a name="modules--model"></a><a name="4.2"></a>
- [4.2](#modules--model) Use the `model` module as the data layer for
your application. This module does not hold a reference to an android
specific context or object.

**[back to top](#table-of-contents)**

## Package Structure

<a name="package-structure--app"></a><a name="5.1"></a>
- [5.1](#package-structure--app) Package structure is important since
it helps when navigating through the code and has a huge impact of the
overall maintainability of the project. Each package name should be
used in its respective plural form, except for the `di` package. The
package structure for the app structure is described in the following
table:

| Package | Description |
|:-|:-------------------|
| activities | Contains all the activities used in the project. |
| adapters | Contains all the adapters of any kind used in the project. |
| annotations | Contains the annotations used in the project. |
| di | Contains classes used for establishing dependency injection. |
| dialogs | Contains classes, which are shown as Dialog. |
| fragments | Contains fragment packages (depending on the architectural pattern used). |
| managers | Contains managers used in the project. |
| listeners | Contains listeners used in your app. These do not need to be cohesive. You can add listeners for animations, lists or ui events. |
| utilities | Contains utility classes. |
| views | Contains classes, which extend android views. |

Note that not each package is needed, since this highly depends on the
usecase, which needs to be implemented. Each class name should end
with the respective package name (e.g. ScrollStateListener in the
listeners package), except for the `di` and `fragment` package.

<a name="package-structure--model"></a><a name="5.2"></a>
- [5.2](#package-structure--model) Each package name should be used in
its respective plural form, except for the `di` package. The package
structure for the model structure is described in the following table:

| Package | Description |
|:-|:-------------------|
| datasources | Contains all the datasources relevant to the project. Can contain packages based on the datasource (i.e. web, database, memory) |
| datastores | Contains all the datastores used in the project. |
| di | Contains classes used for establishing dependency injection. |
| entities | Contains the entities (i.e. objects consumed by the datasources) of the project. Can contain packages based on the datasource (i.e. web, database, memory). |
| managers | Contains managers used in the project. |
| models | Contains the models of the project. |
| schedulers | Contains schedulers, which are needed for asynchronous tasks (preferably with rxjava). |
| transformers | Contains transformers to transform models to entities and vice versa. |
| utilities | Contains utility classes. |

Note that not each package is needed, since this highly depends on the
usecase, which needs to be implemented. Each class name should end
with the respective package name (e.g. ValueTransformer in the
transformers package), except for the `di` package.

**[back to top](#table-of-contents)**

## Style

<a name="style--preferred-stlye"></a><a name="6.1"></a>
- [6.1](#style--preferred-stlye) Use the square android style for all
java classes. XML formatting for layouts is already included and should always been applied. The style along with install instructions can be found
[here](https://github.com/square/java-code-styles). 

<a name="style--line-length"></a><a name="6.2"></a>
- [6.2](#style--line-length) Use a line length of 120 characters.

<a name="style--general"></a><a name="6.3"></a>
- [6.3](#stye--general) Some aspects of code style are not obivous. Therefore we are listing them here explicitly
* Remove any blanklines from the beginning and ending of a block. Exceptions:
  * When defining class members leave a blank line between class header and first class member. Leave also a blank line between the definition of the last class member and the rest.

```
// bad 
public class Foo {

  public void bar() {
  
    // ...
    for(int i = 0; i < 10; i ++) {
      // ...
    }
    
    
  }
  
}

// good
public class Foo {
  public void bar() {
    for(int i = 0; i < 10; i ++) {
      // ...
    }
  }
}

// good, since this is an exception
public class Foo {

  private int member;
  
  public void bar() {
    for(int i = 0; i < 10; i ++) {
      // ...
    }
  }
}
```

* When defining interfaces add a blank line between method definitions. And be consistent!
  
```
  // bad
  public interface Foo {
    void bar1();
    void bar2();
  }
  
  // bad
  public interface Foo {
    void bar1();
    void bar2();
    
    void bar3();
  }
  
  // good 
  public interface Foo {
    void bar1();
    
    void bar2();
    
    void bar3();
  }
```

**[back to top](#table-of-contents)**

## Comments

<a name="comments--multiline"></a><a name="7.1"></a>
- [7.1](#comments--multiline) Use `/** ... */` for block comments.

```
// good

/**
* This does something
* @param tag The tag.
*/
private void doSomething(String tag) {
    ...
}

// bad

// this does something
// @param tag The tag.
private void doSomething(String tag) {
    ...
}
```

<a name="comments--singleline"></a><a name="7.2"></a>
- [7.2](#comments--singleline) Use `//` for single line comments.
Place single line comments on a newline above the subject of the
comment. Put an empty line before the comment unless it's on the first
line of a block.

```
// good
// is current tab
int active = true;

// bad
int active = true; // is current tab


// good
private int getType() {
Log.d(TAG, "Fetching type...");

// set the default type to 'no type'
int typeOut = this.type ? this.type : -1;

return typeOut;
}

// bad
private int getType() {
    Log.d(TAG, "Fetching type...");
    // set the default type to 'no type'
    int typeOut = this.type ? this.type : -1;
    
    return typeOut;
}


// also good
private int getType() {
    // set the default type to 'no type'
    int typeOut = this.type ? this.type : -1;
    
    return type;
}
```

<a name="comments--spaces"></a><a name="7.3"></a>
- [7.3](#comments--spaces) Start all comments with a space to make it easier to read.

```
// good
// is current tab
int active = true;

// bad
//is current tab
int active = true;
```

<a name="comments--fixme"></a><a name="7.4"></a>
- [7.5](#comments--fixme) Use `// FIXME:` to annotate problems.

```
public class Calculator extends Abacus {
    public Calculator() {
        super();
        
        // FIXME: shouldn't use a global here
        this.total = 0;
    }
}
```

<a name="comments--todo"></a><a name="7.5"></a>
- [7.6](#comments--todo) Use `// TODO:` to annotate solutions to problems.

```
public class Calculator extends Abacus {
    public Calculator() {
        super();
        
        // TODO: total should be configurable by an options param
        this.total = 0;
    }
}
```

**[back to top](#table-of-contents)**

## Strings 

<a name="strings--format"></a><a name="8.1"></a>
- [8.1](#strings--format) Use the `StringBuilder` to concatenate
  strings in performance critical situations and in loops. Why: It is much faster than `String.format` and the `+`-Operator on Strings. Also most of the time the java compiler
  will convert String concatenation to `StringBuilder` calls. 

```
int value = 20;

// good
String value = StringBuilder().append(value).append(" melons").toString();

// bad
String value = value + " melons";
String value = String.format("%d melons", value);
String value = StringBuilder().append(value + " melons").toString();
```

**[back to top](#table-of-contents)**

## Classes & Contructors 

<a name="classes-constructors--constructor-variables"></a><a name="9.1"></a>
- [9.1](#classes-constructors--constructor-variables) Reference class
inside a constructor always with `this`. Omit `this` outside the
constructor, except a parameter would shadow the class member.

```
// good
class Klass {
    
    private int value;
    
    public Klass() {
        this.value = 20;
    }
}

// bad
class Klass {
    
    private int value;
    
    public Klass() {
        value = 20;
    }
}


// good
class Klass {

private int value;

    ...
    
    public int getValue() {
        return value;
    }
}

// good
class Klass {

private int value;

    ...
    
    public void doSomething(int value) {
        this.value = value;
        ...
    }
}

// bad
class Klass {

private int value;

    ...
    
    public int getValue() {
        return this.value;
    }
}
```

<a name="classes-constructors--tostring"></a><a name="9.2"></a>
- [9.2](#classes-constructors--tostring) Add always a toString method
  for entities and models. Why: You almost always have the need to log
  the current state of a pojo. And that is when you release you get
  something like this `[Lcom.foo.Object;@28a418fc`. So keep yourself
  from this and create a `toString`-Method. Also todays IDEs can create
  it automatically.

<a name="classes-constructors--order"></a><a name="9.3"></a>
- [9.3](#classes-constructors--order) Arrange methods, variables, constructors,
  interfaces etc. in the following consistent order. Why: It supports your team 
  members to understand your source code faster.
1. static variables
2. butterknife view bindings
3. butterknife resource bindings
4. butterknife listener bindings
5. dependency injection
6. instance variables
7. constructors
8. instance methods
    1. android lifecycle (onCreate, onResume, ...)
    2. initialization and setup
    3. inherited
    4. rest
9.  interfaces/ callbacks

```
// good
public class Klass() {
	public static final long DURATION_IN_MILLIS = 1000L;
	
	@BindView(R.id.login_password_textview) TextView loginPasswordTextview;
	
	@BindString(R.string.login_title) String loginTitleString;
	
	@OnClick(R.id.login_button_submit) onLoginButtonSubmitClicked() { ... }
	
	@Inject LoginController loginController;
	
	private boolean userIsLoggedIn;
	
	Klass() { ... }
	
	private void init() { ... }
	
	interface OnLoginClickListener { ... }
}
```

**[back to top](#table-of-contents)**

## Naming Conventions 

<a name="naming--descriptive"></a><a name="10.1"></a>
- [10.1](#naming--descriptive) Avoid single letter names. Be
descriptive with your naming. Imagine having only the variable name
without the concrete type and context and try to infer the purpose
of the variable. If the name of the variable gets you there the naming
is good!

```
// good since it is verbose enough to tell you what the object references with the variable is supposed to do.
TextView firstnameTextView = ...;
int age = 12;
String lastName;

// bad
int a = 10;
int value = 20; // bad since, this does not tell the proper purpose of the variable
```

<a name="naming--camelcase"></a><a name="10.2"></a>
- [10.2](#naming--camelcase) Use camelCase when naming objects,
functions and instances.

```
// good
int test = 0;
String firstName = "Hans";

// bad
int TEst = 0;
String first_name = "Hans";
```

<a name="naming--pascalcase"></a><a name="10.3"></a>
- [10.3](#naming--pascalcase) Use PascalCase only when naming classes,
constructors and other java filenames.

```
// good
class TestCase {
}

// bad
class testCase {
}
```

<a name="naming--leading-underscore"></a><a name="10.4"></a>
- [10.4](#naming--leading-underscore) Do not use trailing or leading
underscores. This pattern is often used to indicated private methods
or attributes. However Java itself has a fully fledged data access
concept including private date. Use this instead of naming conentions.
Also underscores make the code harder to read and breaks the overall
style, since Java names are composed of camelCases and PascalCases.

```
// good
private int counter;

// bad
int _counter;


// good
void setText (String text) {
    this.text = text;
}

// bad
void setText (String _text) {
    this.text = _text;
}
```

<a name="naming--acronyms-and-initialisms"></a><a name="10.5"></a>
- [10.5](#naming--acronyms-and-initialisms) Acronyms and initialisms
should always be all capitalized, or all lowercased.

> Why? Names are for readability, not to appease a computer algorithm.

```
// good
class SMSContainer {
}

// good
class HTTPRequest {
}

// bad
class SmsContainer {
}

// bad
class HttpRequest {
}
```

<a name="naming--listener-interafaces"></a><a name="10.6"></a>
- [10.6](#naming--listener-interafaces) Interfaces for listeners
should be written in present tense, should start with `On` and end
with `Listener`. Every method within this interface should be written
in past tense and should start with `on`.

```
// good
public interface OnClickListener { ... }
public interface OnScollListener { ... }

// bad
public interface ClickListener { ... }
public interface OnScrolledListener { ... }


// good
public interface OnClickListener {
void onClicked(...) { ... }
}

// bad
public interface OnClickListener {
void onClick(...) { ... }
}
```

**[back to top](#table-of-contents)**

## Asset naming conventions 

<a name="naming--layouts"></a><a name="11.1"></a>
- [11.1](#naming--layouts) Use snake_case for ids within layout files.
The id should be named after the following pattern:

```
<WHERE>_<DESCRIPTION>_<WHAT>
```

```
// example
<TextView
android:id="@+id/login_email_textview"
...
/>
```

The `WHERE` describes in which part of the app the layout is used. The
`DESCRIPTION` should tell the reader what the actual purpose of the
widget is. `WHAT` tells the reader what the widget is and corresponds
to a widget type (e.g. TextView, Button).

When binding a java object to the widget defined in the layout name
the java object after the widget but camelCased.

```
TextView loginEmailTextView = ...;
```

<a name="naming--drawable-filenames"></a><a name="11.2"></a>
- [11.2](#naming--drawable-filenames) Drawable filenames should be
named after the following pattern:

```
<WHAT>_<WHERE>_<DESCRIPTION>[_<SIZE>][_STATE]
```

```
// example
bg_login_textview
ic_startactivity_button_small
```

The `WHAT` denodes the type of the drawable. It can be one of the
following:

* ic (Icons)
* bg (Backgrounds)
* shape (Shapes)

The `WHERE` describes in which part of the app the drawable is used.
If the drawable is used in more than one screen use `all`. The
`DESCRIPTION` should tell the reader what the actual purpose of the
drawable is. The `SIZE` part is optional and describes the size of the
drawable. This can either be a measurable (e.g. 24dp) or a generic
term (e.g. small). `STATE` indicates optionally the state of the
drawable. It can be one of the following:

* normal (can be omitted)
* disabled
* selected
* pressed


<a name="naming--strings"></a><a name="11.3"></a>
- [11.3](#naming--strings) Strings should be named after the following
pattern:

```
<WHERE>_<DESCRIPTION>
```

```
// example
all_cancel
login_firstname
```

The `WHERE`describes in which part of the app the string is used. If
it is used in more than one screen use `all`. The `DESCRIPTION` should
tell the reader what the actual purpose of the string is.

When developing a cross platform app, we recommend using the [twine](https://github.com/scelis/twine) tool for easy 
multi platform string resource synchronization. To make it even easier, just use 
our [Android Twine Plugin](https://github.com/appcom-interactive/android-twine).

<a name="naming--dimens"></a><a name="11.4"></a>
- [11.4](#naming--dimens) Dimensions should be named after the
following pattern:

```
<WHAT>_<DESCRIPTION>[_<SIZE>]
```

```
// example
height_toolbar
height_textsize_medium
margin_profileimage_16dp
```

The `WHAT` indicates the type and thus the purpose of the dimens. It
can be one of the following:

* width (in dp)
* height (in dp)
* size (width == height, in dp)
* fontsize (in sp)
* margin (in dp)
* padding (in dp)
* elevation (in dp)

Note that there are many other types, which can be used. The
`DESCRIPTION` should tell the reader what the actual purpose of the
dimens is. The `SIZE` part is optional and describes the size of the
drawable. This can either be a measurable (e.g. 24dp) or a generic
term (e.g. small).

<a name="naming--integers"></a><a name="11.5"></a>
- [11.5](#naming--integers) Integers should be named after the following
  pattern:

```
<WHERE>_<DESCRIPTION>
```

```
// example
all_animation_duration
login_number_of_successful_tries
```

The `WHERE`describes in which part of the app the integer is used. If
it is used in more than one screen use `all`. The `DESCRIPTION` should
tell the reader what the actual purpose of the integer is.

<a name="naming--colors"></a><a name="11.6"></a>
- [11.6](#naming--colors) Colors should be named after the following rules: 

Do not use prefixes such as dark, light, bright. You cannot ensure that you  never need e.g. an even darker blue than your current darkblue in future. Do not name a color based on its purpose. We often think of a color when we need it and not where it is used. This makes it hard to find the correct one and leads to opening the colors.xml file and hence waste time. When there is suddenly a new purpose, which was not foreseen you probably have to rename the color. 

Instead create a distinct color palette and use the Android Studio plugin [Name That Color](https://plugins.jetbrains.com/plugin/10422-name-that-color) to call the color by its actual name. When you have a hexadecimal color in you clipboard you can use auto-complete (control + space) in the color.xml file to insert directly the complete `<color>` tag with correct naming.

```
// good
<color name="apricot">#f0a081</color>
<color name="violet_red">#FF4081</color>
<color name="governor_bay">#3F51B5</color>

// bad
<color name="all_toolbar">#59177e</color>
<color name="all_button_fab">#59177e</color>
<color name="dark_blue">#f0a081</color>
<color name="light_gray">#aaaabb</color>
```

<a name="naming--styles"></a><a name="11.7"></a>
- [11.7](#naming--styles) Use styles to describe the appearence of a view, which is used multiple times in the app. Write style names in UpperCamelCase. Do only describe view specific information, do not include information such as margin, which describe its specific environment or positioning. Inherit styles from another, start generic and become more specific. Why? Styles allow you to separate the design from its structure and layout. You only have to set style attributes in one place which makes it easy to apply changes or reuse the appearance of a view without copying code. 

```
<Category>.<SubCategory> ...
```

```
// good
<style name="RectSolid" parent="Widget.AppCompat.Button">
    <item name="android:textColor">@color/green_grass</item>
</style>
<style name="RectSolid.Primary">
    <item name="android:textAllCaps">true</item>
    <item name="android:textStyle">bold</item>
</style>

// bad
<style name="RectSolid" parent="Widget.AppCompat.Button">
    <item name="android:textColor">@color/green_grass</item>
</style>
<style name="RectSolidPrimary" parent="Widget.AppCompat.Button">
	<item name="android:textColor">@color/green_grass</item>
    <item name="android:textAllCaps">true</item>
    <item name="android:textStyle">bold</item>
</style>

```

<a name="naming--themes"></a><a name="11.8"></a>
- [11.8](#naming--themes) Use themes to describe the appearance of your entire app, an activity or a view hierarchy. Themes make it easy to apply changes globally and in addition allow to configure non-view elements such as status-bar, textPrimaryColor or windowEnterTransition. Always inherit your app theme from the android support library (Theme.AppCompat. …) to ensure backwards compatibility with android 4.0. When you inherit from android themes, do this explicitly with the `parent` attribute. Apart from that, the rules for styles equally apply to themes.

```
// good
<style name="BaseAppTheme" parent="Theme.AppCompat.Light.NoActionBar">
	<item name="android:colorPrimary">@color/green_grass</item>
</style>
```

**[back to top](#table-of-contents)**

## Logging 

<a name="logging--loglevels"></a><a name="12.1"></a>
- [12.1](#logging--loglevels) Android provides a various amount of log
levels each with its own purpose. Along with android studio you have
the tool for filtering the logcat by exactly this levels. Here is a
list of common log levels and their meaning

| Level | Meaning |
|:-|:-------------|
| DEBUG | To log about anything happen in the app. Is mostly used during debugging. Should be revisited before entering into production mode. |
| ERROR | Log every error conditions (e.g. result of an API call resulted in an error). Usually the programm can resume from there and result in an error state. |
| INFO | All user driven actions of system specific. |
| VERBOSE | Multipurpose log level. A log statement at this level will always be shown in the logcat. |
| WARN | Log all events, which could potentially become an error. |

<a name="logging--messages"></a><a name="12.2"></a>
- [12.2](#logging--messages) Write meaningful log messages. This
sounds easy but is in fact really hard. Keep in mind, that you
sometimes log for the event of an error, which in reality occurs only
rarely. But if it does, you depend on a clear log message along with
an expressive payload. A good log message should consist of the
following items:

* The filename and linenumber of the log statement (optionally the methodname to make it easier to categorize the log purely from reading)
* A meaningful log message
* An expressive payload, usually a Java object (That's why you should always add a toString Method - see [here](#classes-constructors--tostring)

<a name="logging--message-language"></a><a name="12.3"></a>
- [12.3](#logging--message-language) Write your log messages in
english. English is a well known language both in terms of writing and
reading. Furthermore does it not contain any special characters, which
means the it can be logged with ASCII. This is especially important
when performing log rotation, since you do not know where your logs
are stored.

<a name="logging--payload"></a><a name="12.4"></a>
- [12.4](#logging--payload) Always log with payload (i.e. context).
The log message often is not sufficient when tracing bugs. You almost
always need additional information. So just log them along with the
message and you keep yourself from deploying a new version of the app
just to improve log messages.

Make sure that the payload is well formatted and complete. The format
helps you to filter and/or search for certain events.

```
// good
Transaction failed: { id: 63287, checksum: null }

// bad
Transaction failed!

// bad. Payload included but hard to read/parse
Transaction '63287' failed: Checksum 'null' is invalid!
```

## Live templates 

The following is a list of live templates, which can be found
[here](livetemplates). Theses are grouped by certain topics. Each group
has several live templates, which are described in the following. We
list the shortcut and a preview of the code, which will be generated. If
necessary the code will be explained in comments. We will use variables
the same way Android Studio (and IntelliJ in general) does.

For those who are not familiar with this should check it out
[here](https://www.jetbrains.com/help/idea/2016.3/live-templates.html).

### Android 

* rouiT

```
getActivity().runOnUiThread(new Runnable() {
public void run() {
$END$
}
});
```

* gone

```
$VIEW$.setVisibility(View.GONE);
```

* visible

```
$VIEW$.setVisibility(View.VISIBLE);
```

* govi

```
$VIEW$.setVisibility($CONDITION$ ? View.GONE : View.VISIBLE);
```

* vigo

```
$VIEW$.setVisibility($CONDITION$ ? View.VISIBILE : View.GONE);
```

### ButterKnife 

* bind

```
@Bind(R.id.$VIEW_ID$) $TYPE$ $VIEW_ID_CAMELCASED$;
```

where as `$VIEW_ID_CAMELCASED$` is the camelcased value of `$VIEW_ID$`.

* binv

```
@BindView(R.id.$VIEW_ID$) $TYPE$ $VIEW_ID_CAMELCASED$;
```

where as `$VIEW_ID_CAMELCASED$` is the camelcased value of `$VIEW_ID$`.

* binddrawable

```
@BindDrawable(R.drawable.$DRAWABLE_ID$) Drawable $DRAWABLE_ID_CAMELCASED$Drawable;
```

where as `$DRAWABLE_ID_CAMELCASED$` is the camelcased value of `$DRAWABLE_ID$`.

* bindcolor

```
@BindColor(R.color.$COLOR_ID$) int $COLOR_ID_CAMELCASED$Color;
```

where as `$COLOR_ID_CAMELCASED$` is the camelcased value of `$COLOR_ID$`;

* bindstring

```
@BindString(R.color.$String_ID$) String $String_ID_CAMELCASED$String;
```

where as `$STRING_ID_CAMELCASED$` is the camelcased value of `$STRING_ID$`;

* onclick

```
@OnClick(R.id.$VIEW_ID$) public void on$VIEW_ID_CAPITALIZED$Clicked(View view) {
$END$
}
```

where as $VIEW_ID_CAPITALIZED$ is the capitalized value of $VIEW_ID$.

* onlongclick

```
@OnLongClick(R.id.$VIEW_ID$) public void on$VIEW_ID_CAPITALIZED$LongClicked(View view) {
$END$
}
```

where as $VIEW_ID_CAPITALIZED$ is the capitalized value of $VIEW_ID$.

* ontouch

```
@OnTouch(R.id.$VIEW_ID$) public boolean on$VIEW_ID_CAPITALIZED$Touched() {
$END$
}
```

where as $VIEW_ID_CAPITALIZED$ is the capitalized value of $VIEW_ID$.

* onitemselected

```
@OnItemSelected(R.id.$VIEW_ID$) public boolean on$VIEW_ID_CAPITALIZED$Selected(int position) {
$END$
}
```

where as $VIEW_ID_CAPITALIZED$ is the capitalized value of $VIEW_ID$.

### Dagger 2 

* inj

```
@Inject $END$
```

* injc

```
@Inject public $CLASS$() {
$END$
}
```

* pro

```
@Provides $END$
```

* sing

```
@Singleton $END$
```

* ppa

```
@Provides @PerActivity public $CLASS$ provide$CLASS$($CLASS$Impl $CLASS_COPY$Impl) {
return $CLASS_COPY$Impl;
}
```

where as `$CLASS_COPY$` is the decapitalized value of `$CLASS$`.

* ppf

```
@Provides @PerFragment public $CLASS$ provide$CLASS$($CLASS$Impl $CLASS_COPY$Impl) {
return $CLASS_COPY$Impl;
}
```

where as `$CLASS_COPY$` is the decapitalized value of `$CLASS$`.

* pps

```
@Provides @Singleton public $CLASS$ provide$CLASS$($CLASS$Impl $CLASS_COPY$Impl) {
return $CLASS_COPY$Impl;
}
```

where as `$CLASS_COPY$` is the decapitalized value of `$CLASS$`.

### RxJava 2 

* rxsingle

```
Single.create(subscriber -> {
$END$
});
```

* rxcompletable

```
Completable.create(subscriber -> {
$END$
});
```

* rxobservable

```
Observable.create(subscriber -> {
$END$
});
```

* rxflowable

```
Flowable.create(subscriber -> {
$END$
}, $STRATEGY$);
```

* rxsuccess

```
subscriber.onSuccess($END$);
```

* rxerror

```
subscriber.onError($END$);
```

* rxcompleted

```
subscriber.onCompleted();
```

* rxnext

```
subscriber.onNext($NED$);
```

### Timber 

* timd

```
Timber.d($END$);
```

## Roadmap 

This section describes items, which will be added to this style guide in
the future. These items are categorized in two sections namely
`next` for items added in the next release and `future` for items added
in future releases without a fixed date.

### Next 

* Naming conventions for <strike>strings, dimens, integers, styles, themes</strike>
* Conventions for View Injection using Butterknife
* <strike>Conventions for Logging</strike>
* <strike>Naming conventions for Listener-Interfaces and corresponding methods</strike>
* Naming and Grouping conventions for adapters and their view holders
* Grouping conventions for tightly/loosely bounded interfaces
* Usage of ORMs in favor of native SQLite Classes
* Describe Twine support for cross platform string sharing
* Useful links

### Future 

* List and link to Live templates, which can be used in Android Studio
* Architectural patterns (MVP, MVVM)
* Description of the interactor pattern
* Gradle project with important methods and classes based on this style guide
* MVP Templates for Android Studio based on this style guide
* Linter plugin for gradle based on this style guide
* Library project for common classes
* CLI for creating projects/templates

## Resources 

### Naming 

* [http://jeroenmols.com/blog/2016/03/07/resourcenaming/](http://jeroenmols.com/blog/2016/03/07/resourcenaming/)


### Logging 

* [http://www.masterzen.fr/2013/01/13/the-10-commandments-of-logging/](http://www.masterzen.fr/2013/01/13/the-10-commandments-of-logging/)

## Related work

* [Versioning](https://github.com/appcom-interactive/android-versioning) a gradle plugin to enforce artifact naming specified in this document
* [Localisation](https://github.com/appcom-interactive/android-twine) a gradle plugin to provide scripts to use twine 
* [Installing appcom livetemplates](https://github.com/appcom-interactive/android-livetemplates)

## License 

(The MIT License)

Copyright (c) 2017 appcom interactive GmbH

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
