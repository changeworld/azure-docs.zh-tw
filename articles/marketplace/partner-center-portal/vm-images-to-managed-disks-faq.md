---
title: 我們正將虛擬機器（VM）映射移至 Azure Marketplace 中的受控磁片儲存體
description: 為了提供更快、更可靠的儲存空間，並支援新的 marketplace 功能，我們會將 marketplace VM 映射移至受控磁片儲存體。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 17b418ab82cc88b4368efe247b06c0c8bbb27453
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80285107"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>我們正在將 Azure Marketplace 的虛擬機器（VM）映射移至受控磁片儲存體

為了提供更快、更可靠的儲存空間，並支援新的 marketplace 功能，我們會將 marketplace VM 映射移至受控磁片儲存體。

從2020年1月2日開始，我們會在幾個階段中將 VM 映射移至受控磁片儲存體。 在第一個階段中，我們只會在過去90天內移動沒有新部署或執行 Vm 的映射。 在移動任何影像之前，我們會傳送一封電子郵件，讓發行者知道哪些影像會移動，以及何時將移動。

發行者或取用者不需要採取任何動作，使用者也不會受到影響。 Marketplace 供應專案將維持可用狀態，而且客戶仍然可以在移動期間和之後，從這些映射部署受管理的 Vm。

如有任何疑問，請[洽詢我們](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)。

## <a name="faqs"></a>常見問題集

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>VM 映射的使用者是否會遇到中斷的情況？

VM 映射的使用者將不會遇到中斷的情況。 

在第一個階段中，我們只會移動沒有執行中 Vm 的 VM 映射。 因為這些映射沒有任何使用者，所以不會有任何影響。 針對後續階段，將不會對使用者造成任何影響。

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>完成處理常式需要多久的時間？

最多可能需要24小時才能完成遷移。

### <a name="do-i-need-to-take-any-action"></a>我需要採取任何動作嗎？

不會。 發行者或取用者不需要採取任何動作。

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>我是否必須在將系統移至受控磁片儲存體之後，以不同的方式來更新我的系統以呼叫雲端入口網站 Api？

不會。 您現有的 API 呼叫將會繼續作用。

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>我的所有 VM 映射會同時移至受控磁片嗎？

我們會在同一天移動所有 VM 映射。 一旦移動之後，我們會通知您。

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>我可以要求排程將 VM 映射移至稍後的時間嗎？

我們建議您在排程的日期移動影像。 不過，如果您有任何疑慮，請與我們聯繫以重新排定移動。

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>我可以在移動期間發佈 VM 映射的更新嗎？

在移動期間無法進行 VM 映射的更新。

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>在我的 VM 映射移至受控磁片之後，發佈程式會變更嗎？

否，發佈程式會維持不變。 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>發行者可以將其供應專案移至受控磁片嗎？

否，發行者無法將其供應專案移至受控磁片。 他們必須等待，其影像才會自動移動。 我們會先將通知傳送至發行者，再進行任何變更。
