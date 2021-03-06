# How do they work?

Interceptors are CFCs that extend the ColdBox Interceptor class (`coldbox.system.Interceptor`), implement a configuration method called `configure()`, and then contain methods for the events it will listen for. All interceptors are treated as **singletons** in your application, so make sure they are thread safe and var scoped.

![](../images/ColdBoxMajorClasses.jpg)

```cfml
/**
* My Interceptor
*/
component extends="coldbox.system.Interceptor"{
	
	function configure(){}

	function preProcess(event, interceptData, buffer){}
}
```

You can use CommandBox to create interceptors as well:

```bash
coldbox create interceptor help
```


## Registration
Interceptors can be registered in your `Coldbox.cfc` configuration file using the `interceptors` struct, or they can be registered manually via the system's interceptor service.

In `ColdBox.cfc`:
```cfml
// Interceptors registration
interceptors = [
	{ 
		class   = "cfc.path",
		name    = "unique name/wirebox ID",
		properties = { 
			// configuration
		}
	}
];
```

Registered manually:
```cfml
controller.getInterceptorService().registerInterceptor( interceptorClass="cfc.path" );
```

## The `configure()` Method
The interceptor has one important method that you can use for configuration called `configure()`. This method will be called right after the interceptor gets created and injected with your interceptor properties. 

> **Info** These properties are local to the interceptor only!

As you can see on the diagram, the interceptor class is part of the ColdBox framework super type family, and thus inheriting the functionality of the framework.
