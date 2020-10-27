---
title: 還原已卸載的適用於 MySQL 的 Azure 資料庫伺服器
description: 本文說明如何使用 Azure 入口網站，在適用於 MySQL 的 Azure 資料庫中還原已卸載的伺服器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 982747c1a7e093f84daeb63e75cfdf439d3fccf9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546716"
---
# <a name="restore-a-dropped-azure-database-for-mysql-server"></a>還原已卸載的適用於 MySQL 的 Azure 資料庫伺服器

當伺服器卸載時，資料庫伺服器備份最多可在服務中保留五天。 您只能從伺服器原本所在的 Azure 訂用帳戶存取及還原資料庫備份。 您可以遵循下列建議步驟，在刪除伺服器後的 5 天內復原已卸除的 MySQL 伺服器資源。 只有在伺服器的備份仍可供使用，且未從系統中刪除時，建議的步驟才會生效。 

## <a name="pre-requisites"></a>必要條件
若要還原已卸載的適用於 MySQL 的 Azure 資料庫伺服器，您需要下列各項：
- 裝載源伺服器的 Azure 訂用帳戶名稱
- 建立伺服器的位置

## <a name="steps-to-restore"></a>還原的步驟

1. 從 Azure 入口網站中的 [監視] 分頁移至 [ [活動記錄](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) ]。 

2. 在 [活動記錄] 中，按一下 [ **新增篩選** ] （如所示），並針對下列各項設定篩選 

    - **訂** 用帳戶 = 裝載已刪除伺服器的訂用帳戶
    - **資源類型** = 適用於 MySQL 的 Azure 資料庫伺服器 (microsoft.dbformysql/伺服器)  
    - **Operation** = 刪除 MySQL Server (microsoft.dbformysql/servers/delete)  
 
     [![針對刪除 MySQL 伺服器操作篩選的活動記錄](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. 按兩下 [刪除 MySQL 伺服器] 事件，然後按一下 [JSON] 索引標籤，並記下 JSON 輸出中的 "resourceId" 和 "submissionTimestamp" 屬性。 ResourceId 的格式如下：/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver。
 
 4. 移至 [ [建立伺服器 REST API] 頁面](/rest/api/mysql/servers/create) ，然後按一下 [試用] 索引標籤（以綠色醒目提示），然後使用您的 Azure 帳戶登入。
 
 5. 提供 resourceGroupName、serverName (已刪除的伺服器名稱) 、subscriptionId、衍生自步驟3中所捕捉的 resourceId 屬性，並預先填入 api 版本，如影像中所示。
 
     [![使用 REST API 建立伺服器](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. 在下方的 [要求本文] 區段中，貼上下列內容以取代「捨棄的伺服器位置」、「submissionTimestamp」和「resourceId」。 若為 "restorePointInTime"，請指定 "submissionTimestamp" 的值減去 **15 分鐘** ，以確定命令不會發生錯誤。
 
    ```json
        {
          "location": "Dropped Server Location",  
          "properties": 
              {
                  "restorePointInTime": "submissionTimestamp - 15 minutes",
                  "createMode": "PointInTimeRestore",
                  "sourceServerId": "resourceId"
            }
        }
    ```

7. 如果您看到回應碼201或202，則會成功提交還原要求。 

8. 伺服器建立可能需要一些時間，視源伺服器上布建的資料庫大小和計算資源而定。 您可以篩選以從活動記錄監視還原狀態。 
   - **訂** 用帳戶 = 您的訂用帳戶
   - **資源類型** = 適用於 MySQL 的 Azure 資料庫伺服器 (microsoft.dbformysql/伺服器)  
   - **Operation** = 更新 MySQL Server Create

## <a name="next-steps"></a>下一步
- 如果您嘗試在五天內還原伺服器，而且在正確遵循先前所討論的步驟之後仍會收到錯誤，請開啟支援事件以取得協助。 如果您嘗試在五天后還原已卸載的伺服器，則預期會發生錯誤，因為找不到備份檔案。 請勿在此案例中開啟支援票證。 如果已從系統中刪除備份，支援小組無法提供任何協助。 
- 為了避免意外刪除伺服器，我們強烈建議使用 [資源鎖定](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)。