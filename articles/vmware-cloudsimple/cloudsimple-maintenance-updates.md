---
title: Azure VMware 解決方案（AVS）-AVS 維護和更新
description: 說明已排程維護和更新的 AVS 服務程式
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf5937183fc20579ecd21aca8543a0a78d4b9ff3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025022"
---
# <a name="avs-maintenance-and-updates"></a>AVS 維護和更新

AVS 私用雲端環境的設計是不會有單一失敗點。

* ESXi 叢集已設定 vSphere 高可用性（HA）。 叢集的大小會調整為至少有一個備用節點可供復原。
* 多餘的主要儲存體是由 vSAN 提供，這需要至少三個節點才能針對單一失敗提供保護。 vSAN 可以設定為較大的叢集提供更高的復原能力。
* vCenter、PSC 和 NSX Manager Vm 都設定了 RAID 10 的儲存體，以避免儲存失敗。 Vm 會藉由 vSphere HA 而受到保護，以防止節點/網路失敗。
* ESXi 主機具有多餘的風扇和 Nic。
* TOR 和書脊交換器會在 HA 配對中設定，以提供復原功能。

AVS 會持續監視下列 Vm 的執行時間和可用性，並提供可用性 Sla：

* ESXi 主機
* vCenter
* PSC
* NSX 管理員

AVS 也會針對失敗持續監視下列各項：

* 硬碟
* 實體 NIC 埠
* 伺服器
* 扇形
* Power
* 交換器
* 交換器埠

如果磁片或節點失敗，新的節點會自動新增至受影響的 VMware 叢集，以立即恢復健全狀況。

AVS 會在 AVS 私人雲端中備份、維護和更新這些 VMware 元素：

* ESXi
* vCenter 平臺服務
* 控制器
* vSAN
* NSX

## <a name="back-up-and-restore"></a>備份和還原

AVS 備份包含：

* VCenter、PSC 和 DVS 規則的夜間增量備份。
* 用來在應用層備份元件的 vCenter 原生 Api。
* 更新或升級 VMware 管理軟體之前的自動備份。
* 在資料透過 TLS 1.2 加密通道傳送至 Azure 之前，來源的 vCenter 資料加密。 資料會儲存在 Azure blob 中，其會跨區域複寫。

您可以藉由開啟[支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來要求還原。

## <a name="maintenance"></a>維護

AVS 會進行幾種預定的維護。

### <a name="backendinternal-maintenance"></a>後端/內部維護

這種維護通常牽涉到重新設定實體資產或安裝軟體修補程式。 它不會影響所服務資產的一般耗用量。 使用冗余的 Nic 進入每個實體機架時，一般網路流量和 AVS 私人雲端作業不會受到影響。 您可能會注意到，只有在您的組織預期在維護間隔期間使用完整多餘的頻寬時，才會影響效能。

### <a name="avs-portal-maintenance"></a>AVS 入口網站維護

當您更新 AVS 控制平面或基礎結構時，需要一些有限的服務停機時間。 目前，維護間隔的頻率可以是每個月一次。 頻率預期會在一段時間後遭到拒絕。 AVS 提供入口網站維護的通知，並盡可能縮短間隔。 在入口網站維護間隔期間，下列服務會繼續運作而不會產生任何影響：

* VMware 管理平面和應用程式
* vCenter 存取
* 所有網路功能和存放裝置
* 所有 Azure 流量

### <a name="vmware-infrastructure-maintenance"></a>VMware 基礎結構維護

有時候，您必須對 VMware 基礎結構的設定進行變更。 目前，這些間隔可能每隔1-2 個月發生一次，但頻率預期會隨著時間而遭到拒絕。 這種類型的維護通常可以執行，而不會中斷使用 AVS 服務的一般耗用量。 在 VMware 維護間隔期間，下列服務會繼續運作而不會產生任何影響：

* VMware 管理平面和應用程式
* vCenter 存取
* 所有網路功能和存放裝置
* 所有 Azure 流量

## <a name="updates-and-upgrades"></a>更新與升級

AVS 負責在 AVS 私用雲端中管理 VMware 軟體（ESXi、vCenter、PSC 和 NSX）的生命週期。

軟體更新包括：

* **修補程式**。 VMware 發行的安全性修補程式或錯誤修正。
* **更新**。 VMware 堆疊元件的次要版本變更。
* **升級**。 VMware 堆疊元件的主要版本變更。

當您從 VMware 推出重大安全性修補程式時，AVS 就會立即進行測試。 根據 SLA，AVS 會在一周內推出「AVS 私用雲端環境」的安全性修補程式。

AVS 提供 VMware 軟體元件的每季維護更新。 當 VMware 軟體有新的主要版本可用時，AVS 會與客戶合作，協調適合的維護時段來進行升級。

## <a name="next-steps"></a>後續步驟

[使用 Veeam 備份工作負載 Vm](backup-workloads-veeam.md)
