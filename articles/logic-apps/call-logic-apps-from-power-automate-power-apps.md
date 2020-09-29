---
title: 從 Power Automate 和 Power Apps 呼叫邏輯應用程式
description: 藉由將邏輯應用程式匯出為連接器，從 Microsoft Power Automate 流程呼叫邏輯應用程式。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 09/28/2020
ms.openlocfilehash: 99b13f96bb4676e87b96002fd183376a678d070b
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91492404"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>從 Power Automate 和 Power Apps 呼叫邏輯應用程式

若要從 Microsoft Power Automate 和 Microsoft Power Apps 呼叫您的邏輯應用程式，您可以將邏輯應用程式匯出為連接器。 當您在 Power Automate 或 Power Apps 環境中將邏輯應用程式公開為自訂連接器時，您就可以從流程中呼叫邏輯應用程式。

如果您想要改為從 Power Automate 或電源遷移至 Logic Apps 的流程，請參閱 [從 Power Automate 匯出流程，並將其部署至 Azure Logic Apps](export-from-microsoft-flow-logic-app-template.md)。

> [!NOTE]
> 並非所有 Power Automate 連接器都可在 Azure Logic Apps 中使用。 您只能遷移 Azure Logic Apps 中具有對等連接器的 Power Automate 流程。 例如，按鈕觸發程式、核准連接器和通知連接器都是 Power Automate 特有的。 目前，Power Automate 中以 OpenAPI 為基礎的流程不支援做為邏輯應用程式範本匯出和部署。
>
> * 若要找出哪些 Power Automate 連接器沒有 Logic Apps 對等專案，請參閱 [Power Automate 連接器](/connectors/connector-reference/connector-reference-powerautomate-connectors)。
>
> * 若要找出哪些 Logic Apps 連接器沒有 Power Automate 對等專案，請參閱 [Logic Apps 連接器](/connectors/connector-reference/connector-reference-powerautomate-connectors)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Power Automate 或 Power Apps 授權。

* 具有要匯出之要求觸發程式的邏輯應用程式。

* 您要從中呼叫邏輯應用程式 Power Automate 或 Power Apps 的流程。

## <a name="export-your-logic-app-as-a-custom-connector"></a>將邏輯應用程式匯出為自訂連接器

在您可以從 Power Automate 或 Power Apps 呼叫邏輯應用程式之前，必須先將邏輯應用程式匯出為自訂連接器。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站搜尋方塊中，輸入 `Logic Apps` 。 在結果中，選取 [ **服務**] 底下的 [ **Logic Apps**]。

1. 選取您要匯出的邏輯應用程式。

1. 從邏輯應用程式的功能表中選取 [ **匯出**]。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Azure 入口網站中邏輯應用程式頁面的螢幕擷取畫面，其中顯示已選取 [匯出] 按鈕的功能表。":::

1. 在 [ **匯出** ] 窗格的 [ **名稱**] 中，輸入邏輯應用程式的自訂連接器名稱。 從 [ **環境** ] 清單中，選取您要用來呼叫邏輯應用程式的 Power Automate 或 Power Apps 環境。 完成後，選取 [確定]。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="Azure 入口網站中邏輯應用程式頁面的螢幕擷取畫面，其中顯示已選取 [匯出] 按鈕的功能表。":::

1. 若要確認您的邏輯應用程式已成功匯出，請檢查 [通知] 窗格。

### <a name="exporting-errors"></a>匯出錯誤

以下是當您將邏輯應用程式匯出為自訂連接器和其建議解決方案時可能會發生的錯誤：

* **無法取得環境。請確認您的帳戶已設定 Power Automate，然後再試一次。**：請檢查您的 Azure 帳戶是否有 Power Automate 方案。

