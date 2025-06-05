. To use custom namespace prefixes instead of the default ones, configure the `namespacePrefixes` property. By default, {ProductShortName} creates the following namespaces during installation:

* `tssc-app-ci`: For continuous integration pipeline workloads
* `tssc-app-development`, `tssc-app-stage`, and `tssc-app-prod`: For development, staging, and production deployments

You can customize the prefixes for these namespaces and define additional sets. For example:
\+
\[source,yaml]
--------------

developerHub:
namespacePrefixes:
\- my\_prefix1
\- my\_prefix2
--------------

This configuration generates the following namespaces:

* `my_prefix1-app-ci`, `my_prefix1-app-development`, `my_prefix1-app-stage`, `my_prefix1-app-prod`
* `my_prefix2-app-ci`, `my_prefix2-app-development`, `my_prefix2-app-stage`, `my_prefix2-app-prod`
