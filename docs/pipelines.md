# Gitops Pipelines

[id="customizing-the-config-file_{context}"]
= Customizing the `config.yaml` file

Use this procedure to customize the `config.yaml` file before integrating products and external services. Customizing this file ensures that your integrations and preferences are preserved during installation.

== Prerequisites

* You have access to the OpenShift Web Console.
* You plan to integrate at least one external product or service. For example, {RHACSShortName} or Quay.
* (Optional) You have forked the software catalog repository. {ProductShortName} uses this catalog to scaffold developer applications.

.. In your browser, go to the link:https://github.com/redhat-appstudio/tssc-sample-templates[{ProductShortName} software catalog repository].
.. Click *Fork*.
... Clear the checkbox labeled *Copy the `main` branch only*.
.. After the fork is created, copy its URL and save it in the `private.env` file.
.. In the forked repository, click *main* to open the branch or tag dropdown.
.. Under *Tags*, select the release that matches your {ProductShortName} version.

[NOTE]
====
Update your fork regularly to include changes from the upstream repository.
====

== Procedure

. In the OpenShift console, switch to the *Administrator* perspective.
. Go to *Workloads* > *ConfigMaps*.
. From the *Project* list, select the `{ProductShortName}` namespace.
. Open the `rhtap-cli-config` ConfigMap.
. Select the *YAML* view and locate the section that defines `config.yaml` parameters.

[[managesubscription-note]]
[NOTE]
====
To avoid reinstalling operator subscriptions already present on your cluster, set `manageSubscription: false`.

* `manageSubscription: true` (default): The installer manages and installs all required operator subscriptions.
* `manageSubscription: false`: The installer skips installing required operator subscriptions.

Ensure that existing operators are link:https://docs.redhat.com/en/documentation/red_hat_trusted_application_pipeline/{ProductVersion}/html/release_notes_for_red_hat_trusted_application_pipeline_1.5/con_support-matrix_default[compatible with {ProductShortName}]. Incompatible versions may cause installation failure.
====

. Update the following fields as needed:

.. To use a custom software catalog, set the `catalogURL`:
+
[source,yaml]
----
developerHub:
  properties:
    catalogURL: https://github.com/<your-org>/tssc-sample-templates/blob/release-v1.6.x/all.yaml
----

.. To disable automatic installation of components that are already integrated externally:
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

[[rbac-plugin]]
.. To enable the RBAC plugin in Developer Hub, configure the `RBAC` property:
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

[[customize-namespaces]]
.. To customize the default namespace prefixes for your environments:
+
[source,yaml]
----
developerHub:
  namespacePrefixes:
    - my_prefix1
    - my_prefix2
----
+
{ProductShortName} generates the following namespaces by default:
* `tssc-app-ci` for CI workloads
* `tssc-app-development`, `tssc-app-stage`, and `tssc-app-prod` for deployment stages

Using the `namespacePrefixes` property, you can generate custom environments such as `my_prefix1-app-ci`, `my_prefix1-app-development`, and so on.

. After making your changes, click *Save*.
