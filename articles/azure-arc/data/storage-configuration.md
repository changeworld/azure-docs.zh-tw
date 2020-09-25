---
title: 儲存體組態
description: 說明 Azure Arc 啟用的資料服務儲存體設定選項
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: c1560325f21fd60e6bdb2a64eb987359a7246ff2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317322"
---
# <a name="storage-configuration"></a>儲存體組態

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Kubernetes 儲存體概念

Kubernetes 提供基礎虛擬化技術堆疊的基礎結構抽象層 (選用) 和硬體。 Kubernetes 將儲存體抽象化的方法是透過 **[儲存類別](https://kubernetes.io/docs/concepts/storage/storage-classes/)**。 布建 pod 時，可以指定儲存類別以用於每個磁片區。 布建 pod 時，會呼叫儲存體類別 **[布建程式](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** 來布建儲存體，然後在該布建的存放裝置上建立 **[永久性磁片](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** 區，然後透過 **[永久性磁片](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)** 區宣告將 pod 掛接至永久性磁片區。

Kubernetes 可讓存放基礎結構提供者插入驅動程式， (也稱為 "附加元件" ) 擴充 Kubernetes。 儲存體附加元件必須符合 **[容器存放裝置介面標準](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)**。 您可以在這份非最終 **[的 CSI 驅動程式清單](https://kubernetes-csi.github.io/docs/drivers.html)** 中找到數十種附加元件。 您所使用的 CSI 驅動程式將取決於各種因素，例如您是在雲端託管、受控 Kubernetes 服務中執行，還是在您要用於硬體的 OEM 提供者中執行。

您可以藉由執行下列命令來查看 Kubernetes 叢集中設定的儲存類別：

``` terminal
kubectl get storageclass
```

Azure Kubernetes Service (AKS) 叢集的輸出範例：

``` terminal
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

您可以藉由執行下列命令來取得儲存類別的詳細資料：

``` terminal
kubectl describe storageclass\<storage class name>
```

範例：

``` terminal
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

您可以藉由執行下列命令來查看目前布建的永久性磁片區和持續性磁片區宣告：

``` terminal
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

顯示永久性磁片區的範例：

``` terminal

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

顯示持續性磁片區宣告的範例：

``` terminal

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>選擇儲存體設定時應考慮的因素

選取正確的儲存類別對於資料復原和效能來說非常重要。 選擇錯誤的儲存類別可讓您的資料在硬體故障時，有資料遺失總計的風險，或可能導致較不理想的效能。

通常有兩種類型的儲存體：

- **本機儲存體** -在指定節點上的本機硬碟上布建的儲存體。 這種儲存體在效能方面可能很理想，但需要透過將資料複寫到多個節點，以特別設計資料冗余。
- **遠端共用儲存體** -布建在某些遠端存放裝置（例如 SAN、NAS 或雲端儲存體服務，例如 EBS 或 Azure 檔案儲存體）的儲存體。 這種儲存體通常會自動提供資料冗余，但通常不會像本機儲存體一樣快。

> [!NOTE]
> 現在，如果您使用 NFS，在部署 Azure Arc 資料控制器之前，您必須先將部署設定檔檔案中的 allowRunAsRoot 設定為 true。

### <a name="data-controller-storage-configuration"></a>資料控制器儲存體設定

資料服務 Azure Arc 中的某些服務相依于設定為使用遠端共用存放裝置，因為服務沒有複寫資料的能力。 這些服務可在資料控制站 pod 的集合中找到：

|**服務**|**持續性磁片區宣告**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**控制器 SQL 實例**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**控制器 API 服務**|`<namespace>/data-controller`|

在布建資料控制站時，每個持續性磁片區所使用的儲存類別都是藉由傳遞--storage 類別來指定的 |-sc 參數指向 `azdata arc dc create` 命令，或設定所使用之部署範本檔案中 control.js的儲存類別。

現成提供的部署範本具有指定的預設儲存類別，適用于目標環境，但可以在部署時加以覆寫。 請參閱 [變更部署設定檔](create-data-controller.md) 的詳細步驟，以在部署時變更資料控制站 pod 的儲存類別設定。

如果您使用--storage 類別設定儲存類別 |-sc 參數：儲存類別將用於記錄和資料儲存類別。 如果您在部署範本檔案中設定儲存類別，您可以為記錄和資料指定不同的儲存類別。

選擇資料控制器 pod 的儲存類別時要考慮的重要因素：

- 您 **必須** 使用遠端共用儲存類別來確保資料的持久性，如此一來，如果 pod 或節點在 pod 重新開機時不會再重新連線，則可以再次連接至永久性磁片區。
- 寫入控制器 SQL 實例、計量 DB 和記錄 DB 的資料通常是相當低的磁片區，而且不會受到延遲的影響，所以超高效能的儲存體並不重要。 如果您的使用者經常使用 Grafana 和 Kibana 介面，且您有大量的資料庫實例，則您的使用者可能會受益于更快速執行的儲存體。
- 所需的儲存體容量是因為每個資料庫實例都會收集記錄和計量，而您所部署的資料庫實例數目變數。 資料會保留在記錄檔和計量 DB 中，以在清除前2周。 
- 變更儲存類別後置部署很困難、未記載且不受支援。 請務必在部署階段正確選擇儲存類別。

> [!NOTE]
> 如果未指定任何儲存類別，則會使用預設的儲存類別。 每個 Kubernetes 叢集只能有一個預設的儲存類別。 您可以 [變更預設儲存類別](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/)。

### <a name="database-instance-storage-configuration"></a>資料庫實例儲存體設定

每個資料庫實例都有資料、記錄和備份永久性磁片區。 您可以在部署時指定這些永久性磁片區的儲存類別。 如果未指定任何儲存類別，則會使用預設的儲存類別。

使用或命令建立實例 `azdata arc sql mi create` 時 `azdata arc postgres server create` ，有兩個參數可以用來設定儲存類別：

> [!NOTE]
> 其中有些參數正在開發中，將會在 `azdata arc sql mi create` `azdata arc postgres server create` 即將推出的版本中提供。

|參數名稱、簡短名稱|用途|
|---|---|
|`--storage-class-data`, `-scd`|用來指定所有資料檔案的儲存類別，包括交易記錄檔|
|`--storage-class-logs`, `-scl`|用來指定所有記錄檔的儲存類別|
|`--storage-class-data-logs`, `-scdl`|用來指定資料庫交易記錄檔的儲存類別。 **注意：尚未提供。**|
|`--storage-class-backups`, `-scb`|用來指定所有備份檔案的儲存類別。 **注意：尚未提供。**|

下表列出 Azure SQL 受控執行個體容器內對應至資料和記錄之永久性磁片區的路徑：

|參數名稱、簡短名稱|Mssql 內的路徑-miaa 容器|描述|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|包含 mssql 安裝和其他系統進程的目錄。 Mssql 目錄包含預設資料 (包括交易記錄) 、錯誤記錄 & 備份目錄|
|`--storage-class-logs`, `-scl`|/var/log|包含儲存主控台輸出 (stderr、stdout) 、容器內進程的其他記錄資訊的目錄|

下表列出于 postgresql 實例容器內對應至資料和記錄之永久磁片區的路徑：

|參數名稱、簡短名稱|Postgres 容器內的路徑|描述|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|包含 postgres 安裝的資料和記錄檔目錄|
|`--storage-class-logs`, `-scl`|/var/log|包含儲存主控台輸出 (stderr、stdout) 、容器內進程的其他記錄資訊的目錄|

針對資料檔案、記錄和備份，每個資料庫實例都有個別的永久卷。 這表示每種類型的檔案都有各自的 i/o，這些類型的每個檔案都受限於磁片區布建程式布建儲存體的方式。 每個資料庫實例都有自己的持續性磁片區宣告和持續性磁片區。

如果指定的資料庫實例上有多個資料庫，所有資料庫都會使用相同的永久磁片區宣告、永久性磁片區和儲存類別。 所有備份-差異記錄備份和完整備份都將使用相同的永久磁片區宣告和持續性磁片區。 資料庫實例 pod 的持續性磁片區宣告如下所示：

|**執行個體**|**持續性磁片區宣告**|
|---|---|
|**Azure SQL 受控執行個體**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**適用于于 postgresql 的 Azure 資料庫實例**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure 于 postgresql 超大規模**|`<namespace>/logs-<instance namme>-<ordinal>`， `<namespace>/data-<instance namme>-<ordinal>` * (序數的範圍是從0到 w，其中 W 是背景工作的數目) *|

選擇資料庫實例 pod 的儲存類別時要考慮的重要因素：

- 您可以使用單一 pod 模式或多重 pod 模式來部署資料庫實例。 單一 pod 模式的範例是 Azure SQL 受控實例或一般用途定價層 Azure SQL 受控實例的開發人員實例。 多個 pod 模式的範例是高可用性商務關鍵定價層 Azure SQL 受控實例。  (注意：定價層正在開發中，但尚未提供給客戶。使用單一 pod 模式部署的 ) 資料庫實例 **必須** 使用遠端共用儲存類別，以確保資料的持久性，因此，如果 pod 或節點在 pod 重新開機時無法再重新連線，則可以再次連接至永久性磁片區。 相反地，高可用性的 Azure SQL 受控實例會使用 Always On 可用性群組，以同步或非同步方式將資料從某個實例複寫到另一個實例。 尤其是以同步方式複寫資料的情況下，一律會有多份資料複本，通常是3個複本。 因此，您可以使用本機儲存體或遠端共用儲存類別來儲存資料和記錄檔。 在使用本機儲存體的情況下，即使在 pod、節點或儲存體硬體失敗的情況下，仍會保留資料。 有了這種彈性，您可以選擇使用本機儲存體，以獲得更好的效能。
- 資料庫效能主要是指指定儲存裝置的 i/o 輸送量的一項功能。 如果您的資料庫是大量讀取或大量寫入，則您應該選擇一個儲存類別，其下的硬體是針對該類型的工作負載所設計。 例如，如果您的資料庫大多用於寫入，您可能會選擇具有 RAID 0 的本機儲存體。 如果您的資料庫大多用來讀取少量的「熱資料」，但有大量的非經常性存取資料，則您可以選擇能夠分層式儲存的 SAN 裝置。 選擇正確的儲存類別與選擇您要用於任何資料庫的儲存類型並不一樣。
- 如果您使用本機儲存體磁片區布建程式，您應該確保針對資料、記錄和備份所布建的本機磁片區會在不同的基礎存放裝置上登陸，以避免發生磁片 i/o 的爭用情形。 OS 也應該位於掛接到個別磁片的磁片區， (s) 。 這基本上與在實體硬體上的資料庫實例遵循相同的指導方針。
- 由於指定實例上的所有資料庫都會共用持續性磁片區宣告和持續性磁片區，因此請務必不要在相同的資料庫實例上共置忙碌的資料庫實例。 可能的話，請將忙碌的資料庫分開到它們自己的資料庫實例，以避免 i/o 爭用。 此外，使用以節點標籤為目標，將資料庫實例放在不同的節點上，以便將整體 i/o 流量分散到多個節點。 如果您使用虛擬化，請務必考慮將 i/o 流量分散至節點層級，而不只是在指定實體主機上的所有節點 Vm 所發生的結合 i/o 活動。

## <a name="estimating-storage-requirements"></a>估計儲存體需求
包含具狀態資料的每個 pod 會在此版本中使用兩個持續性磁片區：一個用於資料的持續性磁片區，另一個則用於記錄 下表列出單一資料控制器、Azure SQL 受控實例、適用於 PostgreSQL 的 Azure 資料庫實例和 Azure 于 postgresql 超大規模實例所需的永久性磁片區數目：

|資源類型|具狀態的 pod 數目|必要的永久性磁片區數目|
|---|---|---|
|資料控制器|4 (`control` 、 `controldb` 、 `logsdb` 、 `metricsdb`) |4 * 2 = 8|
|Azure SQL 受控執行個體|1|2|
|適用於 PostgreSQL 的 Azure 資料庫實例|1| 2|
|Azure 于 postgresql 超大規模|1 + W (W = 背景工作數目) |2 * (1 + W) |

下表顯示範例部署所需的永久性磁片區總數：

|資源類型|執行個體數目|必要的永久性磁片區數目|
|---|---|---|
|資料控制器|1|4 * 2 = 8|
|Azure SQL 受控執行個體|5|5 * 2 = 10|
|適用於 PostgreSQL 的 Azure 資料庫實例|5| 5 * 2 = 10|
|Azure 于 postgresql 超大規模|2 (每個實例的背景工作數目 = 4) |2 * 2 * (1 + 4) = 20|
|***持續性磁片區總數***||8 + 10 + 10 + 20 = 48|

此計算可用來根據儲存體布建程式或環境來規劃 Kubernetes 叢集的儲存體。 例如，如果將本機儲存體布建程式用於具有5個節點的 Kubernetes 叢集，則在每個節點上方的範例部署至少需要10個持續性磁片區的儲存體。 同樣地，在布建 Azure Kubernetes Service (AKS) 叢集，其中有5個節點為節點集區挑選適當的 VM 大小，因此可以連接10個數據磁片是很重要的。 如需如何針對 AKS 節點的儲存體需求調整節點大小的詳細資訊，請參閱 [這裡](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs)。

## <a name="choosing-the-right-storage-class"></a>選擇正確的儲存類別

### <a name="on-premises-and-edge-sites"></a>內部部署和邊緣網站

Microsoft 及其 OEM、OS 和 Kubernetes 合作夥伴正在處理 Azure Arc 資料服務的認證方案。 此方案將提供客戶來自認證測試控管組的類似測試結果。 測試將會評估功能相容性、壓力測試結果，以及效能和擴充性。 上述每個測試結果都會指出使用的作業系統、使用的 Kubernetes 散發、使用的使用的 CSI 附加元件，以及使用的儲存類別。 這可協助客戶針對其需求選擇最佳的儲存類別、OS、Kubernetes 散發和硬體。 有關此程式和初始測試結果的詳細資訊，將會更接近 Azure Arc data services 的正式運作。

#### <a name="public-cloud-managed-kubernetes-services"></a>公用雲端，受控 Kubernetes 服務

針對公用雲端式受控 Kubernetes 服務，我們可以提出下列建議：

|公用雲端服務|建議|
|---|---|
|**Azure Kubernetes Service (AKS)**|Azure Kubernetes Service (AKS) 有兩種類型的儲存體 Azure 檔案儲存體和 Azure 磁片。 每種類型的儲存體都有兩個定價/效能層級-標準 (HDD) 和 premium (SSD) 。 因此，AKS 中提供的四個儲存體類別 `azurefile` (Azure 檔案儲存體標準層) 、 `azurefile-premium` (Azure 檔案儲存體進階層) 、 `default` (azure 磁片標準層) ，以及 (`managed-premium` azure 磁片進階層) 。 預設的儲存類別是 `default`)  (Azure 磁片標準層。 類型和階層之間有顯著的 **[定價差異](https://azure.microsoft.com/en-us/pricing/details/storage/)** ，這些類型和階層應納入您的決定。 針對具有高效能需求的生產工作負載，建議您 `managed-premium` 針對所有儲存類別使用。 針對開發/測試工作負載、概念證明等，其中成本是考慮因素，則 `azurefile` 是最便宜的選項。 所有的四個選項都可用於需要遠端共用儲存體的情況，因為它們都是 Azure 中所有網路連接的儲存裝置。 深入瞭解 [AKS 儲存體](../../aks/concepts-storage.md)。|
|**AWS Elastic Kubernetes Service (EKS)**| Amazon 的彈性 Kubernetes 服務有一個主要儲存類別，以 [EBS CSI 儲存驅動程式](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html)為基礎。 建議用於生產工作負載。 有新的儲存體驅動程式- [EFS CSI 儲存體驅動程式](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) -可以新增至 EKS 叢集，但它目前處於 Beta 階段，可能會變更。 雖然 AWS 指出此儲存體驅動程式支援生產環境，但我們不建議您使用它，因為它仍處於 Beta 階段，可能會變更。 EBS 儲存類別是預設值，而且會呼叫 `gp2` 。 深入瞭解 [EKS 儲存體](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html)。|
|**Google Kubernetes Engine (GKE)**|Google Kubernetes Engine (GKE) 只會有一個稱為的儲存類別， `standard` 用來 [GCE 持續性磁片](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk)。 這是唯一的，也是預設值。 雖然有適用于 GKE 的 [本機靜態磁片區布建程式](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) 可讓您與直接連結 ssd 搭配使用，但我們不建議您使用它，因為它不是由 Google 維護或支援。 深入瞭解 [GKE 儲存體](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes)。
