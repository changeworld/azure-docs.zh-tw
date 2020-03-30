---
title: 啟用和使用 Azure 堡壘診斷日誌
description: 在本文中，瞭解如何啟用和使用 Azure 堡壘診斷日誌。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2167a17d5d388c97ad357398c4ac2676e43be5a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989445"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>啟用和使用堡壘診斷日誌

當使用者使用 Azure Bastion 連接到工作負荷時，Bastion 可以記錄遠端會話的診斷。 然後，可以使用診斷來查看哪些使用者連接到哪些工作負荷、在什麼時間、從何處以及其他此類相關日誌記錄資訊。 為了使用診斷，必須在 Azure 堡壘上啟用診斷日誌。 本文可説明您啟用診斷日誌，然後查看日誌。

## <a name="enable-the-diagnostics-log"></a><a name="enable"></a>啟用診斷日誌

1. 在[Azure 門戶](https://portal.azure.com)中，導航到 Azure 堡壘資源，並從 Azure 堡壘頁中選擇 **"診斷"設置**。

   ![診斷設置](./media/diagnostic-logs/1diagnostics-settings.png)
2. 選擇**診斷設置**，然後選擇 **"添加診斷設置**"以添加日誌的目標。

   ![添加診斷設置](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. 在 **"診斷設置"** 頁上，選擇要用於存儲診斷日誌的存儲帳戶類型。

   ![選擇存儲位置](./media/diagnostic-logs/3add-storage-account.png)
4. 完成設置後，它將類似于此示例：

   ![示例設置](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>查看診斷日誌

要訪問診斷日誌，可以直接使用啟用診斷設置時指定的存儲帳戶。

1. 導航到存儲帳戶資源，然後導航到**容器**。 您將看到在存儲帳戶 blob 容器中創建的**見解日誌-堡壘稽核記錄 blob。**

   ![診斷設置](./media/diagnostic-logs/1-navigate-to-logs.png)
2. 導航到容器內部時，在博客中可以看到各種資料夾。 這些資料夾指示 Azure 堡壘資源的資源層次結構。

   ![添加診斷設置](./media/diagnostic-logs/2-resource-h.png)
3. 導航到要訪問/查看其診斷日誌的 Azure Bastion 資源的完整層次結構。 診斷日誌的"y"、"m=""d=""h="和"m="分別指示診斷日誌的年份、月份、日、小時和分鐘。

   ![選擇存儲位置](./media/diagnostic-logs/3-resource-location.png)
4. 查找 Azure Bastion 創建的 json 檔，該檔包含導航到的時間段的診斷日誌資料。

5. 從存儲 Blob 容器下載 json 檔。 json 檔的示例條目如下所示供參考：

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

閱讀[堡壘常見問題解答](bastion-faq.md)。
