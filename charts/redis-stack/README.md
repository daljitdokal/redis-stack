# Redis-stack

Redis 7.2 is  most far-reaching release. It encompasses a broad set of new features, and a significant investment in the functionality that supports AI initiatives. In each of these enhancements, you’ll notice a strong theme of making it easier for developers to use Redis, making it run even faster, and making it easier to achieve innovative results.

And we are committing to deliver all of these capabilities through every distribution channel all at once, with an approach we call the Unified Redis Release.

## Helm install

```
export namespace="redis"

# oc new-project ${namespace}
oc project ${namespace}

helm upgrade --install redis-stack ./charts/redis-stack -n ${namespace}
```

## Helm uninstall

```
helm history redis-stack
```

## Helm uninstall

```
helm delete redis-stack
```