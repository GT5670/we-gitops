:_mod-docs-content-type: PROCEDURE

[id="customizing-the-config-file_{context}"]
= Customizing the `config.yaml` file

Use this procedure to customize the `config.yaml` file before integrating products and external services. Customizing this file ensures that your integrations and preferences are preserved during installation.

.Prerequisites

* You have access to the OpenShift Web Console.

* You plan to integrate at least one external product or service (For example, {RHACSShortName} or Quay).

* (Optional) Forked software catalog repository URL. {ProductShortName} provides a catalog of software templates that help developers scaffold applications. To customize these templates, fork the repository before installation.

.. In your browser, go to the link:https://github.com/redhat-appstudio/tssc-sample-templates[{ProductShortName} software catalog repository].

.. Click *Fork* to fork the repository.

... Uncheck the box labeled *Copy the `main` branch only*.

.. When the fork is created, copy its URL and save it in the `private.env` file.

.. In the forked repository, click *main* to open the branch/tag dropdown.

.. Under *Tags*, select the release that matches your {ProductShortName} version.
+
[NOTE]
====
Update your fork periodically to include changes from the upstream repository.
====

.Procedure

. In the OpenShift console, switch to the *Administrator* perspective.

. Go to *Workloads* > *ConfigMaps*.

. From the Project drop down list, select {ProductShortName}.

. Open the `rhtap-cli-config` ConfigMap.

. Select the *YAML* view and navigate to where `config.yaml` parameters are defined.

+
[NOTE]
====
* To avoid reinstalling operator subscriptions already present on your cluster, set `manageSubscription`: `false`.

** `manageSubscription`: `true` (default): The installer manages and installs all required operator subscriptions.
** `manageSubscription`: `false`: The installer skips installing required operator subscriptions.

** Ensure that existing operators are link:https://docs.redhat.com/en/documentation/red_hat_trusted_application_pipeline/{ProductVersion}/html/release_notes_for_red_hat_trusted_application_pipeline_1.5/con_support-matrix_default[compatible with {ProductShortName}]. Incompatible versions may cause installation failure.
====

[discrete]
== sdf
The config.yaml file is structured under a main tssc object. This object has the following key attributes:

* `namespace`: Specifies the default namespace used by the installer, typically set to tssc. This namespace acts as the primary operational area for the installation process.

* `settings`: Defines the settings of the deployment. This can control a wide set of properties.

* `products`: Defines the features or products to be deployed by the installer. Each product is identified by a unique name and a set of properties.

* `dependencies`: Specifies dependencies rolled out by the installer in a specific order defined in the configuration file.

[discrete]
== Customizing the `tssc.products` section

. To use a custom software catalog, set the `catalogURL`:
+
[source,yaml]
----
developerHub:
  properties:
    catalogURL: https://github.com/<your-org>/tssc-sample-templates/blob/release-v1.6.x/all.yaml
----

. To disable automatic installation of components that are already integrated externally:
+
[source,yaml]
----
advancedClusterSecurity:
  enabled: &rhacsEnabled false
  namespace: &rhacsNamespace tssc-acs

quay:
  enabled: &quayEnabled false
  namespace: &quayNamespace tssc-quay
----
+
[NOTE]
====
If you integrate third-party or pre-existing services but do not update the `config.yaml`, the installer still provisions default components. To avoid unintended overrides, update the `config.yaml` to reflect your integration strategy.
====

. To enable the RBAC plugin in Developer Hub, configure the `RBAC` property:
+
[source,yaml]
----
developerHub:
  RBAC:
    enabled: true
    adminUsers:
      - yourGitHubUsername
      - anotherGitHubAdmin
    orgs:
      - your-github-org
----
+
[NOTE]
====
If `adminUsers` or `orgs` are omitted, the installer uses the GitHub credentials configured during integration.
====

. To use a custom namespace instead of the default one, configure the `namespacePrefixes` property in the `redhatDeveloperHub` section of the `config.yaml` file. By default, {ProductShortName} creates four namespaces during installation:

* `tssc-app-ci`: For CI pipeline workloads
* `tssc-app-development`, `tssc-app-stage`, and `tssc-app-prod`: For development, staging, and prod deployments
+
You can customize the prefixes for these namespaces and define additional namespace sets by using the `namespacePrefixes` property. For example, you can configure custom prefixes to generate namespaces such as `my_prefix1-app-ci`, `my_prefix1-app-development`, `my_prefix1-app-stage`, and `my_prefix1-app-prod`.

+
[source,yaml]
----
developerHub:
  namespacePrefixes:
    - my_prefix1
    - my_prefix2
----


. After you complete all necessary changes, select *Save*.
