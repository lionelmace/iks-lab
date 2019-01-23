# Logging with LogDNA

IBM Log Analysis with LogDNA is a third-party service that you can include as part of your IBM Cloud architecture to add log management capabilities. IBM Log Analysis with LogDNA is operated by LogDNA in partnership with IBM.

The service plan that you choose for an IBM Log Analysis with LogDNA instance defines the number of days that data is stored and retained in LogDNA. For example, if you choose the Free plan, data is not stored at all. However, if you choose the 7 day plan, data is stored for 7 days and you have access to it through the LogDNA Web UI.

## View logs in the LogDNA dashboard

1. In the [**Observability** category, under Logging](https://cloud.ibm.com/observe/logging), locate the LogDNA service instance.

1. Click **View LogDNA** to open the LogDNA console

1. Use `namespace:<YOUR_KUBERNETES_NAMESPACE>` to view only the logs of the applications deployed to the specified namespace.

1. As you go through the next steps, keep an eye on the LogDNA console for new log statements coming from your apps deployed in this namespace.

![LogDNA dashboard](./images/observability-logging-logdna.png)

{% hint style='tip' %}
Find more about IBM Log Analysis with LogDNA in the [IBM Cloud documentation](https://cloud.ibm.com/docs/services/Log-Analysis-with-LogDNA/index.html#getting-started).
{% endhint %}