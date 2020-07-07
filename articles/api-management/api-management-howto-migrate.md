---
title: 如何跨區域遷移 Azure API 管理
description: 瞭解如何將 API 管理實例從一個地區遷移至另一個區域。
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
ms.openlocfilehash: 3294a7b2112e9527041ef343f4452aedb7a2a272
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "70073471"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>如何跨區域遷移 Azure API 管理
若要將 API 管理實例從一個 Azure 區域遷移至另一個區域，您可以使用[備份和還原](api-management-howto-disaster-recovery-backup-restore.md)功能。 您應該在來源和目的地區域中選擇相同的 API 管理定價層。 

> [!NOTE]
> 在不同的雲端類型之間進行遷移時，備份和還原將不會有作用。 為此，您必須將資源匯出[為範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)。 然後，針對目標 Azure 區域調整已匯出的範本，然後重新建立資源。 

## <a name="option-1-use-a-different-api-management-instance-name"></a>選項1：使用不同的 API 管理實例名稱

1. 在目的地區域中，使用與來源 API 管理實例相同的定價層，建立新的 API 管理實例。 新的實例應該要有不同的名稱。 
1. 將現有的 API 管理實例備份至儲存體帳戶。
1. 將步驟2中建立的備份還原至步驟1的新區域中建立的新 API 管理實例。
1. 如果您有指向來源區域 API 管理實例的自訂網域，請更新自訂網域 CNAME 以指向新的 API 管理實例。 


## <a name="option-2-use-the-same-api-management-instance-name"></a>選項2：使用相同的 API 管理實例名稱

> [!NOTE]
> 此選項會導致遷移期間的停機時間。

1. 將來源區域中的 API 管理實例備份至儲存體帳戶。
1. 刪除來源區域中的 API 管理。 
1. 在目的地區域中建立新的 API 管理實例，其名稱與來源區域中的相同。
1. 將步驟1中建立的備份還原至目的地區域中的新 API 管理實例。  


## <a name="next-steps"></a><a name="next-steps"> </a>後續步驟
* 如需有關備份和還原功能的詳細資訊，請參閱[如何執行損毀修復](api-management-howto-disaster-recovery-backup-restore.md)。
* 如需有關遷移 Azure 資源的詳細資訊，請參閱[azure 跨區域遷移指引](https://github.com/Azure/Azure-Migration-Guidance)。