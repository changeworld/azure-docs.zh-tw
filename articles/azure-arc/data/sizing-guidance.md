---
title: 調整大小指引
description: 規劃 Azure Arc 啟用的資料服務部署的大小。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e7f2e445c3e4e8df7420c0587e156968f3a2c92
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542672"
---
# <a name="sizing-guidance"></a>調整大小指導方針

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>大小調整指引總覽

規劃 Azure Arc 資料服務的部署時，您應該規劃執行 Azure Arc 資料控制器所需的計算、記憶體和儲存體的正確數量，以及您將部署的 SQL 受控實例和于 postgresql 超大規模伺服器群組數目。  由於 Kubernetes 上已部署 Azure Arc 啟用的資料服務，因此您可以透過新增額外的計算節點或儲存體，彈性地將額外的容量新增至 Kubernetes 叢集。  本指南將提供最低需求的指引，以及針對某些常見需求提供建議大小的指引。

## <a name="general-sizing-requirements"></a>一般調整大小需求

> [!NOTE]
> 如果您不熟悉本文中的概念，您可以閱讀更多有關 [Kubernetes 資源治理](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) 和 [Kubernetes 大小標記法](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)的資訊。

核心數目必須是大於或等於1的整數值。

使用 azdata 進行部署時，應使用兩個數字的乘冪來指定記憶體值，也就是使用尾碼： Ki、Mi 或 Gi。

如果有指定，限制值一律必須大於要求值。

限制核心的值為 SQL 受控實例和于 postgresql 超大規模伺服器群組上的可計費度量。

## <a name="minimum-deployment-requirements"></a>最低部署需求

Azure Arc 啟用的資料服務部署的大小下限可視為 Azure Arc 資料控制器加上一個 SQL 受控實例，加上一個具有兩個背景工作節點的于 postgresql 超大規模伺服器群組。  針對此設定，您的 Kubernetes 叢集上需要至少 16 GB 的 RAM 和4核心的 _可用_ 容量。  您應該確定您的 Kubernetes 節點大小下限為 8 GB RAM 和4個核心，以及所有 Kubernetes 節點上可用的 16 GB RAM 總計容量。  例如，您可以在 32 GB RAM 和4個核心上有1個節點，也可能有2個節點具有 16 GB RAM 和4個核心。

如需儲存體大小調整的詳細資訊，請參閱 [存放裝置](storage-configuration.md) 設定文章。

## <a name="data-controller-sizing-details"></a>資料控制器大小調整詳細資料

資料控制器是部署至 Kubernetes 叢集的 pod 集合，可提供 API、控制器服務、啟動載入器，以及監視資料庫與儀表板。  下表說明記憶體和 CPU 要求和限制的預設值。

|Pod 名稱|CPU 要求|記憶體要求|CPU 限制|記憶體限制|備註|
|---|---|---|---|---|---|
|**扇**|100m|100Mi|200m|200Mi||
|**control**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|1千萬個|100Mi|100m|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100m|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100m|200Mi|200m|300Mi|Metricsdc 是在叢集中的每個 Kubernetes 節點上建立的 daemonset。  表格中的數位是 _每個節點_ 。 如果您在建立資料控制器之前，在部署設定檔中設定 allowNodeMetricsCollection = false，將不會建立 metricsdc daemonset。|
|**metricsui**|2000萬|200Mi|500m|200Mi||
|**mgmtproxy**|200m|250Mi|500m|500Mi||

您可以覆寫部署設定檔檔案或 datacontroller YAML 檔案中 controldb 和控制 pod 的預設設定。  範例：

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

如需儲存體大小調整的詳細資訊，請參閱 [存放裝置](storage-configuration.md) 設定文章。

## <a name="sql-managed-instance-sizing-details"></a>SQL 受控實例調整大小詳細資料

每個 SQL 受控實例都必須具有下列最少資源要求：
- 記憶體：2Gi
- 核心：1

所建立的每個 SQL 受控實例 pod 都有三個容器：

|容器名稱|CPU 要求|記憶體要求|CPU 限制|記憶體限制|備註|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|未指定|未指定|除了針對 SQL 受控實例所指定的要求 _之外_ ，fluentbit 容器資源要求。||
|arc-sqlmi|使用者指定或未指定。|使用者指定或未指定。|使用者指定或未指定。|使用者指定或未指定。||
|collectd|未指定|未指定|未指定|未指定||

所有持續性磁片區的預設磁片區大小為5Gi。

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>于 postgresql 超大規模伺服器群組調整大小詳細資料

每個于 postgresql 超大規模伺服器群組節點都必須具有下列最少資源要求：
- 記憶體：256Mi
- 核心：1

所建立的每個于 postgresql 超大規模伺服器群組協調器或背景工作 pod 都有三個容器：

