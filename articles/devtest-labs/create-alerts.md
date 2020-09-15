---
title: 在 Azure DevTest Labs 中建立實驗室的活動記錄警示
description: 本文提供的步驟可讓您在 Azure DevTest Labs 中建立實驗室的活動記錄警示。
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: d5886ea26ddbeb07efc23d61d3197860620eebf3
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526352"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中建立實驗室的活動記錄警示
本文說明如何在 Azure DevTest Labs (中建立實驗室的活動記錄警示，例如：建立 VM 時或刪除 VM 時) 。

## <a name="create-alerts"></a>建立警示
在此範例中，您會使用傳送電子郵件給訂用帳戶擁有者的動作，針對實驗室中的所有系統管理作業建立警示。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站的搜尋列中，輸入 **monitor**，然後從結果清單中選取 [ **監視** ]。 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="搜尋監視":::        
1. 在左側功能表上選取 [ **警示** ]，然後在工具列上選取 [ **新增警示規則** ]。 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="警示頁面":::    
1. 在 [ **建立警示規則** ] 頁面上，按一下 [ **選取資源**]。 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="選取警示的資源":::        
1. 選取 [**依資源類型篩選**的**DevTest Labs** ]，在清單中選取您的實驗室，然後選取 [**完成**]。

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="選取您的實驗室作為資源":::
1. 回到 [ **建立警示規則** ] 頁面，按一下 [ **選取條件**]。 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="選取條件連結":::    
1. 在 [ **設定信號邏輯** ] 頁面上，選取 DevTest Labs 所支援的信號。 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="選取信號":::
1. 依 **事件層級** 篩選 (詳細資訊、資訊、警告、錯誤、重大、所有) 、 **狀態** (失敗、已啟動、成功) 以及起始事件的 **人員** 。 
1. 選取 [ **完成** ] 以完成條件設定。 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="設定信號邏輯-完成":::
1. 您已針對範圍 (lab) 和警示的條件來指定。 現在，您必須指定動作群組，其中包含符合條件時要執行的動作。 回到 [ **建立警示規則** ] 頁面，選擇 [ **選取動作群組**]。 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="選取動作群組連結":::
1. 選取工具列上的 [ **建立動作群組** ] 連結。 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="建立動作群組連結":::
1. 在 [ **新增動作群組** ] 頁面上，依照下列步驟執行：
    1. 輸入動作群組的**名稱**。
    1. 輸入動作群組的**簡短名稱**。 
    1. 選取您要在其中建立警示的 **資源群組** 。 
    1. 輸入 **動作的名稱**。 
    1. 在此範例中選取 **動作類型** (， **電子郵件 Azure Resource Manager 角色**) 。 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="新增動作群組分頁":::
    1. 在 [ **電子郵件 Azure Resource Manager 角色** ] 頁面上，選取角色。 在此範例中，它是 **擁有**者。 然後選取 [確定]  。 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="選取職責":::            
    1. 在 [新增動作群組] 頁面上，選取 [確定]。 
1. 現在，在 [ **建立警示規則** ] 頁面上，輸入警示規則的名稱，然後選取 **[確定]**。 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="建立警示規則-完成":::

## <a name="view-alerts"></a>檢視警示 
1. 您將會在此範例 **) 的所有** 系統管理作業 (看到警示。 警示可能需要一段時間才會出現。 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="螢幕擷取畫面會在儀表板中顯示警示。":::
1. 如果您在資料行中選取 [數位] (例如： **警示總數**) ，您會看到引發的警示。 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="所有警示":::
1. 如果您選取警示，您會看到它的詳細資料。 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="警示詳細資料":::
1. 在此範例中，您也會收到一封包含內容的電子郵件，如下列範例所示： 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="警示電子郵件":::

## <a name="next-steps"></a>後續步驟
- 若要深入瞭解如何使用不同的動作類型建立動作群組，請參閱 [在 Azure 入口網站中建立和管理動作群組](../azure-monitor/platform/action-groups.md)。
- 若要深入瞭解活動記錄，請參閱  [Azure 活動記錄](../azure-monitor/platform/activity-log.md)。
- 若要瞭解如何設定活動記錄警示，請參閱 [活動記錄的警示](../azure-monitor/platform/activity-log-alerts.md)。

