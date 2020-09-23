---
title: 將于 postgresql 超大規模伺服器群組放置在 Kubernetes 叢集節點上
description: 說明形成于 postgresql 超大規模伺服器群組的于 postgresql 實例如何放置於 Kubernetes 叢集節點上
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5da00916a3f7a6a3685b1de1c56dd032355e28fa
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934174"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Azure Arc 啟用的于 postgresql 超大規模伺服器群組放置

在本文中，我們將範例說明如何將 Azure Arc enabled 于 postgresql 超大規模 server 群組的于 postgresql 實例放在裝載它們的 Kubernetes 叢集的實體節點上。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>設定

在此範例中，我們會使用具有四個實體節點的 Azure Kubernetes Service (AKS) 叢集。 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="Azure 入口網站中的4個節點 AKS 叢集":::

藉由執行下列命令，列出 Kubernetes 叢集的實體節點：

```console
kubectl get nodes
```

其中顯示 Kubernetes 叢集中的四個實體節點：

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

架構可以表示為：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Kubernetes 叢集中分組的4個節點的邏輯標記法":::

Kubernetes 叢集會裝載一個 Azure Arc 資料控制器和一個 Azure Arc 啟用的于 postgresql 超大規模伺服器群組。 此伺服器群組是三個于 postgresql 實例的構成：一個協調器和兩個背景工作角色。

使用下列命令列出 pod：

```console
kubectl get pods -n arc3
```
這會產生下列輸出：

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          9h
postgres01-1         3/3     Running   0          9h
postgres01-2         3/3     Running   0          9h
```
這些 pod 都會裝載于 postgresql 實例。 它們會形成 Azure Arc 啟用的于 postgresql 超大規模伺服器群組：

```output
Pod name    Role in the server group
postgres01-0            Coordinator
postgres01-1    Worker
postgres01-2    Worker
```

## <a name="placement"></a>放置
讓我們看看 Kubernetes 如何放置伺服器群組的 pod。 描述每個 pod，並識別所放置之 Kubernetes 叢集的實體節點。 例如，針對協調器，請執行下列命令：

```console
kubectl describe pod postgres01-0 -n arc3
```

這會產生下列輸出：

```output
Name:         postgres01-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

當我們針對每個 pod 執行此命令時，我們會將目前的位置摘要為：

| 伺服器群組角色|伺服器群組 pod|裝載 pod 的 Kubernetes 實體節點 |
|--|--|--|
| 背景工作|postgres01-1|aks-agentpool-42715708-vmss000002 |
| 背景工作|postgres01-2|aks-agentpool-42715708-vmss000003 |

此外也請注意，在 pod 的描述中，每個 pod 所裝載的容器名稱。 例如，針對第二個背景工作，請執行下列命令：

```console
kubectl describe pod postgres01-2 -n arc3
```

這會產生下列輸出：

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

屬於 Azure Arc enabled 于 postgresql 超大規模伺服器群組一部分的每個 pod 都會裝載下列三個容器：

|容器|描述
|----|----|
|`Fluentbit` |資料 * 記錄檔收集器： https://fluentbit.io/
|`Postgres`|Azure Arc enabled Postgresql 超大規模 server 群組的于 postgresql 實例部分
|`Telegraf` |計量收集器： https://www.influxdata.com/time-series-platform/telegraf/

此架構如下所示：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="每個 pod 都會放置在不同的節點上":::

這表示，此時，每個于 postgresql 實例都會構成 Azure Arc 啟用的于 postgresql 超大規模伺服器群組裝載于 Kubernetes 容器內的特定實體主機上。 這是最佳的設定，可協助您在 Azure Arc 啟用的于 postgresql 超大規模伺服器群組中發揮最大效能，因為每個角色 (協調器和背景工作) 會使用每個實體節點的資源。 這些資源不會在數個于 postgresql 角色間共用。

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Scale out Azure Arc enabled 于 postgresql 超大規模

現在，讓我們向外擴充，將第三個背景工作節點新增至伺服器群組，並觀察發生什麼事。 它會建立將在第四個 pod 中裝載的第四個于 postgresql 實例。
若要相應放大，請執行下列命令：

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

這會產生下列輸出：

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

列出部署在 Azure Arc 資料控制器中的伺服器群組，並確認伺服器群組現在以三個背景工作角色執行。 執行命令：

```console
azdata arc postgres server list
```

並觀察它是否已從兩個背景工作角色擴充至三個背景工作：

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

如同我們先前所做的，請注意伺服器群組現在總共使用四個 pod：

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          11h
postgres01-1         3/3     Running   0          11h
postgres01-2         3/3     Running   0          11h
postgres01-3         3/3     Running   0          5m2s
```

並描述新的 pod，以識別其託管 Kubernetes 叢集的實體節點。
執行命令：

```console
kubectl describe pod postgres01-3 -n arc3
```

若要識別主控節點的名稱：

```output
Name:         postgres01-3
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