|容器名稱|CPU 要求|記憶體要求|CPU 限制|記憶體限制|備註|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|未指定|未指定|Fluentbit 容器資源要求是針對于 postgresql 超大規模伺服器群組節點所指定的要求 _以外_ 的要求。|
|postgres|使用者指定或未指定。|使用者指定或 256Mi (預設) 。|使用者指定或未指定。|使用者指定或未指定。||
|telegraf|未指定|未指定|未指定|未指定||

## <a name="cumulative-sizing"></a>累計大小

Azure Arc 啟用的資料服務所需之環境的整體大小，主要是要建立之資料庫實例的數目和大小的功能。  整體大小可能很難事先預測，以瞭解實例數目將會增加和縮減，而且每個資料庫實例所需的資源數量將會變更。

給定 Azure Arc 啟用的資料服務環境的基準大小是資料控制器的大小，需要4核心和 16 GB 的 RAM。  從該處，您可以在資料庫實例所需的累計核心和記憶體累計總數之上加上。  針對 SQL 受控實例，pod 數目等於所建立的 SQL 受控實例數目。  針對于 postgresql 超大規模伺服器群組，pod 數目相當於背景工作節點數目，以及協調器節點的數目。  例如，如果您有一個具有3個背景工作節點的于 postgresql 伺服器群組，pod 總數將會是4。

除了您針對每個資料庫實例所要求的核心和記憶體之外，您還應該新增代理程式容器的核心和 RAM 250Mi 250m。

以下是大小調整計算的範例。

需求：

- **"SQL1"** ：1個具有 16 GB RAM 的 SQL 受控實例，4個核心
- **"SQL2"** ：1個具有 256 GB RAM 的 SQL 受控實例，16個核心
- **"Postgres1"** ：1于 postgresql 超大規模 server 群組，其中有4個背景工作，位於 12 GB 的 RAM，4個核心

調整大小計算：

- "SQL1" 的大小為：1個 pod * ( [16 個 Gi RAM、4個核心] + [250Mi RAM、250m 核心] ) 適用于每個 pod 的代理程式 = 16.25 Gi RAM、4.25 核心。
- "SQL2" 的大小為：1個 pod * ( [256 Gi RAM、16核心] + + [250Mi RAM，250m 核心] ) 適用于每個 pod 的代理程式 = 256.25 Gi RAM、16.25 核心。
- SQL 1 和 SQL 2 的總大小為： (16.25 GB + 256.25 Gi) = 272.5 GB RAM、 (4.25 核心 + 16.25 核心) = 20.5 核心。

- "Postgres1" 的大小為： (4 個背景工作 pod + 1 個協調器 pod) * ( [12 GB RAM，4核心] + [250Mi RAM，250m 核心] ) 適用于每個 pod 的代理程式 = 61.25 GB RAM、21.25 核心。

- 資料庫實例所需的總容量為： 272.5 GB RAM、適用于 SQL + 61.25 GB RAM 的20.5 核心、于 postgresql 的21.25 核心超大規模 = 333.75 GB RAM、42.5 核心。

- 資料庫實例和資料控制器所需的總容量為： 333.75 GB RAM、資料庫實例的42.5 核心 + 16 GB RAM、4核心（資料控制器 = 349.75 GB RAM、46.5 核心）。

如需儲存體大小調整的詳細資訊，請參閱 [存放裝置](storage-configuration.md) 設定文章。

## <a name="other-considerations"></a>其他考量

請記住，核心或 RAM 的指定資料庫實例大小要求不能超過叢集中 Kubernetes 節點的可用容量。  例如，如果您在 Kubernetes 叢集中擁有的最大 Kubernetes 節點是 256 GB RAM 和24個核心，您將無法建立要求為 512 GB RAM 和48核心的資料庫實例。  

最好在 Kubernetes 節點上維持至少25% 的可用容量，讓 Kubernetes 能夠有效率地排程要建立的 pod，以及允許彈性調整和長期成長的需求。  

在調整大小的計算中，請記得新增 Kubernetes 系統 pod 的資源需求，以及可能與相同 Kubernetes 叢集上的 Azure Arc 啟用的資料服務共用容量的任何其他工作負載。

若要在規劃的維護和嚴重損壞持續性期間維持高可用性，您應該規劃叢集中至少有一個 Kubernetes 節點在任何給定的時間點都無法使用。  Kubernetes 會嘗試重新排定在指定節點上執行的 pod，以進行維護或由於失敗。  如果剩餘的節點上沒有可用的容量，這些 pod 將不會重新排程以供建立，直到有可用的容量為止。  使用大型資料庫實例時，請特別小心。  例如，如果只有一個 Kubernetes 節點夠大，足以滿足大型資料庫實例的資源需求，且該節點失敗，則 Kubernetes 將無法將該資料庫實例 pod 排程至另一個 Kubernetes 節點。

請記住 [Kubernetes 叢集大小的最大限制](https://kubernetes.io/docs/setup/best-practices/cluster-large/) 。

您的 Kubernetes 系統管理員可能已在命名空間/專案上設定 [資源配額](https://kubernetes.io/docs/concepts/policy/resource-quotas/) 。  在規劃資料庫實例大小時，請記住這些因素。
