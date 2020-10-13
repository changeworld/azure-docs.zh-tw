---
title: 如何跨區域遷移 Azure API 管理
description: 瞭解如何將 API 管理實例從一個區域遷移至另一個區域。
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 0eed2328aca78402c5f4691bb9b3d07d4f36472e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86250221"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>如何跨區域遷移 Azure API 管理
若要將 API 管理實例從某個 Azure 區域遷移至另一個區域，您可以使用 [備份和還原](api-management-howto-disaster-recovery-backup-restore.md) 功能。 您應該在來源和目的地區域中選擇相同的 API 管理定價層。 

> [!NOTE]
> 在不同的雲端類型之間進行遷移時，備份和還原將無法運作。 針對這一點，您必須將資源匯出 [為範本](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)。 然後，調整目標 Azure 區域的匯出範本，然後重新建立資源。 

## <a name="option-1-use-a-different-api-management-instance-name"></a>選項1：使用不同的 API 管理實例名稱

1. 在目的地區域中，使用與來源 API 管理實例相同的定價層來建立新的 API 管理實例。 新的實例應該有不同的名稱。 
1. 將現有的 API 管理實例備份至儲存體帳戶。
1. 將步驟2中建立的備份還原至步驟1新區域中建立的新 API 管理實例。
1. 如果您有指向來源區域 API 管理實例的自訂網域，請更新自訂網域 CNAME 以指向新的 API 管理實例。 


## <a name="option-2-use-the-same-api-management-instance-name"></a>選項2：使用相同的 API 管理實例名稱

> [!NOTE]
> 此選項會在遷移期間造成停機時間。

1. 將來源區域中的 API 管理實例備份至儲存體帳戶。
1. 刪除來源區域中的 API 管理。 
1. 在目的地區域中建立新的 API 管理實例，其名稱與來源區域中的相同。
1. 將步驟1中建立的備份還原到目的地區域中的新 API 管理實例。  


## <a name="next-steps"></a><a name="next-steps"> </a>後續步驟
* 如需有關備份和還原功能的詳細資訊，請參閱 [如何執行](api-management-howto-disaster-recovery-backup-restore.md)嚴重損壞修復。
* 如需有關遷移 Azure 資源的詳細資訊，請參閱 [azure 跨區域遷移指引](https://github.com/Azure/Azure-Migration-Guidance)。
* [優化並節省您的雲端費用](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
