---
title: Azure VMware 解決方案（按雲簡單 - 私有雲）
description: 瞭解雲簡單私有雲和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4fb930603455ed1a5df5d357fcab669f41a0c28c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024939"
---
# <a name="cloudsimple-private-cloud-overview"></a>雲簡單私有雲概述

雲簡單在幾分鐘內將 VMware 工作負載轉換為公共雲。 使用 CloudSimple 服務，可以在 Azure 裸機基礎結構上本機部署 VMware。 部署位於 Azure 位置，並完全集成 Azure 雲的其餘部分。

雲簡單解決方案提供完整的 VMware 操作連續性。 此解決方案為您提供了以下公共雲優勢：

* 彈性
* 創新
* 效率

借助 CloudSimple，您可以受益于降低擁有權總成本的雲消費模式。 它還提供按需預配、即用即付和容量優化。

CloudSimple 完全相容：

* 現有工具
* 技術
* 處理序

此相容性使您的團隊能夠管理 Azure 雲上的工作負載，而不會中斷這些類型的策略：

* 網路
* 安全性  
* 資料保護  
* 稽核

CloudSimple 管理基礎架構以及所有必要的網路和管理服務。 雲簡單服務使您的團隊能夠專注于：

* 業務價值
* 應用程式佈建
* 業務持續性
* 支援
* 強制執行原則

## <a name="private-cloud-environment-overview"></a>私有雲環境概述

私有雲是一個孤立的 VMware 堆疊，支援：

* ESXi 主機
* vCenter
* vSAN
* NSX

私有雲通過雲簡單門戶進行管理。 它們有自己的 vCenter 伺服器。

堆疊在以下上運行：

* 專用節點
* 隔離裸機硬體節點

使用者通過本機 VMware 工具使用堆疊，包括：

* vCenter
* NSX 管理器

可以在 Azure 位置部署專用節點。 然後，您可以使用 Azure 和雲簡單管理它們。 私有雲由一個或多個 vSphere 群集組成，每個群集包含 3 到 16 個節點。

您可以使用已購買、即用即付的節點或保留的專用節點創建私有雲。

您可以使用以下連接將私有雲連接到本地環境和 Azure 網路：

* 安全
* 專用 VPN
* Azure ExpressRoute

私有雲環境旨在消除單點故障：

* ESXi 群集配置具有 vSphere 高可用性，大小可至少具有一個備用節點以進行恢復。
* vSAN 提供冗余主存儲。 vSan 至少需要三個節點來防止單個故障。 您可以配置 vSAN 以為較大的群集提供更高的恢復能力。
* 您可以使用 RAID-10 存儲策略配置 vCenter、PSC 和 NSX 管理器 VM，以防止存儲故障。 vSphere HA 可防止節點和網路故障。

## <a name="scenarios-for-deploying-a-private-cloud"></a>部署私有雲的方案

下面是私有雲部署的一些示例用例。

### <a name="data-center-retirement-or-migration"></a>資料中心停用或遷移

* 達到現有資料中心或刷新硬體的限制時，獲取其他容量。
* 在雲中添加所需的容量，並消除管理硬體更新的麻煩。
* 與耗時的轉換或重新架構相比，降低雲遷移的風險和成本。
* 使用熟悉的 VMware 工具和技能加快雲遷移。 在雲中，使用 Azure 服務以您的速度實現應用程式的現代化。

### <a name="expand-on-demand"></a>按需擴展

* 擴展到雲以滿足意外需求，例如新的開發環境或季節性容量突發。
* 按需創建新容量，並僅在需要時保留容量。
* 通過本地和雲中的相同體系結構和策略，減少前期投資，加快預配速度，並降低複雜性。

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Azure 雲中的災害復原和虛擬桌面

* 在 Azure 雲中建立對資料、應用和桌面的遠端存取。 使用高頻寬連接，您可以快速上傳/下載資料以從事件中恢復。 低延遲網路為您提供使用者期望從桌面應用快速回應時間。

* 使用 CloudSimple 門戶和熟悉的 VMware 工具在雲中複製所有策略和網路。 複製減少了創建和管理 DR 和 VDI 實現的工作和風險。

### <a name="high-performance-applications-and-databases"></a>高性能應用程式和資料庫

* 使用 CloudSimple 提供的超融合架構運行您最苛刻的工作負載。
* 運行 Oracle、微軟 SQL 伺服器、中介軟體系統和高性能無 SQL 資料庫。
* 通過高速 25 Gbps 網路連接，將雲體驗為您自己的資料中心。 通過高速連接，您可以運行跨本地、Azure 上的 VMware 和 Azure 專用工作負荷的混合應用，而不會降低性能。

### <a name="true-hybrid"></a>真正的混合

* 跨 VMware 和 Azure 服務統一 DevOps。
* 針對可應用於所有工作負荷的 Azure 服務和解決方案優化 VMware 管理。
* 訪問公共雲服務，而無需擴展資料中心或重新構建應用程式。
* 集中 Azure 上 VMware 應用程式的標識、存取控制策略、日誌記錄和監視。

## <a name="limits"></a>限制

下表列出了私有雲資源的節點限制。

| 資源 | 限制 |
|----------|-------|
| 創建私有雲的最小節點數 | 3 |
| 私有雲群集中的最大節點數 | 16 |
| 私有雲中的最大節點數 | 64 |
| 新群集上的節點最小數量 | 3 |

## <a name="next-steps"></a>後續步驟

* 瞭解如何[創建私有雲](create-private-cloud.md)
* 瞭解如何[配置私有雲環境](quickstart-create-private-cloud.md)
