---
title: 雲簡單維護和更新
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述計畫維護和更新的雲簡單服務流程
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025022"
---
# <a name="cloudsimple-maintenance-and-updates"></a>雲簡單維護和更新

私有雲環境設計為沒有單點故障。

* ESXi 群集配置了 vSphere 高可用性 （HA）。 群集的大小為至少一個備用節點以進行恢復。
* 冗余主存儲由 vSAN 提供，它至少需要三個節點來防止單個故障。 vSAN 可以配置為為較大的群集提供更高的恢復能力。
* vCenter、PSC 和 NSX 管理器 VM 配置了 RAID-10 存儲，以防止存儲故障。 VM 通過 vSphere HA 防止節點/網路故障。
* ESXi 主機具有冗餘風扇和 NIC。
* TOR 和脊柱開關配置在 HA 對中，以提供恢復能力。

CloudSimple 持續監控以下 VM 的停機時間和可用性，並提供可用性 SL：

* ESXi 主機
* vCenter
* Psc
* NSX 管理器

CloudSimple 還會持續監控以下故障：

* 硬碟
* 物理 NIC 埠
* 伺服器
* 球迷
* Power
* 交換器
* 交換器埠

如果磁片或節點發生故障，新節點將自動添加到受影響的 VMware 群集中，使其立即恢復健康。

雲簡單備份、維護和更新私有雲中的這些 VMware 元素：

* ESXi
* vCenter平臺服務
* 控制器
* vSAN
* NSX

## <a name="back-up-and-restore"></a>備份及還原

雲簡單備份包括：

* vCenter、PSC 和 DVS 規則的夜間增量備份。
* vCenter 本機 API 以在應用程式層備份元件。
* 在 VMware 管理軟體更新或升級之前進行自動備份。
* 在通過 TLS1.2 加密通道傳輸到 Azure 之前，在源處進行 vCenter 資料加密。 資料存儲在 Azure Blob 中，在其中跨區域複製資料。

您可以通過打開[支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)請求來請求還原。

## <a name="maintenance"></a>維護

CloudSimple 執行多種類型的計畫維護。

### <a name="backendinternal-maintenance"></a>後端/內部維護

這種維護通常涉及重新配置物理資產或安裝軟體修補程式。 它不會影響所服務資產的正常使用。 隨著冗余 NIC 進入每個物理機架，正常的網路流量和私有雲操作不會受到影響。 僅當您的組織希望在維護期間使用完全冗余頻寬時，您才會注意到性能影響。

### <a name="cloudsimple-portal-maintenance"></a>雲簡單門戶維護

更新 CloudSimple 控制平面或基礎結構時，需要一些有限的服務停機時間。 目前，維護間隔的頻率可能為每月一次。 預計頻率會隨著時間的推移而下降。 CloudSimple 為門戶維護提供了通知，並盡可能縮短間隔時間。 在門戶維護期間，以下服務將繼續運行，沒有任何影響：

* VMware 管理平面和應用
* vCenter 訪問
* 所有網路和存儲
* 所有 Azure 流量

### <a name="vmware-infrastructure-maintenance"></a>VMware 基礎設施維護

有時，有必要更改 VMware 基礎結構的配置。  目前，這些間隔可以每 1-2 個月發生一次，但頻率預計會隨著時間的推移而降低。 這種類型的維護通常可以在不中斷雲簡單服務的正常使用的情況下進行。 在 VMware 維護期間，以下服務將繼續運行，沒有任何影響：

* VMware 管理平面和應用
* vCenter 訪問
* 所有網路和存儲
* 所有 Azure 流量

## <a name="updates-and-upgrades"></a>更新和升級

CloudSimple 負責私有雲中 VMware 軟體（ESXi、vCenter、PSC 和 NSX）的生命週期管理。

軟體更新包括：

* **補丁**。 VMware 發佈的安全修補程式或錯誤修復。
* **更新**. VMware 堆疊元件的次要版本更改。
* **升級**。 VMware 堆疊元件的主要版本更改。

CloudSimple 在 VMware 提供關鍵安全修補程式後，立即測試它。 根據 SLA，CloudSimple 將在一周內將安全修補程式推出到私有雲環境。

CloudSimple 提供 VMware 軟體元件的季度維護更新。 當新的主要版本的 VMware 軟體可用時，CloudSimple 與客戶合作，協調一個合適的維護視窗進行升級。

## <a name="next-steps"></a>後續步驟

[使用 Veeam 備份工作負載 VM](backup-workloads-veeam.md)