叢集實體節點上的于 postgresql 實例位置現在是：

|伺服器群組角色|伺服器群組 pod|裝載 pod 的 Kubernetes 實體節點
|-----|-----|-----
|Coordinator|postgres01-0|aks-agentpool-42715708-vmss000000
|背景工作|postgres01-1|aks-agentpool-42715708-vmss000002
|背景工作|postgres01-2|aks-agentpool-42715708-vmss000003
|背景工作|postgres01-3|aks-agentpool-42715708-vmss000000

而且請注意，新背景工作 (postgres01-3) 的 pod 已放在與協調器相同的節點上。 

此架構如下所示：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="與協調器位於相同節點上的第四個 pod":::

為什麼新的背景工作/pod 不是放置在 Kubernetes 叢集 aks-agentpool-42715708-vmss000003 的剩餘實體節點上？

原因是 Kubernetes 叢集的最後一個實體節點實際上裝載了數個 pod，以裝載執行 Azure Arc 啟用的資料服務所需的其他元件。 Kubernetes 評估了最理想的選擇，也就是在排程期間，若要裝載額外的背景工作角色，aks-agentpool-42715708-vmss000000 實體節點。 

使用與上述相同的命令;我們會看到每個實體節點的裝載：

|其他 pod 名稱\* |使用方式|裝載 pod 的 Kubernetes 實體節點
|----|----|----
|啟動載入器-jh48b||aks-agentpool-42715708-vmss000003
|控制-gwmbs||aks-agentpool-42715708-vmss000002
|controldb-0||aks-agentpool-42715708-vmss000001
|controlwd-zzjp7||aks-agentpool-42715708-vmss000000
|logsdb-0|Elasticsearch，從 `Fluentbit` 每個 pod 的容器接收資料|aks-agentpool-42715708-vmss000003
|logsui-5fzv5||aks-agentpool-42715708-vmss000003
|metricsdb-0|InfluxDB，從 `Telegraf` 每個 pod 的容器接收資料|aks-agentpool-42715708-vmss000000
|metricsdc-47d47||aks-agentpool-42715708-vmss000002
|metricsdc-864kj||aks-agentpool-42715708-vmss000001
|metricsdc-l8jkf||aks-agentpool-42715708-vmss000003
|metricsdc-nxm4l||aks-agentpool-42715708-vmss000000
|metricsui-4fb7l||aks-agentpool-42715708-vmss000003
|mgmtproxy-4qppp||aks-agentpool-42715708-vmss000002

> \* Pod 名稱上的尾碼在其他部署上會有所不同。 此外，我們只會在此列出 Azure Arc 資料控制器的 Kubernetes 命名空間內所裝載的 pod。

此架構如下所示：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="不同節點上命名空間中的所有 pod":::

這表示 Azure Arc 啟用的 Postgres 超大規模伺服器群組 (Pod 1) 的協調器節點會與第三個背景工作節點共用相同的實體資源， (Pod 4) 的伺服器群組。 這是可接受的，因為協調器節點通常使用的資源與背景工作節點可能使用的資源比較少。 您可以從這裡推斷出您應謹慎選擇：
- Kubernetes 叢集的大小和其每個實體節點的特性 (memory，vCore) 
- Kubernetes 叢集中的實體節點數目
- 您在 Kubernetes 叢集上裝載的應用程式或工作負載。

在 Kubernetes 叢集上裝載太多工作負載的含意，可能會針對 Azure Arc 啟用的于 postgresql 超大規模伺服器群組進行節流。 如果發生這種情況，您的功能就無法水準調整。 您從系統中取得的效能，不只是實體節點或儲存體系統的放置或實體特性。 您取得的效能也會說明如何設定在 Kubernetes 叢集中執行的每個資源 (包括 Azure Arc 啟用的于 postgresql 超大規模) ，例如您針對記憶體和 vCore 設定的要求和限制。 您可以在指定的 Kubernetes 叢集上裝載的工作負載量，是相對於 Kubernetes 叢集的特性、工作負載的本質、使用者數目、Kubernetes 叢集的作業如何完成 .。。

## <a name="scale-out-aks"></a>Scale out AKS

讓我們示範如何以水準方式調整 AKS 叢集和啟用 Azure Arc 的于 postgresql 超大規模伺服器，讓您能夠從 Azure Arc 啟用的于 postgresql 超大規模的高效能獲得最大效益。
讓我們將第五個節點新增至 AKS 叢集：

