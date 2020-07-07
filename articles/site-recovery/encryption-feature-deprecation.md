---
title: Azure Site Recovery 資料加密功能的淘汰 |Microsoft Docs
description: Azure Site Recovery 資料加密功能的詳細資料 regarig
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74134992"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Site Recovery 資料加密功能的淘汰

本檔說明當您在設定 Hyper-v 虛擬機器至 Azure 的嚴重損壞修復時，如果您使用 Site Recovery 資料加密功能時，所需採取的淘汰詳細資訊和修復動作。 

## <a name="deprecation-information"></a>取代資訊


Site Recovery 資料加密功能適用于保護 Hyper-v vm 的客戶，以確保複寫的資料受到保護，以免遭受安全性威脅。 這項功能將于**2019 年12月30日**淘汰。 它正由較先進的待用[加密](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/)功能所取代，其使用[儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)（SSE）。 使用 SSE 時，資料會在保存到儲存體之前進行加密，並在抓取時解密，而在容錯移轉至 Azure 時，您的 Vm 將會從加密的儲存體帳戶執行，以提供改善的復原時間目標（RTO）。

請注意，如果您是使用這項功能的現有客戶，您會收到與淘汰詳細資料和補救步驟的通訊。 


## <a name="what-are-the-implications"></a>有哪些含意？

**2019 年12月30日**之後，仍會使用已淘汰加密功能的任何 vm，都不允許執行容錯移轉。 

## <a name="required-action"></a>必要動作
若要繼續成功的容錯移轉作業，和複寫會遵循以下所述的步驟：

針對每個 VM 執行下列步驟： 
1.  [停](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)用複寫。
2.  [建立新的複寫原則](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy)。
3.  [啟用](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication)複寫，並選取已啟用 SSE 的儲存體帳戶。

完成將初始複寫至已啟用 SSE 的儲存體帳戶之後，您的 Vm 將會使用 Azure Site Recovery 的待用加密。


## <a name="next-steps"></a>後續步驟
規劃執行補救步驟，並在最早的時間執行。 如果您有任何關於此淘汰的查詢，請聯繫 Microsoft 支援服務。 若要深入瞭解 Hyper-v 到 Azure 的案例，請參閱[這裡](hyper-v-vmm-architecture.md)。

