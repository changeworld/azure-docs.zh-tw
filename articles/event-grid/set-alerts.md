---
title: 設定 Azure 事件方格計量和活動記錄作業的警示
description: 本文說明如何建立 Azure 事件方格計量和活動記錄作業的警示。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 615ebef749be49822a09470eb0d47d8eb31b9a5a
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119135"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>設定 Azure 事件方格計量和活動記錄的警示
本文說明如何建立 Azure 事件方格計量和活動記錄作業的警示。 您可以針對 Azure 事件方格資源（主題和網域）的發佈和傳遞計量建立警示。 如需系統主題，請[使用 [**計量**] 頁面來建立警示](#create-alerts-using-the-metrics-page)。

## <a name="create-alerts-on-dead-lettered-events"></a>建立無效字母事件的警示
下列程式說明如何針對自訂主題的**死信事件**計量建立警示。 在此範例中，當主題的無效字母事件計數超過10時，電子郵件會傳送給 Azure 資源群組擁有者。 

1. 在主題的 [**事件方格主題**] 頁面上，選取左側功能表上的 [**警示**]，然後選取 [ **+ 新增警示規則**]。 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::
2. 在 [**建立警示規則**] 頁面上，確認已針對資源選取您的主題。 然後，按一下 [**選取條件**]。 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="警示頁面-選取條件":::    
3. 在 [**設定信號邏輯**] 頁面上，依照下列步驟執行：
    1. 選取度量或活動記錄專案。 在此範例中，已選取 [**死信] 事件**。 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="選取死信事件":::        
    2. 選取維度（選擇性）。 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="設定訊號邏輯":::        

        > [!NOTE]
        > 您可以選取 [ **+** **EventSubscriptionName** ] 按鈕，以指定用來篩選事件的事件訂用帳戶名稱。 
    3. 向下捲動。 在 [**警示邏輯**] 區段中，選取 [**運算子**]、[**匯總類型**] 並輸入**臨界值**，然後選取 [**完成**]。 在此範例中，當「不正確信件」事件計數大於10時，就會觸發警示。 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="警示邏輯":::                
4. 回到 [**建立警示規則**] 頁面，按一下 [**選取動作群組**]。

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="[選取動作群組] 按鈕":::
5. 選取工具列上的 [**建立動作群組**]，以建立新的動作群組。 您也可以選取現有的動作群組。        
6. 在 [**新增動作群組**] 頁面上，依照下列步驟執行：
    1. 輸入**動作群組的 [名稱**]。
    1. 輸入動作群組的**簡短名稱**。
    1. 選取您要在其中建立動作群組的**Azure 訂**用帳戶。
    1. 選取您要在其中建立動作群組的**Azure 資源群組**。
    1. 輸入**動作的 [名稱**]。 
    1. 選取 [**動作類型**]。 在此範例中，會選取 [**電子郵件 Azure Resource Manager 角色**]，特別是 [**擁有**者] 角色。 
    1. 選取 [確定] 以關閉頁面。 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="新增動作群組分頁":::                   
7. 回到 [**建立警示規則**] 頁面上，輸入警示規則的名稱，然後選取 [**建立警示規則**]。

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="警示規則名稱":::  
8. 現在，在主題的 [**警示**] 頁面上，您會看到一個連結來管理警示規則（如果尚未發出警示）。 如果有警示，請選取工具列上的 [**管理員警示規則**]。  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="管理警示":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>建立其他計量或活動記錄作業的警示
上一節說明了如何針對無效字母的事件建立警示。 針對其他計量或活動記錄作業建立警示的步驟很類似。 

例如，若要針對傳遞失敗事件建立警示，請在 [**設定信號邏輯**] 頁面上選取 [**傳遞失敗事件**]。 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="選取傳遞失敗事件":::


## <a name="create-alerts-using-the-metrics-page"></a>使用 [計量] 頁面建立警示
您也可以使用 [**計量**] 頁面來建立警示。 步驟很類似。 針對系統主題，您只能使用 [**計量**] 頁面來建立警示，因為 [**警示**] 頁面無法使用。 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="計量頁面-建立警示按鈕":::   
    

> [!NOTE]
> 本文並未涵蓋可用於建立警示的所有不同步驟和組合。 如需警示的總覽，請參閱[警示總覽](../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>後續步驟

* 如需事件傳遞和重試的相關資訊，請參閱[事件格線訊息傳遞與重試](delivery-and-retry.md)。
* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
