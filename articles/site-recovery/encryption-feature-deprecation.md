---
title: 棄用 Azure 網站恢復資料加密功能 |微軟文檔
description: 詳細資訊 雷加里格 Azure 網站恢復資料加密功能
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74134992"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>棄用網站恢復資料加密功能

本文檔介紹在將 Hyper-V 虛擬機器的災害復原配置為 Azure 時使用網站恢復資料加密功能時需要執行的棄用詳細資訊和補救措施。 

## <a name="deprecation-information"></a>棄用資訊


網站恢復資料加密功能可用於保護 Hyper-V vms 的客戶，以確保複製的資料免受安全威脅。 此功能將在**2019 年 12 月 30**日前棄用。 它正被使用[存儲服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)（SSE） 的更高級[的靜態加密](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/)功能所取代。 使用 SSE，資料在保存到存儲之前進行加密，並在檢索時解密，並且在容錯移轉到 Azure 時，VM 將從加密存儲帳戶運行，從而改進恢復時間目標 （RTO）。

請注意，如果您是使用此功能的現有客戶，您就會收到與棄用詳細資訊和補救步驟的通信。 


## <a name="what-are-the-implications"></a>有何影響？

**2019 年 12 月 30 日之後**，任何仍使用已停用加密功能的 VM 將不允許執行容錯移轉。 

## <a name="required-action"></a>所需操作
要繼續成功容錯移轉操作，複製將遵循以下步驟：

按照每個 VM 的以下步驟操作： 
1.  [禁用複製](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)。
2.  [創建新的複寫原則](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy)。
3.  [啟用複製](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication)並選擇啟用 SSE 的存儲帳戶。

在啟用了 SSE 後完成對存儲帳戶的初始複製後，VM 將使用"靜態加密"與 Azure 網站恢復。


## <a name="next-steps"></a>後續步驟
計畫執行修正步驟，並儘早執行這些步驟。 如果您對此棄用有任何疑問，請聯繫 Microsoft 支援部門。 要瞭解有關 Hyper-V 到 Azure 方案的詳細資訊，請參閱[此處](hyper-v-vmm-architecture.md)。

