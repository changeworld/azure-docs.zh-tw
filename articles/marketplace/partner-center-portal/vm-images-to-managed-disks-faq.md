---
title: 我們將虛擬機器 （VM） 映射移動到 Azure 應用商店中的託管磁片存儲
description: 為了為新的市場特性和功能提供更快、更可靠的存儲和支援，我們將市場 VM 映射遷移到託管磁片存儲。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 17b418ab82cc88b4368efe247b06c0c8bbb27453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285107"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>我們將 Azure 應用商店上的虛擬機器 （VM） 映射移動到託管磁片存儲

為了為新的市場特性和功能提供更快、更可靠的存儲和支援，我們將市場 VM 映射遷移到託管磁片存儲。

從 2020 年 1 月 2 日開始，我們將分階段將 VM 映射移動到託管磁片存儲。 在第一階段，我們只會在前 90 天內移動沒有新部署或運行 VM 的圖像。 在移動任何圖像之前，我們將發送電子郵件，讓發行者知道哪些圖像將移動以及何時移動。

發行者或消費者不需要執行任何操作，使用者也不會受到影響。 市場優惠將保持可用狀態，客戶仍能夠在移動期間和之後從這些映射部署託管 VM。

如果您有任何問題，[請聯繫我們](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)。

## <a name="faqs"></a>常見問題集

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>VM 映射的使用者是否會遇到中斷？

VM 映射的使用者不會遇到中斷。 

在第一階段，我們將僅移動沒有正在運行的 VM 的 VM 映射。 由於這些圖像沒有使用者，因此不會產生任何影響。 對於後續階段，對使用者也沒有任何影響。

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>完成此過程需要多長時間？

完成遷移可能需要長達 24 小時。

### <a name="do-i-need-to-take-any-action"></a>我需要採取任何行動嗎？

否。 發行者或消費者無需採取任何操作。

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>在雲門戶 API 移動到託管磁片存儲後，我是否必須更新系統以不同方式調用雲門戶 API？

否。 您現有的 API 呼叫將繼續工作。

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>我的所有 VM 映射是否會同時移動到託管磁片？

我們將在同一天移動您的所有 VM 映射。 一旦他們被移動，我們將通知您。

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>我可以請求將 VM 映射的移動安排到以後嗎？

我們建議在計畫日期移動圖像。 但是，如果您有任何疑問，請聯繫我們重新安排搬遷時間。

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>在移動過程中，是否可以將更新發佈到 VM 映射？

在移動過程中無法對 VM 映射進行更新。

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>將 VM 映射移動到託管磁片後，發佈過程是否會更改？

不，發佈過程將保持不變。 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>發行者是否可以將其產品/服務移動到託管磁片？

否，發行者無法將其產品/服務移動到託管磁片。 他們必須等待，他們的圖像將自動移動。 我們將在進行任何更改之前向發行者發送通知。