* **無法匯出目前的邏輯應用程式。若要匯出，請選取具有要求觸發程式的邏輯應用程式。**：檢查邏輯應用程式的開頭是否為 [要求觸發](./logic-apps-workflow-actions-triggers.md#request-trigger)程式。

## <a name="connect-to-your-logic-app-from-power-automate"></a>從 Power Automate 連接到您的邏輯應用程式

若要連接到您隨 Power Automate 流程匯出的邏輯應用程式：

1. 登入 [Power Automate](https://flow.microsoft.com)。

1. 從 **Power Automate** 首頁] 功能表中，選取 [ **我的流程**]。

1. 在 [ **流程** ] 頁面上，選取您要連接到邏輯應用程式的流程。

1. 從 [流程] 頁面的功能表中，選取 [ **編輯**]。

1. 在流程編輯器中，選取 [ **&#43; 新的步驟**]。

1. 在 [ **選擇動作**] 下的 [搜尋] 方塊中，輸入邏輯應用程式連接器的名稱。 （選擇性）若要只顯示環境中的自訂連接器，請選取 [ **自訂** ] 索引標籤來篩選結果。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Azure 入口網站中邏輯應用程式頁面的螢幕擷取畫面，其中顯示已選取 [匯出] 按鈕的功能表。":::

1. 選取您要使用邏輯應用程式連接器執行的動作。 

1. 提供動作傳遞給邏輯應用程式連接器的資訊。

1. 若要儲存您的變更，請在 [Power Automate 編輯器] 功能表上選取 [ **儲存**]。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Logic Apps 服務中，尋找您匯出的邏輯應用程式。

1. 確認您的邏輯應用程式以您預期的 Power Automate 流程方式運作。

## <a name="delete-logic-app-connector-from-power-automate"></a>從 Power Automate 刪除邏輯應用程式連接器

1. 登入 [Power Automate](https://flow.microsoft.com)。

1. 在 **Power Automate** 首頁上，選取功能表中的 [ **資料** &gt; **自訂連接器** ]。

1. 在清單中，尋找您的自訂連接器，然後選取省略號 (**...**) 按鈕 &gt; **刪除**]。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Azure 入口網站中邏輯應用程式頁面的螢幕擷取畫面，其中顯示已選取 [匯出] 按鈕的功能表。":::

1. 若要確認刪除，請選取 **[確定]**。

## <a name="connect-to-your-logic-app-from-power-apps"></a>從 Power Apps 連接到您的邏輯應用程式

若要連接到您隨 Power Apps 流程匯出的邏輯應用程式：

1. 登入 [Power Apps](https://powerapps.microsoft.com/)。

1. 在 **Power Apps** 首頁上，選取功能表中的 [ **流程** ]。

1. 在 [ **流程** ] 頁面上，選取您要連接到邏輯應用程式的流程。

1. 在流程的頁面上，選取流程功能表中的 [ **編輯** ]。

1. 在流程編輯器中，選取 [ **&#43; 新的步驟** ] 按鈕。

1. 在新步驟的 **[選擇動作** ] 下，于 [搜尋] 方塊中輸入邏輯應用程式連接器的名稱。 （選擇性）依 [ **自訂** ] 索引標籤篩選結果，只查看環境中的自訂連接器。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Azure 入口網站中邏輯應用程式頁面的螢幕擷取畫面，其中顯示已選取 [匯出] 按鈕的功能表。":::

1. 選取您要對連接器採取的動作。 

1. 設定您的動作傳遞給邏輯應用程式連接器的資訊。

1. 在 [Power Apps 編輯器] 功能表中，選取 [ **儲存** ] 以儲存您的變更。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Logic Apps 服務中，尋找您匯出的邏輯應用程式。

1. 確認您的邏輯應用程式在您的 Power Apps 流程中如預期運作。

## <a name="delete-logic-app-connector-from-power-apps"></a>從 Power Apps 刪除邏輯應用程式連接器

1. 登入 [Power Apps](https://powerapps.microsoft.com)。

1. 在 **Power Apps** 首頁上，選取功能表中的 [ **資料** &gt; **自訂連接器** ]。

1. 在清單中，尋找您的自訂連接器，然後選取省略號 (**...**) 按鈕 &gt; **刪除**]。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Azure 入口網站中邏輯應用程式頁面的螢幕擷取畫面，其中顯示已選取 [匯出] 按鈕的功能表。":::

1. 若要確認刪除，請選取 **[確定]**。
