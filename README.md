# Appcom Android Style Guide {.unnumbered}

This document describes the style guide applied to android projects for 
appcom interactive GmbH. It describes rules how to organize your
project, packages and files, so that some best practises are held. 
These best practises have developed over the last 5 years and are 
brought ro proof in many commercial products for well known clients.

## Tables of Contents {.unnumbered #table-of-contents}

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

## 1 Gradle {.unnumbered}

  <a name="gradle--version"></a><a name="1.1"></a>
  - [1.1](#gradle--version) Use at least version 2.3 for the build 
  gradle to support Instant run.
   
  <a name="gradle--structure"></a><a name="1.2"></a>
  - [1.2](#gradle--structure) Create one gradle file per module and one 
  file for the project. The project gradle file holds global 
  declarations used for the module gradle files. It is optional but 
  encouraged to create helper gradle files to outsource commonly used 
  functions (such as customizing artifact file names).
  
  <a name="gradle--global-declarations"></a><a name="1.3"></a>
  - [1.3](#gradle--global-declarations) Declare commonly used variables
  as global variables in the project gradle file. This concerns usually
  
  * build tool version (androidBuildToolsVersion)
  * compile sdk version (androidCompileSdkVersion)
  * min sdk version (androidMinSdkVersion)
  * target sdk version (androidTargetSdkVersion)
  * version code (androidVersionCode)
  * version name (androidVersionName)
  
  Also you should declare versions of library clusters (e.g. retrofit,
  google libraries, rxjava) to make library invocations easier.
  
  <a name="gradle--libaries"></a><a name="1.4"></a>
  - [1.4](#gradle--libaries) Declare your libraries globally in the
  project gradle file. This helps you to manage dependencies globally.
  You can reference imported libraries in an module gradle from the
  project gradle. Example
  
    googleLibraryVersion = "25.0.1"
    
    libs = [
        appcompat : ('com.android.support.appcompat-v7:' + googleLibraryVersion),
        supportv4 : ('com.android.support.support-v7:' + googleLibraryVersion)
    ]
  
  <a name="gradle--tasks"></a><a name="1.5"></a>
  - [1.5](#gradle--tasks) Declare your tasks globally in the project 
  gradle file. This helps you to use them in each module gradle file.

  <a name="gradle--versioning"></a><a name="1.6"></a>
  - [1.6](#gradle--versioning) The android version code and the android 
  version name should be created automatically. You should use git for 
  that. The android version name matches the current git tag. The 
  android version code matches the number of commits after the last git 
  tag. The initial commit must be tagged with `0.0.0`. Each tag must 
  conform the [SemVer](http://semver.org/) Specification.

**[back to top](#table-of-contents)**

## 2 Artifacts {.unnumbered}

  development, staging, produciton

  <a name="artifact--name"></a><a name="2.1"></a>
  - [2.1](#artifact--name) The artifact should be named after 
  the following scheme:
  
    $COMPANYNAME$-$APPNAME$-$STAGE$-$VERSIONNAME$.$VERSIONCODE$.apk 
    
    // bad
    appcom-swipe.apk
    swipe-0.0.1.apk
    
    // good
    appcom-swipe-production-0.0.2.127.apk
    appcom-swipe-develop-0.0.2.20.apk
  
  Stages can be one of the following:
  
  * develop (Development)
  * staging (Preproduction)
  * production (Production)
  
**[back to top](#table-of-contents)**

## 3 Libraries {.unnumbered}

  <a name="libraries--version"></a><a name="3.1"></a>
  - [3.1](#libraries--version) Always use the latest version if possible.
  Make sure to migrate also major releases if possible.
  
  <a name="libraries--grouping"></a><a name="3.2"></a>
  - [3.2](#libraries--grouping) Group library definitions in their 
  respective cluster. Clustering should be done by components. Add a 
  newline after each declaration cluster. Available components are:
  
  * Android
  * UI
  * Database
  * Network
  * Depdency Injection
  * Utilities
  * Misc
  
  Example:
  
    libs = [
        // Android
        appcompat   : ('com.android.support:appcompat-v7:' + supportLibraryVersion),
        supportv4   : ('com.android.support:support-v4:' + supportLibraryVersion),
        
        // Misc               
        timber      : ('com.jakewharton.timber:timber:4.5.1')
    ]
    
**[back to top](#table-of-contents)**

## 4 Modules {.unnumbered}

  <a name="modules--app"></a><a name="4.1"></a>
  - [4.1](#modules--app) Use the `app` module as the primary module, 
  which holds the android context objects (i.e. Application, Activities, 
  Fragments, ...).
   
  <a name="modules--model"></a><a name="4.2"></a>
  - [4.2](#modules--model) Use the `model` module as the data layer for 
  your application. This module does not hold a reference to an android
  specific context or object.

**[back to top](#table-of-contents)**

## 5 Package Structure {.unnumbered}

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
  | dialogs | Contains classes, which are shown as Dialog. Note that you may or may not need this package depending on which dialog framework you are using in your app. |
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

## 6 Style {.unnumbered}

  <a name="style--preferred-stlye"></a><a name="6.1"></a>
  - [6.1](#style--preferred-stlye) Use the square android style for all
  java classes. The style along with install instructions can be found
  [here](https://github.com/square/java-code-styles).

  <a name="style--line-length"></a><a name="6.2"></a>
  - [6.2](#style--line-length) Use a line length of 120 characters.

**[back to top](#table-of-contents)**

## 7 Comments {.unnumbered}

  <a name="comments--multiline"></a><a name="7.1"></a>
  - [7.1](#comments--multiline) Use `/** ... */` for multi-line comments.
  
    // bad
  
    // this does something
    // @param tag The tag.
    private void doSomething(String tag) {
      ... 
    }
  
    // good 
  
    /** 
     * This does something
     * @param tag The tag.
     */
    private void doSomething(String tag) {
      ...
    }
  
  <a name="comments--singleline"></a><a name="7.2"></a>
  - [7.2](#comments--singleline) Use `//` for single line comments. 
  Place single line comments on a newline above the subject of the 
  comment. Put an empty line before the comment unless it's on the first
  line of a block.
  
    // bad
    int active = true;  // is current tab
    
    // good
    // is current tab
    int active = true;
    
    // bad
    private int getType() {
        Log.d(TAG, "Fetching type...");
        // set the default type to 'no type'
        int type = this._type ? this._type : -1;
        
        return type;
    }
    
    // good
    private int getType() {
        Log.d(TAG, "Fetching type...");
        
        // set the default type to 'no type'
        int type = this._type ? this._type : -1;
        
        return type;
    }
    
    // also good
    private int getType() {
        // set the default type to 'no type'
        int type = this._type ? this._type : -1;
        
        return type;
    }

  <a name="comments--spaces"></a><a name="7.3"></a>
  - [7.3](#comments--spaces) Start all comments with a space to make it easier to read. 

    // bad
    //is current tab
    int active = true;  
    
    // good
    // is current tab
    int active = true;

  <a name="comments--fixme"></a><a name="7.4"></a>
  - [7.5](#comments--fixme) Use `// FIXME:` to annotate problems.

    public class Calculator extends Abacus {
      public Calculator() {
        super();

        // FIXME: shouldn't use a global here
        this.total = 0;
      }
    }

  <a name="comments--todo"></a><a name="7.5"></a>
  - [7.6](#comments--todo) Use `// TODO:` to annotate solutions to problems.

    public class Calculator extends Abacus {
      public Calculator() {
        super();

        // TODO: total should be configurable by an options param
        this.total = 0;
      }
    }

**[back to top](#table-of-contents)**

## 8 Strings {.unnumbered}

  <a name="strings--format"></a><a name="8.1"></a>
  - [8.1](#strings--format) Use the `StringBuilder` to concatenate 
  strings. Why: IT is much faster than `String.format` and the 
  `+`-Operator on Strings. Also most of the time the java compiler
  will convert String concatenation to `StringBuilder` calls.
  
    int value = 20;
    
    // bad
    String value = value + " melons";
    
    // also bad 
    String value = String.format("%d melons", value);
    
    // good
    String value = StringBuilder().append(value).append(" melons").toString();
    
**[back to top](#table-of-contents)**

## 9 Classes & Contructors {.unnumbered}

  <a name="classes-constructors--constructor-variables"></a><a name="9.1"></a>
  - [9.1](#classes-constructors--constructor-variables) Reference class 
  inside a constructor always with `this`. Omit `this` outside the 
  constructor, except a parameter would shadow the class member.
  
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
  
      public Klass() {
        this.value = 20;
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
  
  <a name="classes-constructors--tostring"></a><a name="9.2"></a>
  - [9.2](#classes-constructors--tostring) Add always a toString method
  for entities and models. Why: You almost always have the need to log
  the current state of a pojo. And that is when you release you get 
  something like this `[Lcom.foo.Object;@28a418fc`. So keep yourself 
  from this and create a `toString`-Method. Also todays IDEs can create
  it automatically. 
  
  <a name="classes-constructors--solid"></a><a name="9.3"></a>
  - [9.3](#classes-constructors--solid) Classes should be as 
  [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design))
  as possbile. Although this is an architectural pattern it applies to 
  classes in particular and should be mentioned as such. This means in 
  general:
  
  * `S`ingle responsible principle
  * `O`pen/Closed principle
  * `L`iskov substitution principle 
  * `I`nterface segregation principle 
  * `D`ependency inversion principle 
  
**[back to top](#table-of-contents)**

## 10 Naming Conventions {.unnumbered}

  <a name="naming--descriptive"></a><a name="10.1"></a>
  - [10.1](#naming--descriptive) Avoid single letter names. Be 
  descriptive with your naming. Imagine having only the variable name 
  without the concrete type and context and try to infer the purpose 
  of the variable. If the name of the variable gets you there the naming
  is good!
  
    // bad
    int a = 10;
    int value = 20; // bad since, this does not tell the proper purpose of the variable
  
    // good
    TextView firstnameTextView = ...; // good since it is verbose enough to tell you what the object references with the variable is supposed to do. 
  
  <a name="naming--camelcase"></a><a name="10.2"></a>
  - [10.2](#naming--camelcase) Use camelCase when naming objects, 
  functions and instances.
  
    // bad
    int TEst = 0;
    String first_name = "Hans";
    
    // good
    int test = 0;
    String firstName = "Hans";

  <a name="naming--pascalcase"></a><a name="10.3"></a>
  - [10.3](#naming--pascalcase) Use PascalCase only when naming classes,
  constructors and filenames.
  
    // bad
    class testCase {
    }
    
    // good
    class TestCase {
    }
  
  <a name="naming--leading-underscore"></a><a name="10.4"></a>
  - [10.4](#naming--leading-underscore) Do not use trailing or leading 
  underscores. This pattern is often used to indicated private methods
  or attributes. However Java itself has a fully fledged data access
  concept including private date. Use this instead of naming conentions.
  Also underscores make the code harder to read and breaks the overall
  style, since Java names are composed of camelCases and PascalCases.

  <a name="naming--acronyms-and-initialisms"></a><a name="10.5"></a>
  - [10.5](#naming--acronyms-and-initialisms) Acronyms and initialisms
  should always be all capitalized, or all lowercased.
  
  > Why? Names are for readability, not to appease a computer algorithm.
  
    // bad 
    class SmsContainer {
    }
    
    // bad 
    class HttpRequest {
    }
    
    // good
    class SMSContainer {
    }
    
    // good 
    class HTTPRequest {
    }
  
  <a name="naming--layouts"></a><a name="10.6"></a>
  - [10.6](#naming--layouts) Use snake_case for ids within layout files.
  The id should be named after the following pattern:
  
    <WHERE>_<DESCRIPTION>_<WHAT>
    
    // example
    <TextView
        android:id="@+id/login_email_textview"
        ...
    />
  
  The `WHERE` describes in which part of the app the layout is used. The 
  `DESCRIPTION` should tell the reader what the actual purpose of the 
  widget is. `WHAT` tells the reader what the widget is and corresponds
  to a widget type (e.g. TextView, Button).
  
  When binding a java object to the widget defined in the layout name 
  the java object after the widget but camelCased. 
  
    TextView loginEmailTextView = ...;

  <a name="naming--drawable-filenames"></a><a name="10.7"></a>
  - [10.7](#naming--drawable-filenames) Drawable filenames should be 
  named after the following pattern:
  
    <WHAT>_<WHERE>_<DESCRIPTION>[_<SIZE>][_STATE]
    
    // example
    login_background
    login_button_small
    
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
  term (e.g. small). `STATE` indicates the state of the drawable. It can
  be one of the following:
  
  * normal (can be omitted)
  * disabled
  * selected
  * pressed
  
**[back to top](#table-of-contents)**

missing:

* naming strings, dimens, integers, animations, values, styles, themes
* butterknife, timber, (dagger 2, rxjava, retrofit 2 -> best practises)
* architektur pattern
* Interfaces (OnClickListener -> onClicked())
* adapters (packages)
* interfaces in klassen oder außerhalb
* verwendung eines ORMs
* interactors


* live templates snippets exportieren -> stash
* appcom module mit wichtigen klassen (Base fragment, annotations, controllers, ...)
* mvp templates (konfigurierbar)
* linter schreiben
* gradle project -> stash