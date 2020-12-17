---
title: 還原已卸載的適用於 PostgreSQL 的 Azure 資料庫伺服器
description: 本文說明如何使用 Azure 入口網站，在適用於 PostgreSQL 的 Azure 資料庫中還原已卸載的伺服器。
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 591f01004cfba247112f702625ab05ddc0aaede3
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652920"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>還原已卸載的適用於 PostgreSQL 的 Azure 資料庫伺服器

當伺服器卸載時，資料庫伺服器備份最多可在服務中保留五天。 您只能從伺服器原本所在的 Azure 訂用帳戶存取及還原資料庫備份。 您可以遵循下列建議步驟，從伺服器刪除時間的5天內復原已卸載的于 postgresql 伺服器資源。 只有在伺服器的備份仍可供使用，且未從系統中刪除時，建議的步驟才會生效。 

## <a name="pre-requisites"></a>必要條件
若要還原已卸載的適用於 PostgreSQL 的 Azure 資料庫伺服器，您需要下列各項：
- 裝載源伺服器的 Azure 訂用帳戶名稱
- 建立伺服器的位置

## <a name="steps-to-restore"></a>還原的步驟

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)。 選取 **Azure 監視器** 服務，然後選取 [ **活動記錄**]。

2. 在 [活動記錄] 中，按一下 [ **新增篩選** ] （如所示），並為下列各項設定下列篩選

    - **訂** 用帳戶 = 裝載已刪除伺服器的訂用帳戶
    - **資源類型** = 適用於 PostgreSQL 的 Azure 資料庫伺服器 (DBforPostgreSQL/伺服器) 
    - **Operation** = 刪除于 postgresql Server (DBforPostgreSQL/servers/delete) 
 
    ![篩選刪除于 postgresql 伺服器作業的活動記錄](./media/howto-restore-dropped-server/activity-log-azure.png)

3. 選取 [ **刪除于 postgresql 伺服器** ] 事件，然後選取 [ **JSON]** 索引標籤。複製 `resourceId` `submissionTimestamp` JSON 輸出中的和屬性。 ResourceId 的格式如下： `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` 。


 4. 流覽至於 postgresql 的 [ [建立伺服器 REST API] 頁面](/rest/api/PostgreSQL/servers/create) ，然後選取 [ **試用** ] 索引標籤（以綠色反白顯示）。 使用您的 Azure 帳戶進行登入。

 5. 根據在先前步驟3中所捕獲的 resourceId 屬性 JSON 值，提供 **resourceGroupName**、 **serverName** (已刪除的伺服器名稱) 、 **subscriptionId** 屬性。 [Api 版本] 屬性已預先填入，可保持原樣，如下圖所示。

    ![使用 REST API 建立伺服器](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
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

    例如，如果目前的時間是 2020-11-02T23：59： 59.0000000 Z，建議您至少15分鐘之前的還原點（2020-11-02T23：44： 59.0000000 Z）。

    > [!Important]
    > 卸載伺服器之後，有五天的時間限制。 五天后，預期會發生錯誤，因為找不到備份檔案。
    
7. 如果您看到回應碼201或202，則會成功提交還原要求。 

    伺服器建立可能需要一些時間，視源伺服器上布建的資料庫大小和計算資源而定。 您可以篩選以從活動記錄監視還原狀態。 
   - **訂** 用帳戶 = 您的訂用帳戶
   - **資源類型** = 適用於 PostgreSQL 的 Azure 資料庫伺服器 (DBforPostgreSQL/伺服器)  
   - **Operation** = Update 于 postgresql Server Create

## <a name="next-steps"></a>後續步驟
- 如果您嘗試在五天內還原伺服器，而且在正確遵循先前所討論的步驟之後仍會收到錯誤，請開啟支援事件以取得協助。 如果您嘗試在五天后還原已卸載的伺服器，則預期會發生錯誤，因為找不到備份檔案。 請勿在此案例中開啟支援票證。 如果已從系統中刪除備份，支援小組無法提供任何協助。 
- 為了避免意外刪除伺服器，我們強烈建議使用 [資源鎖定](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222)。
