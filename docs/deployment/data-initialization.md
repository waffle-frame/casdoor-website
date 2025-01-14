---
title: Data Initialization
description: How to initialize Casdoor data from files
keywords: [data initialization, deployment, Intranet]
authors: [leo220yuyaodog]
---

If you are deploying Casdoor with other services as a complete application, you may want to provide an **out-of-the-box** feature for users. This means that users can directly use the application without any configuration.

In such a situation, you can use data initialization to register your service in Casdoor through a configuration file. This file can be pre-defined or dynamically generated by your own service.

Here we give a tutorial for importing or exporting config data.

## Import Config Data

If there is a configuration file named `init_data.json` at the root directory of Casdoor, it will be used to initialize data in Casdoor. All you have to do is place this file in the root directory where Casdoor will run.

If you are using the official Docker image of Casdoor, here are some scripts that can help you to mount `init_data.json` into the container.

A template for `init_data.json` is provided at: [init_data.json.template](https://github.com/casdoor/casdoor/blob/master/init_data.json.template). Rename it to `init_data.json` before using it.

### For Docker

If you deploy Casdoor with Docker, you can use the `volume` command to mount `init_data.json` into the container.

```bash
docker run ... -v /path/to/init_data.json:/init_data.json
```

### For Kubernetes

If you deploy Casdoor with Kubernetes, you can use the `configmap` to store `init_data.json`.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: casdoor-init-data
data:
  init_data.json:
```

You can mount the data into Casdoor `pods` by mounting the `configmap`. You can modify your `deployment` as follows:

```yaml
apiVersion: apps/v1
kind: Deployment
...
spec:
  template:
    ...
    spec:
      containers:
      ...
        volumeMounts:
        - mountPath: /init_data.json
          name: casdoor-init-data-volume
          subPath: init_data.json
      volumes:
      - configMap:
          name: casdoor-init-data
        name: casdoor-init-data-volume
```

## Export Config Data

You can also export all of Casdoor configuration data into a file for data migration. A Go test named `TestDumpToFile()` is provided at: [init_data_dump_test.go](https://github.com/casdoor/casdoor/blob/master/object/init_data_dump_test.go)

```bash
go test object/init_data_dump_test.go
```

After running this Go test, a file named `init_data_dump.json` will be generated in same directory. This file contains your full Casdoor configuration data. If you want to migrate the data into another Casdoor instance, just rename `init_data_dump.json` to `init_data.json` and move it to root directory of target Casdoor folder.

## References

All Casdoor objects supported by the data initialization are as follows:

| Object        | Go Struct                                                                                                                     | Documentation                                                     |
|---------------|-------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| organizations | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/organization.go#L50)         | [doc](https://casdoor.org/docs/organization/overview)             |
| applications  | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/application.go#L59)          | [doc](https://casdoor.org/docs/application/overview)              |
| users         | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/user.go#L49)                 | [doc](https://casdoor.org/docs/user/overview)                     |
| certs         | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/cert.go#L24)                 |                                                                   |
| providers     | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/provider.go#L29)             | [doc](https://casdoor.org/docs/provider/overview)                 |
| ldaps         | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/ldap.go#L21)                 | [doc](https://casdoor.org/docs/ldap/overview)                     |
| models        | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/model.go#L26)                |                                                                   |
| permissions   | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/permission.go#L26)           | [doc](https://casdoor.org/docs/permission/overview)               |
| payments      | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/payment.go#L26)              | [doc](https://casdoor.org/zh/docs/products/payment)               |
| products      | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/product.go#L28)              | [doc](https://casdoor.org/zh/docs/products/product)               |
| resources     | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/resource.go#L25)             | [doc](https://casdoor.org/docs/resources/overview)                |
| roles         | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/role.go#L27)                 | [doc](https://casdoor.org/zh/docs/user/roles)                     |
| syncers       | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/syncer.go#L33)               | [doc](https://casdoor.org/docs/syncer/overview)                   |
| tokens        | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/token.go#L46)                | [doc](https://casdoor.org/docs/token/overview)                    |
| webhooks      | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/webhook.go#L29)              | [doc](https://casdoor.org/docs/webhooks/overview)                 |
| groups        | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/group.go#L27)                | [doc](https://casdoor.org/zh/docs/organization/organization-tree) |
| adapters      | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/adapter.go#L28)              | [doc](https://casdoor.org/zh/docs/permission/adapter)             |
| enforcers     | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/enforcer.go#L26)             |                                                                   |
| plans         | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/plan.go#L25)                 | [doc](https://casdoor.org/zh/docs/pricing/plan)                   |
| pricings      | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/pricing.go#L24)              | [doc](https://casdoor.org/docs/pricing/overview)                  |
| invitations   | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/invitation.go#L25)           | [doc](https://casdoor.org/zh/docs/application/invitation-code)    |
| records       | [struct](https://github.com/casvisor/casvisor-go-sdk/blob/afd3c328ccf117cde693bf6f850d467933ceb1f7/casvisorsdk/record.go#L24) |                                                                   |
| sessions      | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/session.go#L30)              |                                                                   |
| subscriptions | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/subscription.go#L39)         | [doc](https://casdoor.org/zh/docs/pricing/subscription)           |
| transactions  | [struct](https://github.com/casdoor/casdoor/blob/f9ee8a68cb36ef39a551ee49907c239b9d71840c/object/transaction.go#L24)          |                                                                   |

If you still feel confused about filling out this template, you can call the RESTful API or use the debug mode of your browser to see the response of `GetXXX` to these objects. The responses are in the same format as `init_data.json`.
