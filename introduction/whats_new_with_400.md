# What's New With 4.0.0

## Introduction

ColdBox 4 is a major release in our ColdBox Platform series and includes
a new revamped MVC core and all extra functionality has been refactored
into modules. We have pushed the modular architecture to 1st class
citizen even in the core itself. There are several [compatibility
updates](upgrading_to_coldbox_400.md) that you must do in order to upgrade your ColdBox 3.X
applications to ColdBox 4 standards. You will notice that the source and
download of ColdBox 4 has been reduced by almost 75% in size. This is
now due to our modular approach where functionality can just be brought
in dynamically. How you might say?

### CommandBox

<img src="../images/CommandBoxLogo.png">

[CommandBox](http://www.ortussolutions.com/products/commandbox), our new ColdFusion (CFML) command line interface,
package manager and REPL. You can now use CommandBox to install
dependencies, modules and even ColdBox itself, all from our centralized
code repository: [ForgeBox](http://www.coldbox.org/forgebox). To install ColdBox 4 Bleeding Edge
you can just type:

```bash
box install coldbox-be
```

You can even use CommandBox to generate ColdBox applications, modules,
handlers, etc. It has a plethora of commands to get you started in a
fantastic ColdBox Adventure:

```bash
// Get help on all the ColdBox commands
coldbox help
```

```bash
// create a ColdBox app with TestBox support
coldbox create app MyFirstApp --installTestBox
```

## Internal Library Updates
ColdBox is composed of three internal libraries: WireBox (DI & AOP), CacheBox (Caching) and LogBox (Logging). Below you can find what's new with this release for each library:

- [WireBox 2.0.0](whats_new/wirebox_200.md)
- [CacheBox 2.0.0](whats_new/cachebox_200.md)
- [LogBox 2.0.0](whats_new/logbox_200.md)

Major Updates
-------------

### Performance Updates

The core has been completely revamped by removing ColdFusion 7/8 code,
decoupled from many features that are now available as modules and
rewrites to pure `cfscript` syntax. The end result is the fastest ColdBox
release since our 1.0.0 days. In our initial vanilla load tests, normal
requests would take around 4-6ms to execute.

### RunEvent Caching
The `runEvent` method has been extended to include caching capabilities much similar to what has been available to the `renderView` methods.  This will allow folks to execute internal or widget-like events and be able to use the built-in caching capabilities of ColdBox to cache the results according to the arguments used.  Below is the new signature of the method:

```java
/**
* Executes events with full life-cycle methods and returns the event results if any were returned.
* @event The event string to execute, if nothing is passed we will execute the application's default event.
* @prePostExempt If true, pre/post handlers will not be fired. Defaults to false
* @private Execute a private event if set, else defaults to public events
* @defaultEvent The flag that let's this service know if it is the default event running or not. USED BY THE FRAMEWORK ONLY
* @eventArguments A collection of arguments to passthrough to the calling event handler method
* @cache.hint Cached the output of the runnable execution, defaults to false. A unique key will be created according to event string + arguments.
* @cacheTimeout.hint The time in minutes to cache the results
* @cacheLastAccessTimeout.hint The time in minutes the results will be removed from cache if idle or requested
* @cacheSuffix.hint The suffix to add into the cache entry for this event rendering
* @cacheProvider.hint The provider to cache this event rendering in, defaults to 'template'
*/
function runEvent(
	event="",
	boolean prePostExempt=false,
	boolean private=false,
	boolean defaultEvent=false,
	struct eventArguments={},
	boolean cache=false,
	cacheTimeout="",
	cacheLastAccessTimeout="",
	cacheSuffix="",
	cacheProvider="template"
){
```

Please note that the default cache provider used for event caching is the **template** cache, so it must be a valid ColdBox Enabled Cache Provider.  So if we execute our sample widget below, we will be able to leverage its caching arguments:

```html
<!--- Render with default cache timeouts --->
#runEvent( event="widgets.users", cache=true )#

<!--- Render with specific cache args --->
#runEvent( event="widgets.users", cache=true, cacheTimeout=60 )#

<!--- Render with specific event args --->
#runEvent( event="widgets.users", eventArguments={ filter:true }, cache=true, cacheTimeout=60 )#

```

> **Info** : Internally ColdBox creates an internal hash of the passed in `event` and `eventArguments` arguments for the cache key.  It also leverages the **template** cache for event caching.


### Model called Models

The convention has been updated so it matches the other conventions. You
now must create a **models** folder instead of a **model** folder.

### onInvalidHTTPMethod Handler Convention

We have created a new action convention in all your handlers called
`onInvalidHTTPMethod` which will be called for you if a request is
trying to execute an action in your handler without the right HTTP Verb.
It will be then your job to determine what to do next:

```javascript
function onInvalidHTTPMethod( faultAction, event, rc, prc ){
    return "Yep, onInvalidHTTPMethod works!";
}
```

### HTTP Content Auto Marshalling

The `getHTTPContent` method on the Request Context now takes in two
boolean arguments:

1.  **json**
2.  **xml**

If set, ColdBox will auto-marshall the HTTP Body content from JSON or
XML to native ColdFusion data types.

```javascript
myStruct  = event.getHTTPContent( json=true );
xmlObject = event.getHTTPContent( xml=true );
```

### SES regex route placeholders

You can now define a-la-carte regex matching on route placeholders. If
the route matches with that regex in the placeholder the value will now
be stored in the RC as well:

```javascript
addRoute( pattern = 'format/:extension-regex:(xml|json)' );
```

### New Global View Helper

You now have a new ColdBox core setting `viewsHelper` which is a
template that will be injected and binded to any layout/view that is
rendered. This means that finally you have a template that can be
globally available to any view/layout in your system.

```javascript
coldbox = {
    viewsHelper = "includes/helpers/ViewHelper.cfm"
};
```

### Application Template Java Support

All the new ColdBox application templates have been updated to include a
folder called `lib` inside that is automatically wired for you to
class load Java classes and jars. All you have to do is drop any jar or
class file into this folder and it will be available via
`createobject(“java”)` anywhere in your app.

### New Core Modules

All internal plugins, and lots of functionality, have been refactored out
of the ColdBox Core and into standalone Modules. All of them are in
[ForgeBox](http://www.coldbox.org/forgebox) and have their own Git repositories. Also, all of them are
installable via [CommandBox](http://www.ortussolutions.com/products/commandbox); Our ColdFusion (CFML) CLI, and Package
Manager. This has reduced the ColdBox Core by over **75%** in source size
and complexity. Not only that, but it allows us to be able to release patches and updates for feature functionality without releasing an entire framework release, but just 1 or more modules.

-   ColdBox Debugger

```bash
box install cbdebugger
```

-   Storages

```bash
box install cbstorages
```

-   Feeds

```bash
box install cbfeeds
```

-   Commons

```bash
box install cbcommons
```

-   i18n

```bash
box install cbi18n
```

-   ORM

```bash
box install cborm
```

-   ioc

```bash
box install cbioc
```

-   JavaLoader

```bash
box install cbjavaloader
```

-   AntiSamy

```bash
box install cbantisamy
```

-   MailServies

```bash
box install cbmailservices
```

-   MessageBox

```bash
box install cbmessagebox
```

-   Soap

```bash
box install cbsoap
```

-   Security

```bash
box install cbsecurity
```

-   Validation

```bash
box install cbvalidation
```

### New Anti-Forgery Module

We have created a nice anti-forgery module called **csrf** which can be
found in [ForgeBox](http://www.coldbox.org/forgebox/view/cbcsrf) and in GitHub: <https://github.com/ColdBox/cbox-csrf>. This
module will enhance your ColdBox applications with Anti-Cross Site
Request Forgery capabilities. You can also install it via CommandBox

```bash
box install csrf
```

### ORM Module Updates

As you know by now, all the ColdBox ORM features are available as a
module that can be installed in your application via CommandBox or
downloaded separately. We have done several updates to the ORM
extensions like:

-   Railo multi-datasource support
-   Expanded `createAlias()` method to allow for a **criteria** argument
    which leverages hibernate's ability to do a where statement on a
    join
-   Script updates

### New System Renderer

The ColdBox Renderer plugin has been removed and it is now part of the
core as the system renderer (`coldbox.system.web.Renderer`).

-   It has been migrated to full script and optimized for ColdFusion 9+
    syntax
-   We have also created a new DSL to inject it via WireBox: `coldbox:renderer`
-   You can also add mixins or alter its behavior by talking to its
    WireBox mapping (`Renderer@coldbox`)
-   All handlers/interceptors/views/layouts have access to the renderer
    by calling the `getRenderer()` method in the super type
-   The main ColdBox controller has a new method called `getRenderer()` to retrieve the system renderer

### New Error Template

By default, we are now not showing any exceptions in the ColdBox default
error template for security and encapsulation. You now have to specify the full exception bug template
if you would like to see the exceptions via the `CustomErrorTemplate` setting:

```javascript
coldbox = {
    customErrorTemplate = "/coldbox/system/includes/BugReport.cfm"
};
```

This is to be secure by default. The default template used is
`/coldbox/system/includes/BugReport-Public.cfm`

### Remote Proxies Autowired

You've always been able to get models in a remote proxy (web-accessible
CFC that extends `coldbox.system.remote.ColdBoxProxy`) using the
`getModel()` method. Now you can also autowire your remote proxies using
`cfproperties` just like you do in handlers and WireBox-managed models.

**/remote/myProxy.cfc**

```javascript
component extends="coldbox.system.remote.ColdBoxProxy" {
    property name='myService' inject='myService';

    remote function doRemote() {
        variables.myService.doSomething();
    }
}
```

> **Note** The autowiring only works for web-accessible remote proxies being
directly invoked. You can extend the ColdBoxProxy by another
manually-created CFC (such as an ORM eventHandler) but it won't be
autowired.

### Handlers Are Now Singletons

In pre-4.0.0 applications, all event handlers were cached in CacheBox
with specific timeouts. We have found that this just created extra noise
and complexity for handler CFCs. So now all event handlers will be
cached as singletons by default (unless specified in the `ColdBox.cfc`).

### Bootstrap Enhancement

The ColdBox application bootstrapper, the one used in `Application.cfc`
has been completely updated and renamed to **Bootstrap** instead of
**Coldbox**. This brings in lots of performance enhancements and faster
startup times to your applications. Just use the included application
templates or just update the **Coldbox** reference to **Bootstrap**.

```javascript
// Inheritance
component extends="coldbox.system.Bootstrap"{

}

// Non-Inheritance
component{
    public boolean function onApplicationStart(){
    	application.cbBootstrap = new coldbox.system.Bootstrap( COLDBOX_CONFIG_FILE, COLDBOX_APP_ROOT_PATH, COLDBOX_APP_KEY, COLDBOX_APP_MAPPING );
    	application.cbBootstrap.loadColdbox();
    	return true;
    }
}
```

### Module Enhancements

There has been tremendous focus on modules in this release as we have
moved completely to a modular architecture in the core as well. First of
all, we have moved almost 75% of the source into modules so they can be
installed a-la-carte by developers. Here are some major updates:

-   New `getModuleSettings() & getModuleConfig()` super type methods.
    This allows you to get access to any module setting or configuration
    property rather easily and direct.
-   All module properties are NOT required anymore except the **name**
-   Modules no longer register their **models** folder as scan locations
    to increase performance
-   try/catch around module unloads, so if a module throws an exception
    during unload it will be intercepted, unloaded and then thrown an
    exception. This way it will allow developers to fix the unloading
    issues instead of basically restarting the entire CFML engine to
    make it work

#### Module Inception

We have altered the module services to now allow you to nest modules
within modules up to the Nth degree. Our final move to hierarchical MVC
is complete. Now you can package a module with other modules that can
even contain other modules within. It really opens a great opportunity
for better packaging, delivery and a further break from monolithic
applications. To use, just create a **modules** folder in your module
and drop the modules there as well.

#### Module Config New Properties

The ''ModuleConfig.cfc'' has been updated with several new properties:


| Setting | Type | Required | Default | Description |
| -- | -- | -- | -- | -- |
| **activate** | boolean | false | true |  You can tell ColdBox to register the module but NOT to activate it. By default, all modules activate. |
| **aliases** | array | false | [] | An array of names that can be used to execute the module instead of only the module folder name |
| **autoMapModels** | boolean | false | true | Will automatically map all model objects under the **models** folder in WireBox using `@modulename` as part of the alias. |
| **cfmapping** | string | false | *empty* | The ColdFusion mapping that should be registered for you that points to the root of the module. |
| **disabled** | boolean | false | false | You can manually disable a module from loading and registering |
| **dependencies** | array | false | [] | An array of dependent module names.  All dependencies will be registered and activated FIRST before the module declaring them. |
| **modelNamespace** | string | false | *moduleName* | The name of the namespace to use when registering models in WireBox.  By default it uses the name of the module. |

``` {.coldfusion}
this.autoMapModels = true;
this.modelNamespace = "store";
this.aliases = [ "store", "ecommerce", "shop" ];
this.cfmapping = "cbstore";
this.dependencies = [ "JavaLoader", "CFCouchbase" ];
```

#### Module Dependencies

Modules can now declare other module dependencies. This means that
before the declared module is activated, the dependencies will be
registered and activated **FIRST** and then the declared module will
load.

#### Module Aliases

In pre-4.0.0 ColdBox applications, the way that you executed module
handlers was via its registered module name, which had to be the name of
the module folder on disk. This was ok to a certain point, but it would
cause issues as it was very restrictive to the name on disk. On ColdBox
4 you can now give the module different execution aliases so you can
execute the handler events via the aliases and the module folder name as
well.

#### Module CF Mappings

Every module can now tell ColdBox what ColdFusion mapping to register
for it that points to the module root location on disk when deployed.
This is a huge feature for portability and the ability to influence the
ColdFusion mappings for you via ColdBox.

#### Module Models Auto Mapped

The entire **models** folder will now be automatically mapped for you in
WireBox via the **mapDirectory** call with a namespace attached to the
objects (the name of the module). This way, all models are automatically
mapped for you so you can just use them. Let's say you have a module
called **store**:

```javascript
property name="orderService" inject="OrderService@store";
```

As you can see it adds a `@moduleName` to discover models that come
from a module directly.

> **Hint** You can alter this behavior by setting the
`this.autoMapModels` configuration setting to `false`. You can also
alter the namespace used via the `this.modelNamespace` configuration
property.


#### Module Bundles

You can now bundle your modules into an organizational folder that has
the convention name of **{name}-bundle**. This is mostly for
organizational purposes.

```
coldbox-bundle
  * cbstorages
  * cborm
  * cbsecurity
```
</h3>
