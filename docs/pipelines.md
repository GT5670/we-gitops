:_mod-docs-content-type: PROCEDURE

[id="customizing-the-config-file_{context}"]
= Customizing the `config.yaml` file

Customize the `config.yaml` file before integrating external products and services to preserve your integration settings during installation.

.Prerequisites

* You have access to the OpenShift web console.
* You plan to integrate at least one external product or service (for example, {RHACSShortName} or Quay).
* (Optional) You forked the software catalog repository. {ProductShortName} includes a catalog of templates to help developers scaffold applications. Fork this repository if you plan to customize these templates:
.. In your browser, open the link:https://github.com/redhat-appstudio/tssc-sample-templates[{ProductShortName} software catalog repository].
.. Click *Fork*.
... Clear the *Copy the `main` branch only* check box.
.. After creating the fork, copy the URL and save it in the `private.env` file.
.. In your forked repository, click the branch/tag dropdown (default is *main*).
.. Select the release tag that matches your {ProductShortName} version under the *Tags* section.
+
[NOTE]
====
Update your fork periodically to incorporate upstream changes.
====

.Procedure

. Log in to the OpenShift web console and switch to the *Administrator* perspective.
. Click *Workloads* > *ConfigMaps*.
. From the *Project* dropdown menu, select `{ProductShortName}`.
. Click to open the `rhtap-cli-config` ConfigMap.
. Select the *YAML* tab and navigate to the section defining the `config.yaml` parameters.
+
[NOTE]
====
* Set `manageSubscription` to control operator subscription installations:
** `true` (default): The installer manages operator subscriptions.
** `false`: Skips installation of operators already present in your cluster.

* Ensure existing operators are compatible with your {ProductShortName} version. Review compatibility details in the link:https://docs.redhat.com/en/documentation/red_hat_trusted_application_pipeline/{ProductVersion}/html/release_notes_for_red_hat_trusted_application_pipeline_1.5/con_support-matrix_default[compatibility matrix].
====

[discrete]
== Structure of the `config.yaml` file

The `config.yaml` file is structured under the main `tssc` object with these key attributes:

* `namespace`: Defines the default installation namespace (typically `tssc`), serving as the primary operational area.
* `settings`: Controls global deployment settings and properties.
* `products`: Lists products and features for deployment, each identified by a unique name with associated properties.
* `dependencies`: Specifies dependencies that the installer deploys in the order defined in the configuration file.

[discrete]
== Customizing the `tssc.products` section

You can customize products within the `tssc.products` section as follows:

. To specify a custom software catalog URL:
+
[source,yaml]
----
developerHub:
  properties:
    catalogURL: https://github.com/<your-org>/tssc-sample-templates/blob/release-v1.6.x/all.yaml
----

. To disable automatic installation for products integrated externally:
+
[source,yaml]
----
advancedClusterSecurity:
  enabled: false
  namespace: tssc-acs

quay:
  enabled: false
  namespace: tssc-quay
----
+
[NOTE]
====
If external integrations are not correctly reflected in the `config.yaml`, the installer deploys default components. Update the file to prevent unintended provisioning.
====

. To enable role-based access control (RBAC) for Developer Hub:
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
If you do not define `adminUsers` or `orgs`, the installer defaults to GitHub credentials specified during integration.
====

. To define custom namespace prefixes instead of default namespaces, configure `namespacePrefixes`. By default, {ProductShortName} creates these four namespaces during installation:

* `tssc-app-ci`: CI pipeline workloads
* `tssc-app-development`, `tssc-app-stage`, and `tssc-app-prod`: Development, staging, and production workloads

+
You can use custom
