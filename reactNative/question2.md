# React Native Interview Question 2: React Native New Architecture

You tube video > : https://www.youtube.com/watch?v=ObypwcPI6sQ 

> The React Native New Architecture replaces the legacy Bridge-based communication model with JSI-based interoperability. 
> JSI allows JavaScript and native C++ code to interact more directly.
> Fabric is the new renderer, enabling better integration with native views and modern React concurrent features. 
> TurboModules modernize native modules with typed specifications and lazy loading.   
> Codegen generates the native interfaces and glue code from those specifications. Together, these changes improve interoperability, type safety, and rendering capabilities, although actual performance gains depend on the application


| Old Architecture | New Architecture |
|---|---|
| Uses the asynchronous Bridge for JS/native communication. | Uses JSI-based interoperability. |
| UI updates use the legacy renderer. | Uses the Fabric renderer. |
| Native modules use the legacy module system. | Uses TurboModules. |
| Native interfaces often rely on manually maintained glue code. | Codegen generates integration code from typed specifications. |
| Communication involves serialized messages through the Bridge. | Supports more direct calls and synchronous operations where appropriate. |