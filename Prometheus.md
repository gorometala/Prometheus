# Prometheus
- Prometheus is a time series database.
- Prometheus is designed to monitor targets. Servers, databases, standalone virtual machines, pretty much everything can be monitored with Prometheus.
- Prometheus expects to retrieve metrics via HTTP calls done to certain endpoints that are defined in Prometheus configuration.
---
![Exporters](https://user-images.githubusercontent.com/15921307/77827936-4b55e000-7121-11ea-921d-308fc77b1aab.png)
- Centralized control : if Prometheus initiates queries to its targets, your whole configuration is done on Prometheus server side and not on your individual targets.
- Prometheus is the one deciding who to scrap and how often you should scrap them.

### Prometheus ecosystem:

1. Alertmanager : Prometheus pushes alerts to the Alertmanager via custom rules defined in configuration files. From there, you can export them to multiple endpoints such as Pagerduty or Slack.
2. Data visualization : similarly to Grafana, you can visualize your time series directly in Prometheus Web UI. You can easily filter and have a concrete overview of what’s happening on your different targets.
3. Service discovery : Prometheus can discover your targets dynamically and automatically scrap new targets on demand. This is particularly handy when playing with containers that can change their addresses dynamically depending on demand.
---
![Architecture](https://user-images.githubusercontent.com/15921307/77828317-6164a000-7123-11ea-9a62-f67ce6282eb7.png)
---
- Prometheus works with key value pairs
  "The key is called a metric. It could be for example a CPU rate or a memory usage. Labels are designed to provide more details to your metrics by appending additional fields to it. You would not simply describe the CPU rate but you would describe the CPU rate for core one located at a certain IP for example."

### Key Value Data Model

- Data Model 

**key**: cpu_usage  

**value**: 10.00

**Labels**: {core="1", ip"172.16.1.1"}

- Data Model Filtering

       cpu_usage	{core!~"1"}

       =: Select labels that are exactly equal to the provided string.

      !=: Select labels that are not equal to the provided string.

      =~: Select labels that regex-match the provided string.

      !~: Select labels that do not regex-match the provided string.

### Metric Types

1. **Counter**
   * Counts elements over time.
   * Counter only goes up or resets
   * A counter is particularly suited to count the number of occurrences of a certain event on a period, i.e the rate at which your metric evolved over time.

2. **Gauges**
   * Gauges are designed to handle values that may decrease over time
   * Gauges are perfect when you want to monitor the current value of a metric that can decrease over time.
   * Gauges can’t be used when you want to see the evolution of your metrics over time. When using gauges, you may essentially lose the sporadic changes of your metrics over time.
   
3. **Histogram**
   * Histogram is a more complex metric type. It provides additional information for your metrics such as the sum of the observations and the count of them.

   * Values are aggregated in buckets with configurable upper bounds. It means that with histograms you are able to :

      * Compute averages : as they represent the fraction of the sum of your values divided by the number of values recorded.
      * Compute fractional measurements on your values : this is a very powerful tool as it allows you to know for a given bucket how many values follow a given criteria. This is especially interesting when you want to monitor proportions or establish quality indicators.
      * In a real world context, I want to be alerted when 20% of my servers respond in more than 300ms or when my servers respond in more than 300ms more than 20% of the time.
      * As soon as proportions are involved, histograms can and should be used.

4. **Sumamries**
      * Summaries are an extension of histograms. Besides also providing the sum and the count of observations, they provide quantiles metrics on sliding windows.

      * As a reminder, quantiles are ways to divide your probability density into ranges of equal probability.

      * Histograms aggregate values over time, giving a sum and a count function that makes it easy to see the evolution of a given metric.

      * On the other hand, summaries exposes quantiles over sliding windows (i.e continuously evolving over time).

              This is particularly handy to get the value that represents 95% of the values recorded over time.
              As soon as proportions are involved, histograms can and should be used
             
 5. **Jobs & Instances**
 
 * Using Prometheus language, a single webserver unit is called an instance.
 * The job would be the fact that you measure the number of HTTP errors for all your instances.
 ![Jobs](https://user-images.githubusercontent.com/15921307/77847130-61b47800-71c3-11ea-95b8-c163abfd313b.png)
 
 6. **PromQL**
 
 * Prometheus also has its own embedded language that facilitates querying and retrieving data from your Prometheus servers : PromQL.
    * Instant vectors: giving a representation of all the metrics tracked at the most recent timestamp;
    * Time ranged vectors: if you want to see the evolution of a metric over time, you can query Prometheus with custom time ranges. The result will be a vector aggregating all the values recorded for the period selected.
    
7. **Instrumentation**

* Instrumentation in Prometheus terms means adding client libraries to your application in order for them to expose metrics to Prometheus.

* Instrumentation can be done for most of the existing programming languages like Python, Java, Ruby, Go and even Node or C# applications.

* While instrumenting, you will essentially create memory objects (like gauges or counters) that you will increment or decrement on the fly.

* Later on, you will choose where to expose your metrics. From there, Prometheus will pick them up and store them into its embedded time series database.

8. **Exporters**

* Those exporters, most of the time exposed as Docker images, are easily configurable to monitor your existing targets. They expose a preset of metrics and often preset dashboards to get your monitoring set in minutes.

   * Examples of exporters include :

      * Database exporters : for MongoDB databases, SQL servers, and MySQL servers.
      * HTTP exporters : for HAProxy, Apache or NGINX servers.
      * Unix exporters : you can monitor system performance using built node exporters that exposes complete system metrics out of the box.

![Exporters](https://user-images.githubusercontent.com/15921307/77847539-747c7c00-71c6-11ea-887e-df94b00947f0.png)

9. **Alerts**

* The alert manager is a standalone tool that binds to Prometheus and run custom alerters.
* Alerts are defined via a configuration file and they define a set of rules on your metrics. If those rules are met in your time series, the alerts are triggered and sent to a predefined destination.

![AlertManager](https://user-images.githubusercontent.com/15921307/77848259-31bda280-71cc-11ea-90e1-7647df9a3f92.png)

