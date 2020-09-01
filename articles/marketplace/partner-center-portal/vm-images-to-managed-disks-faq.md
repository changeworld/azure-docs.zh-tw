---
title: 我們正在將虛擬機器 (VM) 映射移至 Azure Marketplace 中的受控磁片儲存體
description: 為了提供更快、更可靠的儲存體，以及支援新的 marketplace 功能，我們會將 marketplace VM 映射移至受控磁片儲存體。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
author: iqshahmicrosoft
ms.author: iqshah
ms.openlocfilehash: 814a4012ace95e84e75bcb4071c549822b2b0633
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182423"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>我們正在將 Azure Marketplace 的虛擬機器 (VM) 映射移至受控磁片儲存體

為了提供更快、更可靠的儲存體，以及支援新的 marketplace 功能，我們會將 marketplace VM 映射移至受控磁片儲存體。

從2020年1月2日開始，我們會以階段將 VM 映射移至受控磁片儲存體。 在第一個階段中，我們只會在先前的90天內移動沒有新部署或執行中 Vm 的映射。 在移動任何影像之前，我們會傳送一封電子郵件，讓發行者知道將移動哪些影像，以及何時移動它們。

發行者或取用者不需要採取任何動作，使用者也不會受到影響。 Marketplace 供應專案將保持可用，而客戶仍可在移動期間和之後，從這些映射部署受控 Vm。

如果您有任何問題，請 [洽詢我們](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)。

## <a name="faqs"></a>常見問題集

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>我的 VM 映射的使用者是否遇到中斷？

VM 映射的使用者將不會遭遇中斷。 

在第一個階段中，我們只會移動沒有正在執行之 Vm 的 VM 映射。 因為這些影像沒有任何使用者，所以不會有任何影響。 在後續的階段中，對使用者不會造成任何影響。

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>完成此程式需要多久的時間？

完成遷移最多可能需要24小時的時間。

### <a name="do-i-need-to-take-any-action"></a>我需要採取任何動作嗎？

否。 發行者或取用者不需要採取任何動作。

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>在移至受控磁片儲存體之後，我是否必須以不同的方式更新我的系統以呼叫 Cloud Portal Api？

否。 您現有的 API 呼叫將會繼續運作。

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>我的所有 VM 映射是否會同時移至受控磁片？

我們將會在同一天移動您的所有 VM 映射。 一旦移動之後，我們會通知您。

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>我是否可以要求將 VM 映射的移動排程到較晚的時間？

建議您在排程的日期移動影像。 但是，如果您有疑慮，請與我們聯繫以重新排程移動。

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>我是否可以在移動期間將更新發佈至我的 VM 映射？

移動時無法進行 VM 映射的更新。

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>將 VM 映射移至受控磁片之後，發佈程式是否會變更？

否，發行程式會維持不變。 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>發行者是否可以將其供應專案移至受控磁片？

否，發行者無法將其供應專案移至受控磁片。 他們必須等待，並且會自動移動其影像。 我們會在進行任何變更之前，先將通知傳送給發行者。
