---
title: Azure 事件方格-啟用主題的診斷記錄
description: 本文提供逐步指示，說明如何啟用 Azure 事件方格主題的診斷記錄。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960499"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Azure 事件方格主題的診斷記錄
診斷設定可讓「事件方格」使用者在下列其中一個位置中，捕捉及查看發行和傳遞失敗記錄： Azure 儲存體帳戶、事件中樞或 Log Analytics 工作區。 本文提供逐步指示，說明如何啟用事件方格主題的診斷記錄。

## <a name="prerequisites"></a>必要條件

- 已布建的事件方格主題
- 用於捕獲診斷記錄的已布建目的地。 它可以是下列其中一個目的地：
    - Azure 儲存體帳戶
    - 事件中樞
    - Log Analytics 工作區


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>啟用主題診斷記錄的步驟

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽至您想要啟用診斷記錄設定的事件方格主題。 
3. 在左側功能表中，選取 [**監視**] 底下的 [**診斷設定**]。
4. 在 [**診斷設定**] 頁面上，選取 [**新增診斷設定**]。 
    
    ![[新增診斷設定] 按鈕](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 指定診斷設定的**名稱**。 

    ![診斷設定-名稱](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. 啟用記錄檔的一個或多個 capture 目的地，然後選取先前建立的 capture 資源加以設定。 
    - 如果您選取 [封存**至儲存體帳戶**]，請選取 [**儲存體帳戶-設定**]，然後在您的 Azure 訂用帳戶中選取儲存體帳戶。 

        ![封存至 Azure 儲存體帳戶](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - 如果您選取 [**串流至事件中樞**]，請選取 [**事件中樞-設定**]，然後選取 [事件中樞命名空間]、[事件中樞] 和 [存取原則]。 
        ![串流到事件中樞](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - 如果您選取 [**傳送至 Log analytics**]，請選取 [log analytics 工作區]。
        ![傳送至 Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. 在 [**記錄**檔] 區段中選取**DeliveryFailures**和**PublishFailures**選項。 
    ![選取失敗](./media/enable-diagnostic-logs-topic/log-failures.png)
8. 選取 [儲存]。 選取右上角的 [ **X** ] 以關閉頁面。 
9. 現在，回到 [**診斷設定**] 頁面，確認您在 [**診斷設定**] 資料表中看到新的專案。 
    ![清單中的診斷設定](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     您也可以啟用主題所有計量的收集。 

## <a name="next-steps"></a>後續步驟
如果您需要更多協助，請在[Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-eventgrid)中張貼您的問題，或開啟[支援票證](https://azure.microsoft.com/support/options/)。 
