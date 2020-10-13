---
title: 顯示已啟用 Arc 的于 postgresql 超大規模伺服器群組設定
titleSuffix: Azure Arc enabled data services
description: 顯示已啟用 Arc 的于 postgresql 超大規模伺服器群組設定
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ceab9af7e6556b2d957fafce8cd89d4a0daf9508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936790"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>顯示已啟用 Arc 的于 postgresql 超大規模伺服器群組設定

本文說明如何 (s) 顯示伺服器群組的設定。 這樣做的方法是，預期您可能會要求自己的一些問題，並回答這些問題。 有時可能會有幾個有效的答案。 本文將簡報最常見或實用的專案。 它會依主題將這些問題分組：

- 從 Kubernetes 的觀點來看
- 從已啟用 Azure Arc 的資料服務觀點來看

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>從 Kubernetes 的觀點來看

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc enabled 于 postgresql 超大規模使用多少 pod？

列出 Postgres 類型的 Kubernetes 資源。 執行命令：

```console
kubectl get postgresqls [-n <namespace name>]
```

此命令的輸出會顯示已建立的伺服器群組清單。 它會指出 pod 的數目。 例如：

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

此範例顯示已建立2個伺服器群組，且每個群組都執行于3個 pod (1 個協調器 + 2 個背景工作) 。 這表示在此 Azure Arc 資料控制器中建立的伺服器群組會使用6個 pod。

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 啟用的于 postgresql 超大規模伺服器群組會使用哪些 pod？

執行：

```console
kubectl get pods [-n <namespace name>]
```

這會傳回 pod 清單。 您將會根據您提供給這些伺服器群組的名稱，看到您伺服器群組所使用的 pod。 例如：

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

在此範例中，裝載所建立之兩個伺服器群組的六個 pod 為：
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>伺服器群組有哪些角色要使用哪個伺服器群組 pod？

任何做為尾碼的 pod 名稱都 `-0` 代表協調器節點。 任何節點名稱的後置字元是 `-x` 1 或更大的背景工作節點。 在上述範例中：
- 協調員為： `postgres01-0` 、 `postgres02-0`
- 背景工作角色為： `postgres01-2` 、 `postgres01-2` 、 `postgres02-1` 、 `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Pod 的狀態為何？

執行 `kubectl get pods` 並查看資料行 `STATUS`

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>使用 (Pvc) 的永久性磁片區宣告為何？ 

若要瞭解使用哪些 Pvc 以及用於資料、記錄和備份的程式，請執行： 

```console
kubectl get pvc [-n <namespace name>]
```

根據預設，PVC 名稱的前置詞會指出其使用方式：

- `backups-`...：是用於備份的 PVC
- `data-`...：是用於資料檔案的 PVC
- `logs-`...：是用於交易記錄/WAL 檔案的 PVC

例如：

```output
NAME                   STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>從已啟用 Azure Arc 的資料服務觀點來看：

* Arc 資料控制器中建立了多少個伺服器群組？
* 名稱是什麼？
* 他們使用多少個背景工作節點？
* 他們執行的 Postgres 是哪個版本？

使用下列其中一個命令。
- **使用 kubectl：**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   例如，它會產生下列輸出，其中每一行都是 `servergroup` 。 以下顯示的名稱結構會形成：

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   上面的輸出顯示2個 Postgres 版本12的伺服器群組。 如果版本是 Postgres 11，則會改為 postgresql-11.arcdata.microsoft.com .CRD 的名稱。

   每個節點都會在三個節點/pod 上執行：1個協調器和2個背景工作角色。

- **使用 azdata：**

執行下列命令。 輸出會顯示類似于 kubectl 所顯示的資訊：

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>使用多少記憶體和虛擬核心，以及為群組建立了哪些延伸模組？

執行下列其中一個命令

**使用 Kubectl：**

使用 kubectl 來描述 Postgres 資源。 若要這樣做，您需要其 Kubernetes 資源的種類 (名稱 (.CRD) 對應的 Postgres 版本，如上所示) 和伺服器群組的名稱。
此命令的一般格式為：

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

例如：

```console
kubectl describe postgresql-12/postgres02
```

此命令會顯示伺服器群組的設定：

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Backups:
    Delta Minutes:  3
    Full Minutes:   10
    Tiers:
      Retention:
        Maximums:
          6
          512MB
        Minimums:
          3
      Storage:
        Volume Size:  1Gi
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Shards:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

讓我們在上面所示的描述中，呼叫一些特定的重點 `servergroup` 。 這會告訴我們此伺服器群組的相關資訊？

- 它是 Postgres 的12版： 
   > 類別         `postgresql-12`
- 它是在2020年8月建立的：
   > 建立時間戳記：  `2020-08-31T21:01:07Z`
- 此伺服器群組中建立了兩個 Postgres 延伸模組： `citus` 和 `pg_stat_statements`
   > 引擎：延伸模組：名稱：  `citus` 名稱：  `pg_stat_statements`
- 它使用兩個背景工作節點
   > 調整：分區：  `2`
- 保證每個節點使用1個 cpu/vCore 和 512 MB 的 Ram。 它會使用超過4個 cpu/虛擬核心和1024MB 的記憶體：
   > 排程：預設：資源：限制： Cpu：4記憶體：1024Mi 要求： Cpu：1記憶體：512Mi
 - 它可供查詢，而且沒有任何問題。 所有節點都已啟動且正在執行：
   > 地位：。。。就緒 pod：3/3 狀態：就緒

**使用 azdata：**

命令的一般格式為：

```console
azdata arc postgres server show -n <server group name>
```

例如：

```console
azdata arc postgres server show -n postgres02
```

傳回格式和內容非常類似 kubectl 所傳回的輸出。

```output
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "backups": {
      "deltaMinutes": 3,
      "fullMinutes": 10,
      "tiers": [
        {
          "retention": {
            "maximums": [
              "6",
              "512MB"
            ],
            "minimums": [
              "3"
            ]
          },
          "storage": {
            "volumeSize": "1Gi"
          }
        }
      ]
    },
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "shards": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>後續步驟
- [瞭解 Azure Arc enabled 于 postgresql 的概念超大規模](concepts-distributed-postgres-hyperscale.md)
- [瞭解如何向外延展 (在伺服器群組中新增背景工作節點) ](scale-out-postgresql-hyperscale-server-group.md)
- [瞭解如何擴大/縮小 (增加或減少) 伺服器群組的記憶體和/或虛擬核心](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [瞭解儲存體設定](storage-configuration.md)
- [瞭解如何監視資料庫實例](monitor-grafana-kibana.md)
- [在啟用 Azure Arc 的于 postgresql 超大規模伺服器群組中使用於 postgresql 延伸模組](using-extensions-in-postgresql-hyperscale-server-group.md)
- [為已啟用 Azure Arc 的 PostgreSQL 超大規模資料庫伺服器群組設定安全性](configure-security-postgres-hyperscale.md)
