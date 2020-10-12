---
title: Azure VMware Solution by CloudSimple-私用雲端
description: 深入瞭解 CloudSimple 的概念和優點，包括完整的 VMware 營運持續性、與現有工具、技能和流程的相容性。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e2096e8c0ebfb233c7449510bf0bc8e80b56231e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140711"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple 私用雲端總覽

在幾分鐘內 CloudSimple 將 VMware 工作負載轉換和擴充至公用雲端。 您可以使用 CloudSimple 服務，以原生方式在 Azure 裸機基礎結構上部署 VMware。 您的部署會存在於 Azure 位置，並與 Azure 雲端的其餘部分完全整合。

CloudSimple 解決方案提供完整的 VMware 營運持續性。 此解決方案提供您公用雲端的優點：

* 彈性
* 創新
* 效率

透過 CloudSimple，您可以從雲端耗用量模型獲益，以降低擁有權總成本。 它也提供隨選布建、隨用隨付和容量優化。

CloudSimple 完全相容于：

* 現有的工具
* 技術
* 處理程序

此相容性可讓您的小組管理 Azure 雲端上的工作負載，而不會中斷這些類型的原則：

* 網路
* 安全性  
* 資料保護  
* 稽核

CloudSimple 會管理基礎結構和所有必要的網路和管理服務。 CloudSimple 服務可讓您的小組專注于：

* 商業價值
* 應用程式佈建
* 業務持續性
* 支援
* 強制執行原則

## <a name="private-cloud-environment-overview"></a>私用雲端環境總覽

私用雲端是一種隔離的 VMware 堆疊，支援：

* ESXi 主機
* vCenter
* vSAN
* NSX

私人雲端是透過 CloudSimple 入口網站進行管理。 他們在自己的管理網域中有自己的 vCenter server。

堆疊執行于：

* 專用節點
* 隔離的裸機硬體節點

使用者透過原生 VMware 工具取用堆疊，包括：

* vCenter
* NSX 管理員

您可以在 Azure 位置部署專用節點。 然後您就可以使用 Azure 和 CloudSimple 來管理它們。 私人雲端包含一或多個 vSphere 叢集，而每個叢集都包含3到16個節點。

您可以使用購買的隨用隨付節點或保留的專用節點來建立私人雲端。

您可以使用下列連線將私人雲端連接到內部部署環境和 Azure 網路：

* 安全
* 私人 VPN
* Azure ExpressRoute

私用雲端環境的設計目的是要消除單一失敗點：

* ESXi 叢集會設定為具有 vSphere 高可用性，且大小設定為至少有一個備用節點可供復原。
* vSAN 提供冗余的主要儲存體。 vSan 需要至少三個節點來提供保護，以防止單一失敗。 您可以設定 vSAN 以針對較大的叢集提供更高的復原能力。
* 您可以使用 RAID 10 儲存體原則來設定 vCenter、PSC 和 NSX Manager Vm，以防止儲存體失敗。 vSphere HA 可防止節點和網路失敗。

## <a name="scenarios-for-deploying-a-private-cloud"></a>部署私用雲端的案例

以下是私用雲端部署的一些使用案例範例。

### <a name="data-center-retirement-or-migration"></a>資料中心淘汰或遷移

* 當您達到現有資料中心或重新整理硬體的限制時，可以取得額外容量。
* 在雲端中新增所需的容量，並消除管理硬體重新整理的麻煩。
* 相較于耗時的轉換或重新架構，降低雲端遷移的風險和成本。
* 使用熟悉的 VMware 工具和技能來加速雲端遷移。 在雲端中，您可以使用 Azure 服務，依照您的步調將應用程式現代化。

### <a name="expand-on-demand"></a>依需求擴充

* 擴充至雲端以符合非預期的需求，例如新的開發環境或季節性容量高載。
* 依需求建立新的容量，並只在需要時才保留。
* 在內部部署和雲端中，透過相同的架構和原則，降低您的預付率、加快布建速度，以及降低複雜度。

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Azure 雲端中的嚴重損壞修復和虛擬桌面

* 在 Azure 雲端中建立對資料、應用程式和桌面的遠端存取。 透過高頻寬連接，您可以快速上傳/下載資料以從事件中復原。 低延遲網路可提供使用者從桌面應用程式預期的快速回應時間。

* 使用 CloudSimple 入口網站和熟悉的 VMware 工具，在雲端中複寫您的所有原則和網路。 複寫可減少建立和管理 DR 和 VDI 執行的工作和風險。

### <a name="high-performance-applications-and-databases"></a>高效能應用程式和資料庫

* 使用 CloudSimple 所提供的超融合式架構，執行需求最高的工作負載。
* 執行 Oracle、Microsoft SQL server、中介軟體系統，以及高效能的非 SQL 資料庫。
* 使用高速的 25 Gbps 網路連線體驗雲端作為您自己的資料中心。 高速連線可讓您在內部部署、VMware on Azure 和 Azure 私用工作負載執行混合式應用程式，而不會影響效能。

### <a name="true-hybrid"></a>真正的混合式

* 整合 VMware 與 Azure 服務之間的 DevOps。
* 將適用于您所有工作負載的 Azure 服務和解決方案的 VMware 管理優化。
* 無須擴充您的資料中心或重新架構您的應用程式，即可存取公用雲端服務。
* 集中化身分識別、存取控制原則、記錄和監視 Azure 上的 VMware 應用程式。

## <a name="limits"></a>限制

下表列出私人雲端資源的節點限制。

| 資源 | 限制 |
|----------|-------|
| 建立私人雲端的節點數目下限 | 3 |
| 私人雲端上叢集中的節點數目上限 | 16 |
| 私人雲端中的節點數目上限 | 64 |
| 新叢集中的節點數目下限 | 3 |

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [建立私用雲端](create-private-cloud.md)
* 瞭解如何 [設定私人雲端環境](quickstart-create-private-cloud.md)
