---
title: 設定診斷
titleSuffix: Azure Digital Twins
description: 請參閱如何使用診斷設定來啟用記錄。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 0376a57e3f2c1158e9da97fb291a28c99ce2463c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903949"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>針對 Azure 數位 Twins 進行疑難排解：診斷記錄

Azure 數位 Twins 會收集您的服務實例的[計量](troubleshoot-metrics.md)，以提供資源狀態的相關資訊。 您可以使用這些計量來評估 Azure 數位 Twins 服務及其連線資源的整體健全狀況。 這些使用者對應的統計資料可協助您瞭解 Azure 數位 Twins 的情況，並協助對問題執行根本原因分析，而不需要聯絡 Azure 支援。

本文說明如何針對您的 Azure 數位 Twins 實例中的計量資料開啟**診斷記錄**。 您可以使用這些記錄來協助您針對服務問題進行疑難排解，並設定診斷設定，以將 Azure 數位 Twins 度量傳送至不同的目的地。 若要深入瞭解這些設定，請參閱[*建立診斷設定以將平臺記錄和計量傳送至不同的目的地*](../azure-monitor/platform/diagnostic-settings.md)。

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>使用 Azure 入口網站開啟診斷設定

以下說明如何為您的 Azure 數位 Twins 實例啟用診斷設定：

1. 登入[Azure 入口網站](https://portal.azure.com)，然後流覽至您的 Azure 數位 Twins 實例。 您可以在入口網站的搜尋列中輸入其名稱來尋找它。 

2. 從功能表中選取 [**診斷設定**]，然後按一下 [**新增診斷設定**]。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="顯示 [診斷設定] 頁面和要新增之按鈕的螢幕擷取畫面":::

3. 在接下來的頁面中，填入下列值：
     * **診斷設定名稱**：提供診斷設定的名稱。
     * **分類詳細資料**：選擇您想要監視的作業，並核取方塊以啟用這些作業的診斷功能。 診斷設定可以報告的作業為：
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
     * **目的地詳細資料**：選擇您要傳送記錄的位置。 您可以選取這三個選項的任意組合：
        - 傳送至 Log Analytics
        - 封存至儲存體帳戶
        - 串流至事件中樞

        如果您選取目的地時，系統可能會要求您填入其他詳細資料。  
    
4. 儲存新設定。 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="顯示 [診斷設定] 頁面和要新增之按鈕的螢幕擷取畫面":::

新的設定大約會在 10 分鐘內生效。 之後，記錄會出現在實例的 [**診斷設定**] 頁面上設定的目標中。 

## <a name="next-steps"></a>後續步驟

* 如需設定診斷的詳細資訊，請參閱[*收集並取用來自 Azure 資源的記錄資料*](../azure-monitor/platform/platform-logs-overview.md)。
* 如需 Azure 數位 Twins 計量的詳細資訊，請參閱[*疑難排解：使用 Azure 監視器來查看計量*](troubleshoot-metrics.md)。
* 若要瞭解如何為您的計量啟用警示，請參閱[*疑難排解：設定警示*](troubleshoot-alerts.md)。
