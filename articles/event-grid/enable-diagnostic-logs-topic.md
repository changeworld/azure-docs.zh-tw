---
title: Azure 事件網格 - 為主題啟用診斷日誌
description: 本文提供有關如何為 Azure 事件網格主題啟用診斷日誌的分步說明。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960499"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Azure 事件網格主題的診斷日誌
診斷設置允許事件網格使用者在以下位置之一捕獲和查看發佈和傳遞失敗日誌：Azure 存儲帳戶、事件中心或日誌分析工作區。 本文提供分步說明，用於為事件網格主題啟用診斷日誌。

## <a name="prerequisites"></a>Prerequisites

- 預配事件網格主題
- 用於捕獲診斷日誌的預配目標。 它可以是以下目標之一：
    - Azure 儲存體帳戶
    - 事件中樞
    - Log Analytics 工作區


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>為主題啟用診斷日誌的步驟

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 導航到要為其啟用診斷日誌設置的事件網格主題。 
3. 在左側功能表中的 **"監視"** 下選擇**診斷設置**。
4. 在 **"診斷設置"** 頁上，選擇"**添加新診斷設置**"。 
    
    ![添加診斷設置按鈕](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 為診斷設置指定**名稱**。 

    ![診斷設置 - 名稱](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. 為日誌啟用一個或多個捕獲目標，然後通過選擇以前創建的捕獲資源來配置它們。 
    - 如果選擇 **"存檔到存儲帳戶**"，請選擇 **"存儲帳戶 - 配置**"，然後在 Azure 訂閱中選擇存儲帳戶。 

        ![存檔到 Azure 存儲帳戶](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - 如果選擇 **"流到事件中心**"，請選擇**事件中心 - 配置**，然後選擇事件中心命名空間、事件中心和訪問策略。 
        ![資料流到事件中心](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - 如果選擇 **"發送到日誌分析**"，請選擇日誌分析工作區。
        ![傳送至 Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. 在 **"日誌"** 部分中選擇 **"傳遞失敗**"和 **"發佈失敗"** 選項。 
    ![選擇故障](./media/enable-diagnostic-logs-topic/log-failures.png)
8. 選取 [儲存]****。 在右角選擇**X**以關閉頁面。 
9. 現在，回到 **"診斷設置"** 頁上，確認在 **"診斷設置"** 表中看到新條目。 
    ![清單中的診斷設置](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     您還可以啟用本主題的所有指標的集合。 

## <a name="next-steps"></a>後續步驟
如果您需要更多説明，請在[堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-eventgrid)中發佈您的問題或打開[支援票證](https://azure.microsoft.com/support/options/)。 
