# Using Multiple Containers in a Kubernetes Pod

Multi-container Pods in Kubernetes use additional containers within the same Pod to solve technical challenges. This lab will allow you to get hands-on with multi-container Pods. You will build expertise as you design your own multi-container Pod in order to address a real-world issue.

Welcome to HiveCorp, a software design company that is totally not run by bees! Your team is working on part of the company's honey management app. Sorry, money management, not honey management!

Your app reaches out to a Service maintained by another team. Some recent changes by the other team have led to some issues:

Your team's app Pods need to delay startup by a few seconds to allow time for some auxiliary systems to react to the Pod's presence.

The other team wants to change the external Port exposed by their Service, and your team's app will need to be updated accordingly. Use an ambassador container so that this port is more easily configurable in the future.

Solve these problems by modifying your app's Deployment. Use an init container to delay your app's startup, and use an ambassador container to allow your app to use the new Service port in a configurable way.

## Learning Objectives

1. There is a Service called hive-svc located in the 1-multiple-containers-pod Namespace. Modify this service so that it listens on port 9076.

2. The app is managed by the app-gateway Deployment located in the 1-multiple-containers-pod Namespace. Add an init container to the Pod template so that startup will be delayed by 10 seconds. You can do this by using the busybox:stable image and running the command sh -c sleep 10.

3. Find the app-gateway Deployment located in the 1-multiple-containers-pod Namespace. Add an ambassador container to the Deployment's Pod template using the haproxy:2.4 image.
Supply the ambassador container with an haproxy configuration file at /usr/local/etc/haproxy/haproxy.cfg. There is already a ConfigMap called haproxy-config in the 1-multiple-containers-pod Namespace with a pre-configured haproxy.cfg file.
You will need to edit the command of the main container so that it points to localhost instead of hive-svc.

## Review

The main difficulty I found with this exercise was how to declare a Volume for a Deployment and mount the Volume into a Container for a ConfigMap.

The app-gateway Deployment was Erroring because the haproxy-config ConfigMap wasn't being mounted as a file.

This was solved by the following specification:

```Text
volumes:
      - name: ambassador-config
        configMap:
          name: haproxy-config
          items:
          - key: haproxy.cfg
            path: haproxy.cfg
```

Declared the name of the volume.
Declared the type of volume (configMap).
Specified the name of the ConfigMap to use.
Specified the items of the ConfigMap to use:

- items if unspecified, each key-value pair in the Data field of the referenced ConfigMap will be projected into the volume as a file whose name is the key and content is the value.
