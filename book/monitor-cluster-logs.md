# Logging with LogDNA

IBM Log Analysis with LogDNA is a third-party service that you can include as part of your IBM Cloud architecture to add log management capabilities. IBM Log Analysis with LogDNA is operated by LogDNA in partnership with IBM.

The service plan that you choose for an IBM Log Analysis with LogDNA instance defines the number of days that data is stored and retained in LogDNA. For example, if you choose the Free plan, data is not stored at all. However, if you choose the 7 day plan, data is stored for 7 days and you have access to it through the LogDNA Web UI.

On the IBM Cloud, to configure cluster-level logging for a Kubernetes cluster, you must provision an instance of the IBM Log Analysis with LogDNA service.

## Provision an instance of Log Analysis with LogDNA service

1. In the [**Observability** category, under Logging](https://cloud.ibm.com/observe/logging), locate the LogDNA service instance.

1. Click IBM Log Analysis with LogDNA. The Observability dashboard opens.

1. Select Create instance.

1. Enter a name for the service instance.

1. Select the resource group that your cluster is in. By default, the Default resource group is set for you.

1. Choose a service plan for your service instance. By default, the Lite plan is selected for you. For more information about other service plans, see Pricing plans.

1. To provision the IBM Log Analysis with LogDNA service in the IBM Cloud resource group where you are logged in, click Create. The Observability dashboard opens and shows the details for your service.

## Configure the LogDNA agent on every worker (node) in a cluster.

## View logs in the LogDNA dashboard

1. Launch the LogDNA dashboard and view logs
To launch the LogDNA dashboard through the IBM Cloud console, complete the following steps:

1. Log in to your IBM Cloud account External link icon.

1. From the menu Menu icon, select Observability.

1. Select Logging. The list of IBM Log Analysis with LogDNA service instances that are available on IBM Cloud is displayed.

1. Select one instance and click View LogDNA. The LogDNA dashboard opens. Note: With the Free service plan, you can tail your latest logs only. For more information, see Viewing logs.




1. Click **View LogDNA** to open the LogDNA console

1. Use `namespace:<YOUR_KUBERNETES_NAMESPACE>` to view only the logs of the applications deployed to the specified namespace.

1. As you go through the next steps, keep an eye on the LogDNA console for new log statements coming from your apps deployed in this namespace.

![LogDNA dashboard](./images/observability-logging-logdna.png)

{% hint style='tip' %}
Find more about IBM Log Analysis with LogDNA in the [IBM Cloud documentation](https://cloud.ibm.com/docs/services/Log-Analysis-with-LogDNA/index.html#getting-started).
{% endhint %}