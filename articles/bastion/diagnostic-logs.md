---
title: 啟用及使用 Azure 防禦資源記錄
description: 在本文中，您將瞭解如何啟用及使用 Azure 防禦診斷記錄。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 1e76fffd17ee565d4103ca8a7bf1523bbd16209d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445380"
---
# <a name="enable-and-work-with-bastion-resource-logs"></a>啟用和使用 Bastion 資源記錄

當使用者使用 Azure 防禦連接到工作負載時，防禦可以記錄遠端會話的診斷。 然後，您可以使用診斷來查看哪些使用者連接到哪些工作負載、哪些時間、從何處，以及其他這類相關的記錄資訊。 您必須啟用 Azure 防禦的診斷記錄，才能使用診斷。 本文可協助您啟用診斷記錄，然後再查看記錄。

## <a name="enable-the-resource-log"></a><a name="enable"></a>啟用資源記錄檔

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您的 azure 防禦資源，然後從 Azure 防禦頁面選取 [ **診斷設定** ]。

   ![顯示 [診斷設定] 頁面的螢幕擷取畫面。](./media/diagnostic-logs/1diagnostics-settings.png)
2. 選取 [ **診斷設定**]，然後選取 [ **+ 新增診斷設定** ] 以新增記錄的目的地。

   ![顯示 [診斷設定] 頁面的螢幕擷取畫面，其中已選取 [新增診斷設定] 按鈕。](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. 在 [ **診斷設定** ] 頁面上，選取要用於儲存診斷記錄的儲存體帳戶類型。

   ![[診斷設定] 頁面的螢幕擷取畫面，其中包含用來選取醒目提示之儲存位置的區段。](./media/diagnostic-logs/3add-storage-account.png)
4. 當您完成設定時，它看起來會像這個範例：

   ![範例設定](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>查看診斷記錄

若要存取您的診斷記錄，您可以直接使用您在啟用診斷設定時所指定的儲存體帳戶。

1. 流覽至您的儲存體帳戶資源，然後流覽至 **容器**。 您會看到在儲存體帳戶 blob 容器中建立的 **bastionauditlogs blob 記錄** 。

   ![診斷設定](./media/diagnostic-logs/1-navigate-to-logs.png)
2. 當您在容器內流覽至時，您會在您的 blog 中看到不同的資料夾。 這些資料夾指出 Azure 防禦資源的資源階層。

   ![新增診斷設定](./media/diagnostic-logs/2-resource-h.png)
3. 流覽至您想要存取/查看其診斷記錄的 Azure 堡壘資源的完整階層。 ' Y = '、' = '、' = '、' h = ' 和 ' m = ' 分別指出資源記錄檔的年、月、日、小時和分鐘。

   ![選取儲存位置](./media/diagnostic-logs/3-resource-location.png)
4. 找出 Azure 防禦所建立的 json 檔案，其中包含流覽至的時間週期的診斷記錄資料。

5. 從儲存體 blob 容器下載 json 檔案。 以下顯示 json 檔案中的範例專案，以供參考：

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>後續步驟

閱讀 [Bastion 常見問題集](bastion-faq.md)。
