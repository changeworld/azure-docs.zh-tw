---
title: 私用雲端 VMware 元件
titleSuffix: Azure VMware Solution by CloudSimple
description: 說明如何在私人雲端上安裝 VMware 元件
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386871"
---
# <a name="private-cloud-vmware-components"></a>私用雲端 VMware 元件

私人雲端是由管理網域中的 vCenter server 所管理的隔離 VMware 堆疊（ESXi 主機、vCenter、vSAN 和 NSX）環境。  CloudSimple 服務可讓您以原生方式在 azure 中的 Azure 裸機基礎結構上部署 VMware。  私人雲端與 Azure 雲端的其他部分整合。  使用下列 VMware 堆疊元件部署私人雲端：

* **VMware ESXi-** Azure 專用節點上的虛擬程式
* **VMware vCenter-** 用於集中管理私人雲端 vSphere 環境的設備
* **VMware vSAN-** 超融合式基礎結構解決方案
* **VMWARE NSX 資料中心-** 網路虛擬化和安全性軟體  

## <a name="vmware-component-versions"></a>VMware 元件版本

私用雲端 VMware 堆疊是以下列軟體版本進行部署。

| 元件 | 版本 | 授權版本 |
|-----------|---------|------------------|
| ESXi | 6.7 u2 | 企業加 |
| vCenter | 6.7 u2 | vCenter 標準 |
| vSAN | 6.7 | Enterprise |
| NSX 資料中心 | 2.4.1 | 進階 |

## <a name="esxi"></a>ESXi

當您建立私人雲端時，VMware ESXi 會安裝在已布建的 CloudSimple 節點上。  ESXi 提供用於部署工作負載虛擬機器（Vm）的管理程式。  節點提供私人雲端上的超融合式基礎結構（計算和儲存體）。  節點是私人雲端上 vSphere 叢集的一部分。  每個節點都有四個連線到 underlay 網路的實體網路介面。  有兩個實體網路介面可用來在 vCenter 上建立**VSphere 分散式交換器（VDS）** ，而兩者則用來建立以**NSX 管理的虛擬分散式交換器（N-VDS）** 。  網路介面是以主動-主動模式設定，以提供高可用性。

深入瞭解 VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter server 應用裝置

vCenter server 應用裝置（VCSA）提供 CloudSimple 的 VMware 解決方案的驗證、管理和協調流程功能。 當您建立私人雲端時，會部署 VCSA 與內嵌平臺服務控制站（PSC）。  VCSA 部署在部署私人雲端時所建立的 vSphere 叢集上。  每個私人雲端都有自己的 VCSA。  擴充私用雲端會將節點新增至私人雲端上的 VCSA。

### <a name="vcenter-single-sign-on"></a>vCenter 單一登入

VCSA 上的內嵌平臺服務控制器與**VCenter 單一登入網域**相關聯。  功能變數名稱為**cloudsimple。**  系統會為您建立預設的使用者 **CloudOwner@cloudsimple.com** ，以供您存取 vCenter。  您可以新增適用于 vCenter 的內部部署/Azure active directory 身分[識別來源](set-vcenter-identity.md)。

## <a name="vsan-storage"></a>vSAN 儲存體

建立私用雲端時，會使用完整設定的全部 flash vSAN 儲存體（本機到叢集）。  建立包含 vSAN 資料存放區的 vSphere 叢集時，必須至少有三個相同 SKU 的節點。  預設會在 vSAN 資料存放區上啟用重復資料刪除和壓縮。  VSphere 叢集的每個節點上都會建立兩個磁片群組。 每個磁片群組都包含一個快取磁片和三個容量磁片。

預設的 vSAN 儲存原則會建立在 vSphere 叢集上，並套用至 vSAN 資料存放區。  此原則會決定如何在資料存放區中布建和配置 VM 儲存體物件，以確保所需的服務層級。  儲存體原則會定義**可容忍的失敗（FTT）** 和**失敗容錯方法**。  您可以建立新的存放裝置原則，並將其套用至 Vm。 若要維護 SLA，必須在 vSAN 資料存放區上維護25% 的備用容量。  

