---
title: 設定 Azure 事件方格計量和活動記錄作業的警示
description: 本文說明如何在 Azure 事件方格計量和活動記錄作業上建立警示。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 615ebef749be49822a09470eb0d47d8eb31b9a5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86119135"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>設定 Azure 事件方格計量和活動記錄的警示
本文說明如何在 Azure 事件方格計量和活動記錄作業上建立警示。 您可以為 Azure 事件方格資源的發佈和傳遞計量建立警示， (主題和網域) 。 若為系統主題，請 [使用 [ **計量** ] 頁面來建立警示](#create-alerts-using-the-metrics-page)。

## <a name="create-alerts-on-dead-lettered-events"></a>建立死信事件的警示
下列程式示範如何針對自訂主題的「 **死信事件** 」度量建立警示。 在此範例中，當主題的無效信件事件計數超過10時，系統會將電子郵件傳送給 Azure 資源群組擁有者。 

1. 在主題的 **事件方格主題** 頁面上，選取左側功能表上的 [ **警示** ]，然後選取 [ **+ 新增警示規則**]。 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::
2. 在 [ **建立警示規則** ] 頁面上，確認已為資源選取您的主題。 然後，按一下 [ **選取條件**]。 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::    
3. 在 [ **設定信號邏輯** ] 頁面上，依照下列步驟執行：
    1. 選取度量或活動記錄專案。 在此範例中，已選取 [ **死信事件** ]。 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::        
    2. 選取 [維度] (選擇性) 。 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::        

        > [!NOTE]
        > 您可以選取 [ **+** **EventSubscriptionName** ] 按鈕，以指定要用來篩選事件的事件訂用帳戶名稱。 
    3. 向下捲動。 在 [ **警示邏輯** ] 區段中，選取 [ **運算子**]、[ **匯總類型**] 並輸入 **臨界值**，然後選取 [ **完成**]。 在此範例中，當總無效信件事件計數大於10時，就會觸發警示。 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::                
4. 回到 [ **建立警示規則** ] 頁面，按一下 [ **選取動作群組**]。

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::
5. 選取工具列上的 [ **建立動作群組** ]，以建立新的動作群組。 您也可以選取現有的動作群組。        
6. 在 [ **新增動作群組** ] 頁面上，依照下列步驟執行：
    1. 輸入 **動作群組的名稱**。
    1. 輸入動作群組的**簡短名稱**。
    1. 選取您要在其中建立動作群組的 **Azure 訂** 用帳戶。
    1. 選取您要在其中建立動作群組的 **Azure 資源群組** 。
    1. 輸入 **動作的名稱**。 
    1. 選取 **動作類型**。 在此範例中，已選取 [ **電子郵件] Azure Resource Manager 角色** ，特別是 [ **擁有** 者] 角色。 
    1. 選取 [確定] 以關閉頁面。 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::                   
7. 回到 [ **建立警示規則** ] 頁面，輸入警示規則的名稱，然後選取 [ **建立警示規則**]。

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::  
8. 現在，在主題的 [ **警示** ] 頁面上，如果沒有任何警示，您會看到用來管理警示規則的連結。 如果有警示，請選取工具列上的 [ **管理員警示規則** ]。  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>建立其他計量或活動記錄作業的警示
上一節說明如何建立死信事件的警示。 針對其他計量或活動記錄作業建立警示的步驟很類似。 

例如，若要建立傳遞失敗事件的警示，請選取 [**設定信號邏輯**] 頁面上的 [**傳遞失敗事件**]。 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::


## <a name="create-alerts-using-the-metrics-page"></a>使用 [計量] 頁面建立警示
您也可以使用 [ **計量** ] 頁面來建立警示。 步驟很類似。 針對系統主題，您只能使用 [ **計量** ] 頁面來建立警示，因為無法使用 [ **警示** ] 頁面。 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="[警示] 頁面 - [新增警示規則] 按鈕":::   
    

> [!NOTE]
> 本文並未涵蓋您可用來建立警示的所有不同步驟和組合。 如需警示的總覽，請參閱 [警示總覽](../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>接下來的步驟

* 如需事件傳遞和重試的相關資訊，請參閱[事件格線訊息傳遞與重試](delivery-and-retry.md)。
* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
