---
title: CloudSimple 維護和更新
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述排程維護和更新的 CloudSimple 服務程式
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025022"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple 維護和更新

私用雲端環境的設計是不會有單一失敗點。

* ESXi 叢集是以 vSphere 高可用性 (HA) 來設定。 叢集會調整大小，以提供至少一個備用節點來復原。
* 多餘的主要儲存體是由 vSAN 提供，需要至少三個節點來提供保護以防止單一失敗。 您可以設定 vSAN 以提供更高的彈性給較大的叢集。
* vCenter、PSC 和 NSX Manager Vm 都設定了具有 RAID 10 的儲存體，以防止儲存失敗。 藉由 vSphere HA，可保護 Vm 免于節點/網路失敗。
* ESXi 主機具有多餘的風扇和 Nic。
* TOR 和側面交換器設定為 HA 配對以提供復原功能。

CloudSimple 會持續監視下列 Vm 的執行時間和可用性，並提供可用性 Sla：

* ESXi 主機
* vCenter
* Psc
* NSX 管理員

CloudSimple 也會持續監視下列錯誤：

* 硬碟
* 實體 NIC 埠
* 伺服器
* 球迷
* 電源
* 交換器
* 交換器埠

如果磁片或節點失敗，會自動將新的節點新增至受影響的 VMware 叢集，使其立即恢復健康狀態。

CloudSimple 會在私人雲端中備份、維護及更新這些 VMware 元素：

* ESXi
* vCenter 平臺服務
* 控制器
* vSAN
* NSX

## <a name="back-up-and-restore"></a>備份及還原

CloudSimple 備份包括：

* VCenter、PSC 和 DVS 規則的夜間增量備份。
* 要在應用層級備份元件的 vCenter 原生 Api。
* 更新或升級 VMware 管理軟體之前的自動備份。
* 在透過 TLS 1.2 加密通道傳送資料到 Azure 之前，來源的 vCenter 資料加密。 資料會儲存在跨區域複寫的 Azure blob 中。

您可以藉由開啟 [支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來要求還原。

## <a name="maintenance"></a>維護

CloudSimple 會進行數種規劃的維護。

### <a name="backendinternal-maintenance"></a>後端/內部維護

這種維護通常牽涉到重新設定實體資產或安裝軟體修補程式。 它不會影響所服務之資產的一般耗用量。 當有多餘的 Nic 進入每個實體機架時，一般的網路流量和私用雲端作業不會受到影響。 您可能會注意到，只有在您的組織預期在維護間隔期間使用完整的冗余頻寬時，才會影響效能。

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple 入口網站維護

更新 CloudSimple 控制平面或基礎結構時，需要某些有限的服務停機時間。 目前，每個月會有一次維護間隔的頻率。 頻率預期會隨著時間而拒絕。 CloudSimple 會提供入口網站維護的通知，並盡可能縮短間隔。 在入口網站維護間隔期間，下列服務會繼續運作而不會有任何影響：

* VMware 管理平面和應用程式
* vCenter 存取
* 所有網路和存放裝置
* 所有 Azure 流量

### <a name="vmware-infrastructure-maintenance"></a>VMware 基礎結構維護

有時候必須變更 VMware 基礎結構的設定。  目前，這些間隔每1-2 個月會發生一次，但頻率預期會隨著時間而拒絕。 這種類型的維護通常可以在不中斷 CloudSimple 服務的一般耗用量的情況下完成。 在 VMware 維護間隔期間，下列服務會繼續運作，而不會產生任何影響：

* VMware 管理平面和應用程式
* vCenter 存取
* 所有網路和存放裝置
* 所有 Azure 流量

## <a name="updates-and-upgrades"></a>更新和升級

CloudSimple 負責管理 VMware 軟體的生命週期管理 (ESXi、vCenter、PSC 和 NSX) 在私用雲端中。

軟體更新包括：

* **修補程式**。 VMware 發行的安全性修補程式或錯誤修正。
* **更新**。 VMware 堆疊元件的次要版本變更。
* **升級**。 VMware 堆疊元件的主要版本變更。

CloudSimple 可從 VMware 取得重大安全性修補程式。 根據 SLA，CloudSimple 會在一周內推出私用雲端環境的安全性修補程式。

CloudSimple 提供 VMware 軟體元件的每季維護更新。 當 VMware 軟體有新的主要版本可供使用時，CloudSimple 會與客戶合作，以協調適當的維護時段以進行升級。

## <a name="next-steps"></a>後續步驟

[使用 Veeam 備份工作負載 Vm](backup-workloads-veeam.md)