### <a name="default-vsan-storage-policy"></a>預設 vSAN 儲存原則

下表顯示預設 vSAN 儲存體原則參數。

| VSphere 叢集中的節點數目 | FTT | 失敗容錯方法 |
|------------------------------------|-----|--------------------------|
| 3和4個節點 | 1 | RAID 1 （鏡像）-建立2個複本 |
| 5到16個節點 | 2 | RAID 1 （鏡像）-建立3個複本 |

## <a name="nsx-data-center"></a>NSX 資料中心

NSX 資料中心在您的私人雲端上提供網路虛擬化、微分割和網路安全性功能。  您可以透過 NSX 設定您私人雲端上的 NSX 資料中心所支援的所有服務。  當您建立私人雲端時，會安裝並設定下列的 NSX 元件。

* NSXT 管理員
* 傳輸區域
* 主機和邊緣上行連結設定檔
* 邊緣傳輸、Ext1 和 Ext2 的邏輯交換器
* ESXi 傳輸節點的 IP 集區
* Edge 傳輸節點的 IP 集區
* 邊緣節點
* 適用于控制器和邊緣 Vm 的 DRS 反親和性規則
* 第0層路由器
* 在 Tier0 路由器上啟用 BGP

## <a name="vsphere-cluster"></a>vSphere 叢集

ESXi 主機會設定為叢集，以確保私用雲端的高可用性。  當您建立私人雲端時，vSphere 的管理元件會部署在第一個叢集上。  系統會為管理元件建立資源集區，並將所有管理 Vm 部署在此資源集區中。 無法刪除第一個叢集來壓縮私用雲端。  vSphere 叢集會使用**VSPHERE HA**為 vm 提供高可用性。  可容忍的失敗是根據叢集中可用的節點數目。  您可以使用公式 ```Number of nodes = 2N+1```，其中 ```N``` 是要容許的失敗次數。

### <a name="vsphere-cluster-limits"></a>vSphere 叢集限制

| 資源 | 限制 |
|----------|-------|
| 建立私人雲端的節點數目下限（第一個 vSphere 叢集） | 3 |
| 私人雲端上 vSphere 叢集中的節點數目上限 | 16 |
| 私人雲端中的節點數目上限 | 64 |
| 私人雲端中的 vSphere 叢集數目上限 | 21 |
| 新 vSphere 叢集上的節點數目下限 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 基礎結構維護

有時候，您必須對 VMware 基礎結構的設定進行變更。 目前，這些間隔可能每隔1-2 個月發生一次，但頻率預期會隨著時間而遭到拒絕。 這種類型的維護通常可以執行，而不會中斷 CloudSimple 服務的一般耗用量。 在 VMware 維護間隔期間，下列服務會繼續運作而不會產生任何影響：

* VMware 管理平面和應用程式
* vCenter 存取
* 所有網路功能和存放裝置
* 所有 Azure 流量

## <a name="updates-and-upgrades"></a>更新與升級

CloudSimple 負責在私人雲端中管理 VMware 軟體（ESXi、vCenter、PSC 和 NSX）的生命週期。

軟體更新包括：

* **修補程式**。 VMware 發行的安全性修補程式或錯誤修正。
* **更新**。 VMware 堆疊元件的次要版本變更。
* **升級**。 VMware 堆疊元件的主要版本變更。

CloudSimple 會在從 VMware 提供重大安全性修補程式後立即進行測試。 根據 SLA，CloudSimple 會在一周內匯總私人雲端環境的安全性修補程式。

CloudSimple 提供 VMware 軟體元件的每季維護更新。 當 VMware 軟體有新的主要版本可供使用時，CloudSimple 會與客戶合作，協調適合的維護時段進行升級。  

## <a name="next-steps"></a>後續步驟

* [CloudSimple 維護和更新](cloudsimple-maintenance-updates.md)