:::row:::
    :::column:::
        之前
    :::column-end:::
    :::column:::
        在
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="之前 Azure 入口網站版面配置":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="Azure 入口網站的版面配置":::
    :::column-end:::
:::row-end:::

此架構如下所示：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="更新後 Kubernetes 叢集上的邏輯配置":::

讓我們來看看如何藉由執行下列命令，在新的 AKS 實體節點上裝載 Arc 資料控制器命名空間的 pod：

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

然後讓我們更新系統架構的標記法：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="叢集邏輯圖上的所有 pod":::

我們可以觀察到 Kubernetes 叢集的新實體節點只裝載 Azure Arc 資料服務所需的計量 pod。 請注意，在此範例中，我們只會將焦點放在 Arc 資料控制器的命名空間，而不代表其他 pod。

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>相應放大 Azure Arc 已啟用于 postgresql 超大規模

第五個實體節點尚未裝載任何工作負載。 當我們相應放大 Azure Arc 啟用的于 postgresql 超大規模時，Kubernetes 會將新于 postgresql pod 的放置優化，而且不應該在已裝載更多工作負載的實體節點上共置。 執行下列命令，將已啟用 Azure Arc 的于 postgresql 超大規模從3調整為4個背景工作角色。 在作業結束時，伺服器群組將會構成並散發到五個于 postgresql 實例、一個協調器和四個背景工作角色。

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

這會產生下列輸出：

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

列出部署在資料控制器中的伺服器群組，並確認伺服器群組現在會以四個背景工作角色執行：

```console
azdata arc postgres server list
```

並且觀察到它確實從三到四個背景工作。 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

如同我們先前所做的，觀察伺服器群組現在使用四個 pod：

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          13h
postgres01-1         3/3     Running   0          13h
postgres01-2         3/3     Running   0          13h
postgres01-3         3/3     Running   0          179m
postgres01-4         3/3     Running   0          3m13s
```

伺服器群組的圖形現在是：

|伺服器群組角色|伺服器群組 pod
|----|-----
|Coordinator|postgres01-0
|背景工作|postgres01-1
|背景工作|postgres01-2
|背景工作|postgres01-3
|背景工作|postgres01-4

讓我們來說明 postgres01-4 pod，以識別它所裝載的實體節點：

```console
kubectl describe pod postgres01-4 -n arc3
```

並且觀察它執行的 pod：

|伺服器群組角色|伺服器群組 pod| Pod
|----|-----|------
|Coordinator|postgres01-0|aks-agentpool-42715708-vmss000000
|背景工作|postgres01-1|aks-agentpool-42715708-vmss000002
|背景工作|postgres01-2|aks-agentpool-42715708-vmss000003
|背景工作|postgres01-3|aks-agentpool-42715708-vmss000000
|背景工作|postgres01-4|aks-agentpool-42715708-vmss000004

架構看起來像這樣：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Kubernetes 會以最低使用量排程節點中最新的 pod":::

Kubernetes 已在 Kubernetes 叢集最少載入的實體節點中排程新的于 postgresql pod。

## <a name="summary"></a>摘要

為了充分利用擴充性和調整 Azure Arc 啟用的伺服器群組的效能，您應該避免在 Kubernetes 叢集中的資源爭用：
- 在 Azure Arc 啟用的于 postgresql 超大規模伺服器群組和裝載于相同 Kubernetes 叢集上的其他工作負載之間
- 在構成 Azure Arc 啟用的于 postgresql 超大規模伺服器群組的所有于 postgresql 實例之間

您可以透過數種方式達成此目的：
- Scale out Kubernetes 和 Azure Arc enabled Postgres 超大規模：考慮水準調整 Kubernetes 叢集的方式，就像是調整 Azure Arc 啟用的于 postgresql 超大規模伺服器群組一樣。 針對您新增至伺服器群組的每個背景工作，將實體節點新增至叢集。
- Scale out Azure Arc 啟用的 Postgres 超大規模，而不需向外延展 Kubernetes：設定正確的資源條件約束 (要求和限制記憶體和 vCore) 裝載于 Kubernetes (Azure Arc 啟用的于 postgresql 超大規模) 中，您將可在共置上啟用工作負載的 Kubernetes，並降低資源爭用的風險。 您必須確定 Kubernetes 叢集實體節點的實體特性可採用您所定義的資源限制。 您也應該確保均衡隨著時間而演進，或在 Kubernetes 叢集中新增更多工作負載。
- 使用 Kubernetes 機制 (pod 選取器、親和性和反親和性) 來影響 pod 的放置。

## <a name="next-steps"></a>下一步

[藉由新增更多背景工作節點來向外擴充您的 Azure Arc 啟用的于 postgresql 超大規模伺服器群組](scale-out-postgresql-hyperscale-server-group.md)
