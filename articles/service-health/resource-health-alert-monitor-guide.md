---
title: 使用 Azure 入口網站建立資源健康狀態警示
description: 使用 Azure 入口網站建立警示，以在您的 Azure 資源變成無法使用時通知您。
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: 127aad1691f7bb6b6c64332eefde734a809ab75f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540671"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>使用 Azure 入口網站設定資源健康情況警示

本文說明如何使用 Azure 入口網站設定資源健康狀態通知的活動記錄警示。

Azure 資源健康狀態會隨時通知您 Azure 資源目前和過去的健康狀態。 Azure 資源健康狀態警示會在這些資源的健康狀態變更時，以近乎即時的方式通知您。 以程式設計方式建立資源健康狀態警示，讓使用者能夠建立並自訂大量警示。

資源健康狀態通知會儲存在「 [Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md) 」中，並提供儲存在活動記錄中的大量資訊，而且有個別的使用者介面可讓您更輕鬆地查看及設定資源健康狀態通知的警示。
當 Azure 資源將資源健康狀態通知傳送至您的 Azure 訂用帳戶時，您會收到警示。 您可以針對下列設定警示：

* 受影響的訂閱。
* 資源 (s) 類型 (s) 受影響。
*  (s 的資源群組) 受到影響。
* 受影響的資源 () 。
* 資源 () 受影響的事件狀態 (s) 。
* 資源 (s) 受影響的狀態。
*  (s 的原因) 類型 (s) 受影響的資源 (。

您也可以設定應傳送警示的對象：

* 選取現有的動作群組。
* 建立新動作群組 (可用於未來的警示)。

若要深入了解動作群組，請參閱[建立及管理動作群組](../azure-monitor/platform/action-groups.md)。

如需有關如何使用 Azure Resource Manager 範本來設定資源健康狀態通知警示的詳細資訊，請參閱 [Resource Manager 範本](./resource-health-alert-arm-template-guide.md)。
使用 Azure 入口網站資源健康狀態警示

## <a name="resource-health-alert-using-azure-portal"></a>使用 Azure 入口網站資源健康狀態警示

1. 在 Azure [入口網站](https://portal.azure.com/)中，選取 [ **服務健康狀態**]。

    ![服務健全狀況選取專案](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. 在 [ **資源健康狀態** ] 區段中，選取 [ **服務健康狀態**]。
3. 選取 [ **新增資源健康狀態警示** ]，然後填入欄位。
4. 在 [警示目標] 底下，選取您要收到警示的 **訂**用帳戶、 **資源類型**、 **資源群組** 和 **資源** 。

    ![目標選取範圍選取範圍](./media/resource-health-alert-monitor-guide/alert-target.png)

5. 在 [警示條件] 底下選取：
    1. 您希望收到警示的 **事件狀態** 。 事件的嚴重性層級：作用中、已解決、進行中、已更新
    2. 您希望收到警示的 **資源狀態** 。 事件的資源狀態：可用、無法使用、未知、已降級
    3. 您希望收到警示的 **原因類型** 。 事件的原因：啟動平臺，使用者起始的 ![ 警示條件選取狀況選取範圍](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. 在 [定義警示詳細資料] 下，提供下列詳細資料：
    1. **警示規則名稱**：新警示規則的名稱。
    2. **描述**：新警示規則的描述。
    3. **將警示儲存到資源群組**：選取您儲存此新規則的資源群組。
7. 在 [動作群組] 下，從下拉式功能表中指定您想要指派給此新警示規則的動作群組。 或者，[建立新的動作群組](../azure-monitor/platform/action-groups.md)並指派給新規則。 若要建立新的群組，請選取 [+ **新增群組**]。
8. 若要在建立群組之後啟用規則，請選取 [在建立時啟用規則] 選項的 [是] 。
9. 選取 [建立警示規則]。

活動記錄的新警示規則隨即建立，並會在視窗右上角顯示確認訊息。
您可以啟用、停用、編輯或刪除規則。 深入瞭解 [如何管理活動記錄規則](../azure-monitor/platform/alerts-activity-log.md#view-and-manage-in-the-azure-portal)。

## <a name="next-steps"></a>後續步驟

深入了解資源健康狀態：

* [Azure 資源健康狀態總覽](Resource-health-overview.md)
* [可透過 Azure 資源健康狀態使用的資源類型和健康情況檢查](resource-health-checks-resource-types.md)

建立服務健康狀態警示：

* [設定適用於服務健康情況的警示](./alerts-activity-log-service-notifications-portal.md) 
* [Azure 活動記錄事件結構描述](../azure-monitor/platform/activity-log-schema.md)
* [使用 Resource Manager 範本設定資源健康狀態警示](./resource-health-alert-arm-template-guide.md)
