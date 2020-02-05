---
title: Azure VMware 解決方案（AVS）-AVS 私人雲端
description: 瞭解 AVS 私用雲端和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2688edf281a6d8bc3d61e8e294c920f115f0f3f6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024939"
---
# <a name="avs-private-cloud-overview"></a>AVS 私用雲端總覽

在短短幾分鐘內，AVS 會將 VMware 工作負載轉換並擴充至公用雲端。 使用 AVS 服務，您可以在 Azure 裸機基礎結構上以原生方式部署 VMware。 您的部署位於 Azure 位置，並與 Azure 雲端的其他部分完全整合。

AVS 解決方案提供完整的 VMware 操作持續性。 此解決方案提供您公用雲端的優點：

* 彈性
* 創新
* 效率

有了 AVS，您就能從雲端耗用量模型中獲益，以降低擁有權總成本。 它也提供隨選布建、隨用隨付和容量優化。

AVS 與下列內容完全相容：

* 現有的工具
* 技術
* 程序

此相容性可讓您的小組管理 Azure 雲端上的工作負載，而不會中斷這些類型的原則：

* 網路
* 安全性  
* 資料保護  
* 稽核

AVS 會管理基礎結構和所有必要的網路和管理服務。 AVS 服務可讓您的小組專注于：

* 商業價值
* 應用程式布建
* 業務續航力
* 支援
* 強制執行原則

## <a name="avs-private-cloud-environment-overview"></a>AVS 私用雲端環境總覽

AVS 私用雲端是一種隔離的 VMware 堆疊，它支援：

* ESXi 主機
* vCenter
* vSAN
* NSX

AVS 私人雲端是透過 AVS 入口網站來管理。 他們在自己的管理網域中有自己的 vCenter server。

堆疊會在上執行：

* 專用節點
* 獨立的裸機硬體節點

使用者透過原生 VMware 工具使用堆疊，包括：

* vCenter
* NSX 管理員

您可以在 Azure 位置中部署專用節點。 然後您就可以使用 Azure 和 AVS 來管理它們。 AVS 私用雲端是由一或多個 vSphere 叢集所組成，而每個叢集包含3到16個節點。

您可以使用已購買的隨用隨付節點或保留的專用節點來建立 AVS 私人雲端。

您可以使用下列連線，將 AVS 私人雲端連線到您的內部部署環境和 Azure 網路：

* 安全
* 私人 VPN
* Azure ExpressRoute

AVS 私用雲端環境的設計目的是消除單一失敗點：

* ESXi 叢集的設定具有 vSphere 高可用性，並已調整大小為至少有一個備用節點可供復原。
* vSAN 提供重複的主要儲存體。 vSan 需要至少三個節點，才能針對單一失敗提供保護。 您可以設定 vSAN 來為較大的叢集提供更高的復原能力。
* 您可以使用 RAID 10 儲存原則來設定 vCenter、PSC 和 NSX Manager Vm，以防止儲存失敗。 vSphere HA 可防止節點和網路失敗。

## <a name="scenarios-for-deploying-an-avs-private-cloud"></a>部署 AVS 私用雲端的案例

以下是一些適用于 AVS 私用雲端部署的範例使用案例。

### <a name="data-center-retirement-or-migration"></a>資料中心淘汰或遷移

* 當您達到現有資料中心的限制或重新整理硬體時，取得額外的容量。
* 在雲端中新增所需的容量，並消除管理硬體重新整理的麻煩。
* 相較于耗時的轉換或改造，降低雲端遷移的風險和成本。
* 使用熟悉的 VMware 工具和技能來加速雲端遷移。 在雲端中，您可以使用 Azure 服務，依您的步調將應用程式現代化。

### <a name="expand-on-demand"></a>隨選擴充

* 擴充至雲端以符合非預期的需求，例如新的開發環境或季節性的容量高載。
* 依需求建立新的容量，並只在需要時才保留。
* 透過內部部署和雲端的相同架構和原則，降低您的前期投資、加速布建速度，以及降低複雜性。

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Azure 雲端中的嚴重損壞修復和虛擬桌面

* 建立對 Azure 雲端中的資料、應用程式和桌上型電腦的遠端存取。 透過高頻寬連線，您可以快速上傳/下載資料以從事件復原。 低延遲的網路可讓您快速地回應使用者從桌面應用程式所預期的時間。

* 使用 AVS 入口網站和熟悉的 VMware 工具，在雲端中複寫您所有的原則和網路。 複寫可減少建立和管理 DR 和 VDI 的工作和風險。

### <a name="high-performance-applications-and-databases"></a>高效能應用程式和資料庫

* 使用 AVS 所提供的超大範圍架構，執行您最嚴苛的工作負載。
* 執行 Oracle、Microsoft SQL server、中介軟體系統和高效能的非 SQL 資料庫。
* 使用高速 25 Gbps 網路連線體驗雲端作為您自己的資料中心。 高速連線可讓您執行跨內部部署、Azure 上的 VMware 和 Azure 私人工作負載的混合式應用程式，而不會影響效能。

### <a name="true-hybrid"></a>真正的混合式

* 跨 VMware 與 Azure 服務整合 DevOps。
* 將適用于您所有工作負載的 Azure 服務和解決方案的 VMware 系統管理優化。
* 存取公用雲端服務，而不需要擴充您的資料中心或重新架構您的應用程式。
* 集中化身分識別、存取控制原則、記錄和監視 Azure 上的 VMware 應用程式。

## <a name="limits"></a>限制

下表列出 AVS 私人雲端資源的節點限制。

| 資源 | 限制 |
|----------|-------|
| 建立 AVS 私人雲端所需的最小節點數目 | 3 |
| 在 AVS 私人雲端的叢集中的節點數目上限 | 16 |
| AVS 私人雲端中的節點數目上限 | 64 |
| 新叢集上節點的最小數目 | 3 |

## <a name="next-steps"></a>後續步驟

* 瞭解如何[建立 AVS 私用雲端](create-private-cloud.md)
* 瞭解如何[設定 AVS 私用雲端環境](quickstart-create-private-cloud.md)
