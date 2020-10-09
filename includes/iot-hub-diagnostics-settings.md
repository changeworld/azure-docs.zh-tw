---
title: 包含檔案
description: 包含檔案
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f3cab4909937bbf5ccb0f72b194b08810c0487c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792010"
---
### <a name="enable-logging-with-diagnostics-settings"></a>利用診斷設定啟用記錄

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

2. 選取 [診斷設定]。

3. 選取 [開啟診斷]。

   ![開啟診斷](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. 指定診斷設定的名稱。

5. 選擇您要傳送記錄的位置。 您可以選取這三個選項的任意組合：

   * 封存至儲存體帳戶
   * 串流至事件中樞
   * 傳送至 Log Analytics

6. 選擇您想要監視哪些作業，並啟用這些作業的記錄。 診斷設定可以報告的作業為：

   * 連接
   * 裝置遙測
   * 雲端到裝置的訊息
   * 裝置身分識別作業
   * 檔案上傳
   * 訊息路由
   * 雲端到裝置對應項作業
   * 裝置到雲端對應項作業
   * 對應項作業
   * 工作的作業
   * 直接方法  
   * 分散式追蹤 (預覽)
   * 組態
   * 裝置串流
   * 裝置計量

6. 儲存新設定。 

如果您想要使用 PowerShell 開啟診斷設定，請使用下列程式碼：

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

新的設定大約會在 10 分鐘內生效。 之後，記錄就會顯示在 [診斷設定]**** 刀鋒視窗上已設定的封存目標中。 如需有關設定診斷的詳細資訊，請參閱 [收集和取用來自 Azure 資源的記錄資料](../articles/azure-monitor/platform/platform-logs-overview.md)。
