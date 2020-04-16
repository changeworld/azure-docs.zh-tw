---
title: 包含檔案
description: 包含檔案
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4063751a71cd9cecc424dfe3daddaecfd9ea4071
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421918"
---
使用 Spot VM 使您能夠利用我們未使用的容量,從而顯著節省成本。 在 Azure 需要返回容量的任何時間點,Azure 基礎結構將驅逐 Spot VM。 因此,Spot VM 非常適合處理批次處理作業、開發/測試環境、大型計算工作負載等中斷的工作負載。

可用容量的數量可能因大小、區域、一天中的時間等而異。 部署 Spot VM 時,如果可用容量,但這些 VM 沒有 SLA,Azure 將分配 VM。 Spot VM 不提供高可用性保證。 在 Azure 需要返回容量的任何時間點,Azure 基礎結構將在 30 秒通知後逐出 Spot VM。 


## <a name="eviction-policy"></a>驅逐政策

VM 可以根據容量或您設置的最大價格被逐出。 對於虛擬機,逐出策略設置為 *「取消分配*」,這將逐出 VM 移至已停止的已停止的已停止的已停止位置狀態,從而允許您在以後重新部署已展開的 VM。 但是,重新分配 Spot VM 將取決於存在可用的 Spot 容量。 交易定位的 VM 將計入您的現場 vCPU 配額,您將為基礎磁碟付費。 

用戶可以選擇加入通過[Azure 計劃事件](../articles/virtual-machines/linux/scheduled-events.md)接收 VM 內通知。 如果 VM 被逐出,您將通知您,並且在驅逐之前,您將有 30 秒的時間完成任何作業並執行關閉任務。 


| 選項 | 成果 |
|--------|---------|
| 最高價格設置為>= 當前價格。 | 如果容量和配額可用,則部署 VM。 |
| 最高價格設置為<當前價格。 | 未部署 VM。 您會收到一條錯誤消息,指出最高價格需要>= 當前價格。 |
| 如果最大價格>= 當前價格,則重新啟動停止/取消分配 VM | 如果有容量和配額,則部署 VM。 |
| 如果最高價格<當前價格,則重新啟動停止/取消分配 VM | 您會收到一條錯誤消息,指出最高價格需要>= 當前價格。 | 
| VM 的價格已經上漲,現在>最高價格。 | VM將被逐出。 在實際驅逐之前,您會收到 30s 的通知。 | 
| 驅逐后,VM 的價格會回到<最高價格。 | 不會自動重新啟動 VM。 您可以自行重新啟動 VM,並將按當前價格收費。 |
| 如果最高價格設定為`-1` | 出於定價原因,不會逐出 VM。 最高價格將是當前價格,最高為標準 VM 的價格。 您永遠不會被收取高於標準價格的費用。| 
| 變更最高價格 | 您需要取消分配 VM 以更改最大價格。 取消分配 VM,設置新的最高價格,然後更新 VM。 |

## <a name="limitations"></a>限制

Spot VM 不支援以下 VM 大小:
 - B 系列
 - 任何尺寸的促銷版本(如 Dv2、NV、NC、H 促銷尺寸)

Spot VM 目前無法使用臨時作業系統磁碟。

除 Microsoft Azure 中國 21Vianet 外,Spot VM 都可以部署到任何區域。

## <a name="pricing"></a>定價

現貨 VM 的定價基於區域和 SKU 是可變的。 有關詳細資訊,請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定價。 


使用可變定價,您可以選擇使用最多 5 個小數位設置最高價格(美元 (USD)。 例如,該值`0.98765`將是每小時 0.98765 美元的最高價格。 如果將最高價格設置為`-1`,則 VM 不會根據價格被逐出。 VM 的價格將是現貨的當前價格或標準 VM 的價格,只要有容量和配額可用,標準 VM 的價格就更少了。


##  <a name="frequently-asked-questions"></a>常見問題集

**問:** 建立後,Spot VM 是否與常規標準 VM 相同?

**答:** 是,除了 Spot VM 沒有 SLA,它們可以隨時被逐出。


**問:** 當您被逐出時該怎麼辦,但仍需要容量?

**答:** 如果您需要容量,我們建議您使用標準 VM 而不是 Spot VM。


**問:** 如何管理 Spot VM 的配額?

**答:** Spot VM 將具有單獨的配額池。 競價配額將在 VM 和縮放設置實例之間共用。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。


**問:** 我可以申請 Spot 的額外配額嗎?

**答:** 是的,您將能夠通過[標準配額請求流程](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)提交請求,以增加 Spot VM 的配額。


**問:** 哪些通道支援 Spot VM?

**答:** 有關 Spot VM 可用性,請參閱下表。

<a name="channel"></a>

| Azure 頻道               | Azure Spot VM 可用性       |
|------------------------------|-----------------------------------|
| Enterprise 合約         | 是                               |
| 隨用隨付                | 是                               |
| 雲端服務提供者 (CSP) | [連絡您的合作夥伴](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Microsoft 客戶合約 | 是                               |
| 優點                     | 無法使用                     |
| 贊助                    | 無法使用                     |
| 免費試用                   | 無法使用                     |


**問:** 我在哪裡可以發佈問題?

**答:** 您可以在[Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html)`azure-spot`上發表和標記您的問題。 



