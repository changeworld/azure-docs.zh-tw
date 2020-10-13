---
title: 本快速入門 - 在 Azure Logic Apps 中使用 Azure 通訊服務來傳送 SMS 訊息
titleSuffix: An Azure Communication Services quickstart
description: 在本快速入門中，了解如何在 Azure Logic Apps 工作流程中，使用 Azure 通訊服務連接器傳送 SMS 訊息。
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ed9af178c10d033806b05f73372a0642bb28cab4
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801631"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>快速入門：在 Azure Logic Apps 中使用 Azure 通訊服務來傳送 SMS 訊息

藉由使用 [Azure 通訊服務 SMS](../../overview.md) 連接器和 [Azure Logic Apps](../../../logic-apps/logic-apps-overview.md)，您可以建立可傳送 SMS 訊息的自動化工作流程或「邏輯應用程式」。 本快速入門示範如何自動傳送文字訊息以回應觸發事件，這是邏輯應用程式工作流程中的第一個步驟。 觸發事件可以是內送的電子郵件訊息、週期排程、[Azure Event Grid](../../../event-grid/overview.md) 資源事件，或是 [Azure Logic Apps 所支援的任何其他觸發程序](/connectors/connector-reference/connector-reference-logicapps-connectors)。

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="此螢幕擷取畫面顯示已開放給邏輯應用程式設計工具的 Azure 入口網站，並顯示範例邏輯應用程式，其會使用適用於 Azure 通訊服務連接器的 [傳送 SMS] 動作。":::

雖然本快速入門著重於使用連接器來回應觸發程序，但您也可以使用連接器來回應其他動作，也就是在工作流程中遵循觸發程序的步驟。 如果您不熟悉邏輯應用程式，請在開始之前檢閱[什麼是 Azure Logic Apps](../../../logic-apps/logic-apps-overview.md)。

> [!NOTE]
> 完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。

## <a name="prerequisites"></a>必要條件

- 包含作用中訂用帳戶的 Azure 帳戶，或[建立免費 Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 作用中的 Azure 通訊服務資源，或[建立通訊服務資源](../create-communication-resource.md)。

- 作用中的 Logic Apps 資源 (邏輯應用程式)，或[建立空白的邏輯應用程式，但搭配您想要使用的觸發程序](../../../logic-apps/quickstart-create-first-logic-app-workflow.md)。 目前，Azure 通訊服務 SMS 連接器只會提供動作，因此您的邏輯應用程式至少需要一個觸發程序。

  本快速入門會使用**新的電子郵件送達時**觸發程序，其可與 [Office 365 Outlook 連接器](/connectors/office365/)搭配使用。

- 已啟用 SMS 的電話號碼，或[取得電話號碼](./get-phone-number.md)。

## <a name="add-an-sms-action"></a>新增 SMS 動作

若要在工作流程中使用 Azure 通訊服務 SMS 連接器，新增 [傳送 SMS] 動作做為新步驟，請在邏輯應用程式設計工具中開啟邏輯應用程式工作流程時，在 [Azure 入口網站](https://portal.azure.com) 中遵循下列步驟：

1. 在設計工具上，於您要新增動作的步驟底下，選取 [新增步驟]。 或者，若要在步驟之間新增動作，請將指標移到這些步驟之間的箭號上、選取加號 ( **+** )，然後選取 [新增動作]。

1. 在 [選擇作業] 搜尋方塊中，輸入 `Azure Communication Services`。 從動作清單中，選取 [傳送 SMS]。

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="此螢幕擷取畫面顯示已開放給邏輯應用程式設計工具的 Azure 入口網站，並顯示範例邏輯應用程式，其會使用適用於 Azure 通訊服務連接器的 [傳送 SMS] 動作。":::

1. 現在，建立與通訊服務資源的連線。

   1. 提供連線的名稱。

   1. 選取 Azure 通訊服務資源。

   1. 選取 [建立]  。

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="此螢幕擷取畫面顯示已開放給邏輯應用程式設計工具的 Azure 入口網站，並顯示範例邏輯應用程式，其會使用適用於 Azure 通訊服務連接器的 [傳送 SMS] 動作。":::

1. 在 [傳送 SMS] 動作中，提供下列資訊： 

   * 來源和目的地電話號碼。 基於測試目的，您可以使用自己的電話號碼做為目的地電話號碼。

   * 您想要傳送的訊息內容，例如 "Hello from Logic Apps1"。

   以下是 [傳送 SMS] 動作與範例資訊：

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="此螢幕擷取畫面顯示已開放給邏輯應用程式設計工具的 Azure 入口網站，並顯示範例邏輯應用程式，其會使用適用於 Azure 通訊服務連接器的 [傳送 SMS] 動作。":::

1. 完成時，請在設計工具的工具列上，選取 [儲存]。

接著，執行邏輯應用程式進行測試。

## <a name="test-your-logic-app"></a>測試應用程式邏輯

若要手動啟動邏輯應用程式，請在設計工具的工具列上選取 [執行]。 或者，您可以等候邏輯應用程式觸發。 在這兩種情況下，邏輯應用程式都應該將 SMS 訊息傳送至您指定的目的地電話號碼。 如需執行邏輯應用程式的詳細資訊，請參閱[如何執行邏輯應用程式](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app)

## <a name="clean-up-resources"></a>清除資源

若要移除通訊服務訂用帳戶，請刪除通訊服務資源或資源群組。 刪除資源群組也會刪除該群組中的任何其他資源。 如需詳細資訊，請檢閱[如何清除通訊服務資源](../create-communication-resource.md#clean-up-resources)。

若要清除邏輯應用程式工作流程和相關資源，請檢閱[如何清除 Logic Apps 資源](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources)。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Azure Logic Apps 和 Azure 通訊服務來傳送 SMS 訊息。 若要深入了解，請繼續訂閱 SMS 事件：

> [!div class="nextstepaction"]
> [訂閱 SMS 事件](./handle-sms-events.md)

如需 Azure 通訊服務中 SMS 的詳細資訊，請參閱下列文章：

- [SMS 概念](../../concepts/telephony-sms/concepts.md)
- [規劃您的電話語音和 SMS 解決方案](../../concepts/telephony-sms/plan-solution.md)
- [SMS SDK](../../concepts/telephony-sms/sdk-features.md)
