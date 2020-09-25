# Kubernetes Logging with Fluent Bit




## Getting started

To get started run the following commands to create the namespace, service account and role setup:

```
$ kubectl create namespace logging
$ kubectl create -f ./fluent-bit-service-account.yaml
$ kubectl create -f ./fluent-bit-role.yaml
$ kubectl create -f ./fluent-bit-role-binding.yaml
```


#### Fluent Bit to Kafka

Create a ConfigMap that will be used by our Fluent Bit DaemonSet:

```
$ kubectl create -f ./output/kafka/fluent-bit-configmap.yaml
```

Fluent Bit DaemonSet ready to be used with Kafka on a normal Kubernetes Cluster:

```
$ kubectl create -f ./output/kafka/fluent-bit-ds.yaml
```


## Details

The default configuration of Fluent Bit makes sure of the following:

- Consume all containers logs from the running Node.
- The [Tail input plugin](http://fluentbit.io/documentation/0.12/input/tail.html) will not append more than __5MB__  into the engine until they are flushed to the Elasticsearch backend. This limit aims to provide a workaround for [backpressure](http://fluentbit.io/documentation/0.13/configuration/backpressure.html) scenarios.
- The Kubernetes filter will enrich the logs with Kubernetes metadata, specifically _labels_ and _annotations_. The filter only goes to the API Server when it cannot find the cached info, otherwise it uses the cache.
- The default backend in the configuration is Elasticsearch set by the [Elasticsearch Output Plugin](http://fluentbit.io/documentation/0.13/output/elasticsearch.html). It uses the Logstash format to ingest the logs. If you need a different Index and Type, please refer to the plugin option and do your own adjustments.
- There is an option called __Retry_Limit__ set to False that means if Fluent Bit cannot flush the records to Elasticsearch it will re-try indefinitely until it succeeds.

