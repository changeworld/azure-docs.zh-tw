---
title: 如何跨區域遷移 Azure API 管理
description: 瞭解如何將 API 管理實例從一個區域遷移到另一個區域。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073471"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>如何跨區域遷移 Azure API 管理
要將 API 管理實例從一個 Azure 區域遷移到另一個 Azure 區域，可以使用[備份和還原](api-management-howto-disaster-recovery-backup-restore.md)功能。 您應該在源區域和目的地區域中選擇相同的 API 管理定價層。 

> [!NOTE]
> 在不同的雲類型之間遷移時，備份和還原不起作用。 為此，您需要將資源[匯出為範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)。 然後，調整目標 Azure 區域的匯出範本並重新創建資源。 

## <a name="option-1-use-a-different-api-management-instance-name"></a>選項 1：使用其他 API 管理實例名稱

1. 在目的地區域中，創建一個與源 API 管理實例具有相同定價層的新 API 管理實例。 新實例應具有不同的名稱。 
1. 將現有的 API 管理實例備份到存儲帳戶。
1. 將步驟 2 中創建的備份還原到步驟 1 中新區域中創建的新 API 管理實例。
1. 如果有指向源區域 API 管理實例的自訂域，請更新自訂域 CNAME 以指向新的 API 管理實例。 


## <a name="option-2-use-the-same-api-management-instance-name"></a>選項 2：使用相同的 API 管理實例名稱

> [!NOTE]
> 此選項將導致遷移期間停機。

1. 將源區域中的 API 管理實例備份到存儲帳戶。
1. 刪除源區域中的 API 管理。 
1. 在目的地區域中創建一個與源區域中具有相同名稱的新 API 管理實例。
1. 將步驟 1 中創建的備份還原到目的地區域中的新 API 管理實例。  


## <a name="next-steps"></a><a name="next-steps"> </a>後續步驟
* 有關備份和還原功能的詳細資訊，請參閱[如何實現災害復原](api-management-howto-disaster-recovery-backup-restore.md)。
* 有關遷移 Azure 資源的資訊，請參閱[Azure 跨區域遷移指南](https://github.com/Azure/Azure-Migration-Guidance)。