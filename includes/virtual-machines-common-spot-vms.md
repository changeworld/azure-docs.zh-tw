---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7cfa6e9810057493cc3007eec7fd1668a70c727e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77179021"
---
使用「點 Vm」可讓您以可觀的成本節約，利用我們未使用的容量。 Azure 基礎結構會在任何時間點回復，以找出虛擬機器的功能。 因此，針對可處理中斷的工作負載（例如批次處理作業、開發/測試環境、大型計算工作負載等），找出 Vm 很棒。

可用容量的數量可能會因大小、區域、時間和更多而有所不同。 部署點 Vm 時，如果有可用的容量，Azure 將會配置 Vm，但這些 Vm 沒有 SLA。 點 VM 不提供高可用性保證。 Azure 基礎結構會在任何時間點收回 Vm，並提供30秒的通知。 

> [!IMPORTANT]
> 點實例目前處於公開預覽狀態。
> 此預覽版本不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="eviction-policy"></a>收回原則

Vm 可以根據容量或您設定的最大價格來收回。 針對虛擬機器，收回原則會設定為 [*解除配置*]，這會將您的已收回 vm 移至已停止（已取消配置）狀態，讓您可以在稍後重新部署已收回的 vm。 不過，重新配置點 Vm 將會相依於是否有可用的點容量。 已解除配置的 Vm 將計入您的點 vCPU 配額，而您將需支付基礎磁片的費用。 

使用者可以選擇透過[Azure Scheduled Events](../articles/virtual-machines/linux/scheduled-events.md)接收 VM 內通知。 這會在您的 Vm 被收回時通知您，而且您將有30秒的時間完成任何作業，並在收回前執行關閉工作。 


| 選項 | 結果 |
|--------|---------|
| [最大價格] 設定為 > = 目前的價格。 | 如果有可用的容量和配額，則會部署 VM。 |
| 最大價格設定為 < 目前的價格。 | 未部署 VM。 您會收到一則錯誤訊息，指出最大價格必須 > = 目前的價格。 |
| 如果最大價格是 >，則重新開機停止/解除配置 VM = 目前的價格 | 如果有容量和配額，則會部署 VM。 |
| 如果最大價格 < 目前價格，則重新開機停止/解除配置 VM | 您會收到一則錯誤訊息，指出最大價格必須 > = 目前的價格。 | 
| VM 的價格已增加，現在已 > 最大價格。 | VM 會被收回。 在實際收回之前，您會收到30秒通知。 | 
| 收回後，VM 的價格會回到 < 最大價格。 | VM 將不會自動重新開機。 您可以自行重新開機 VM，並以目前的價格向您收費。 |
| 如果最大價格設定為 `-1` | 基於定價理由，將不會收回 VM。 最大價格會是目前的價格，最高可達標準 Vm 的價格。 您永遠不會以標準價格收費。| 
| 變更最大價格 | 您必須解除配置 VM，才能變更最大價格。 解除配置 VM、設定 t 新的最大價格，然後更新 VM。 |

## <a name="limitations"></a>限制

下列 VM 大小不支援用於點 Vm：
 - B 系列
 - 任何大小的促銷版本（例如 Dv2、NV、NC、H 促銷大小）

找出 Vm 目前無法使用暫時的 OS 磁片。

找出 Vm 可以部署到任何區域，但 Microsoft Azure 中國的世紀除外。

## <a name="pricing"></a>價格

點 Vm 的定價是以區域和 SKU 為依據的變數。 如需詳細資訊，請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定價。 


有了可變的定價，您可以選擇使用最多5個小數位數來設定最大價格（以美元（美元）為單位）。 例如，`0.98765`的值是每小時 $0.98765 美元的最大價格。 如果您將最大價格設定為 `-1`，將不會根據價格來收回 VM。 VM 的價格將會是標準 VM 的目前價格或價格（這是較少的），只要有可用的容量和配額。


##  <a name="frequently-asked-questions"></a>常見問題集

**問：** 建立後，它是與一般標準 VM 相同的點 VM 嗎？

**答：** 是，但沒有適用于點 Vm 的 SLA，而且可以隨時收回。


**問：** 當您收回但仍需要容量時，該怎麼辦？

**答：** 如果您需要立即使用容量，建議您使用標準 Vm，而不是找出 Vm。


**問：** 如何管理適用于點 Vm 的配額？

**答：** 找出 Vm 將會有個別的配額集區。 點配額會在 Vm 與擴展集實例之間共用。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。


**問：** 我可以要求額外的時間配額嗎？

**答：** 是，您可以提交要求以增加您的配額，以透過[標準配額要求](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)程式來找出您的 vm。


**問：** 哪些通道支援找出 Vm 的位置？

**答：** 請參閱下表中的點機可用性。

<a name="channel"></a>

| Azure 頻道               | Azure 點 Vm 可用性       |
|------------------------------|-----------------------------------|
| Enterprise 合約         | 是                               |
| 隨用隨付                | 是                               |
| 雲端服務提供者 (CSP) | [聯絡您的合作夥伴](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| 優點                     | 無法使用                     |
| 商                    | 無法使用                     |
| 免費試用                   | 無法使用                     |


**問：** 我可以在何處張貼問題？

**答：** 您可以使用[Q & A 的](https://docs.microsoft.com/answers/topics/azure-spot.html)`azure-spot` 來張貼和標記您的問題。 



