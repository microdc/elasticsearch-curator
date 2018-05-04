# Introduction
A simple elasticsearch-curator docker image to use to clear down ES in microdc.

There is no official curator docker image, so I create one here.

It is autobuilt by https://hub.docker.com/r/microdc/elasticsearch-curator on each tag-push and
push to master branch.

It is intended for use in a k8s cronjob. Its default config is to delete indices > 7 days. This
can be overridden with a config map which mounts onto the /etc/elasticsearch-curator/ directory,
and replaces files curator.yaml and actions.yaml .

# Examples

## One-off run:
```bash
kubectl run --namespace instrumentation --image=microdc/elasticsearch-curator:0.0.8 -i --rm --restart=Never elasticsearch-curator
```

## k8s cron-job:
```
---
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: curator
  namespace: instrumentation
spec:
  schedule: "@daily"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: curator
            image: microdc/elasticsearch-curator:0.0.6
          restartPolicy: OnFailure
```
