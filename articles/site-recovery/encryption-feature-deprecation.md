---
title: 淘汰 Azure Site Recovery 資料加密功能 |Microsoft Docs
description: Azure Site Recovery 資料加密功能的詳細資料 regarig
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: sharrai
ms.openlocfilehash: 5860928d71c0e7431190908d5df5d7496e8ffb17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426311"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>淘汰 Site Recovery 資料加密功能

本檔說明當您在設定 Hyper-v 虛擬機器至 Azure 的嚴重損壞修復時使用 Site Recovery 資料加密功能時，所需採取的淘汰詳細資料和補救動作。 

## <a name="deprecation-information"></a>取代資訊


Site Recovery 的資料加密功能可供保護 Hyper-v vm 的客戶使用，以確保複寫的資料免于受到安全性威脅的保護。 這項功能將于 **2022 年4月30日**淘汰。 它會被「更先進的待用 [加密](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) 」功能取代，這會使用 [儲存體服務加密](../storage/common/storage-service-encryption.md) (SSE) 。 使用 SSE 時，會在保存到儲存體之前加密資料，並在抓取時解密，然後在容錯移轉至 Azure 時，您的 Vm 會從加密的儲存體帳戶執行，以改善復原時間目標 (RTO) 。

請注意，如果您是使用這項功能的現有客戶，您會收到與淘汰詳細資料和補救步驟的通訊。 


## <a name="what-are-the-implications"></a>有何影響？

**2022 年4月30日**之後，任何仍使用停用加密功能的 vm 都不會執行容錯移轉。 

## <a name="required-action"></a>必要動作
若要繼續成功的容錯移轉作業，複寫會遵循下列步驟：

針對每部 VM，請遵循下列步驟： 
1.  [停](./site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)用複寫。
2.  [建立新的複寫原則](./hyper-v-azure-tutorial.md#set-up-a-replication-policy)。
3.  [啟用](./hyper-v-vmm-azure-tutorial.md#enable-replication) 複寫，並選取啟用 SSE 的儲存體帳戶。

完成初始複寫至已啟用 SSE 的儲存體帳戶之後，您的 Vm 將會使用 Azure Site Recovery 的待用加密。


## <a name="next-steps"></a>後續步驟
規劃執行補救步驟，並在最早的時間執行。 如果您有任何關於此淘汰的查詢，請與 Microsoft 支援服務聯繫。 若要深入瞭解 Hyper-v 到 Azure 的案例，請參閱 [這裡](hyper-v-vmm-architecture.md)。
