---
title: 使用模擬資料測試邏輯應用程式
description: 使用模擬資料設定靜態結果來測試邏輯應用程式，而不會影響生產環境
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 711d753203aeaeba50cea692053a37fcab2e9c7b
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027698"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>藉由設定靜態結果來測試具有 mock 資料的邏輯應用程式

測試您的邏輯應用程式時，您可能會因為各種原因而無法實際呼叫或存取應用程式、服務和系統。 通常在這些情況下，您可能必須執行不同的條件路徑、強制執行錯誤、提供特定的訊息回應主體，或甚至嘗試略過一些步驟。 藉由在邏輯應用程式中設定動作的靜態結果，您可以模擬該動作的輸出資料。 針對動作啟用靜態結果不會執行動作，而會改為傳回模擬資料。

例如，如果您設定 Outlook 365 傳送郵件動作的靜態結果，Logic Apps 引擎只會傳回您指定為靜態結果的 mock 資料，而不是呼叫 Outlook 並傳送電子郵件。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先<a href="https://azure.microsoft.com/free/" target="_blank">註冊免費的 Azure 帳戶</a>。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要設定靜態結果的邏輯應用程式

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>設定靜態結果

1. 如果您還沒有在 [Azure 入口網站](https://portal.azure.com)中，請在 Logic Apps 設計工具中開啟邏輯應用程式。

1. 在您想要設定靜態結果的動作上，依照下列步驟執行： 

   1. 在動作的右上角選擇省略號 ( *...* ) 按鈕，然後選取 [ **靜態結果** ]，例如：

      ![選取 [靜態結果] > [啟用靜態結果]](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. 選擇 [ **啟用靜態結果** ]。 針對必要的 ( * ) 屬性，請指定您想要傳回給動作回應的 mock 輸出值。

      例如，以下是 HTTP 動作的必要屬性：

      | 屬性 | 描述 |
      |----------|-------------|
      | **狀態** | 要傳回的動作狀態 |
      | **狀態碼** | 要傳回的特定狀態碼 |
      | **標頭** | 要傳回的標頭內容 |
      |||

      ![選取 [啟用靜態結果]](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      若要 JavaScript 物件標記法 (JSON) 格式輸入 mock 資料，請選擇 [ **切換至 Json 模式** ![ ] (選擇 [切換至 json 模式] ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) 。

   1. 針對選擇性屬性，開啟 [ **選取選擇性欄位** ] 清單，然後選取您想要模擬的屬性。

      ![選取選擇性屬性](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 當您準備好要儲存時，請選擇 [ **完成** ]。

   在動作的右上角，標題列現在會顯示 (![ 靜態結果) 圖示的測試小燒杯圖示 ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ，表示您已啟用靜態結果。

   ![顯示啟用的靜態結果圖示](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   若要尋找使用模擬資料的先前回合，請參閱本主題稍後的 [尋找使用靜態結果的執行](#find-runs-mock-data) 。

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>重複使用先前的輸出

如果您的邏輯應用程式具有先前執行的輸出，您可以重複使用作為 mock 輸出，您可以複製並貼上該執行的輸出。

1. 如果您還沒有在 [Azure 入口網站](https://portal.azure.com)中，請在 Logic Apps 設計工具中開啟邏輯應用程式。

1. 在邏輯應用程式的主功能表上，選取 **[總覽** ]。

1. 在 [ **執行歷程記錄** ] 區段中，選取您要的邏輯應用程式執行。

1. 在邏輯應用程式的工作流程中，尋找並展開具有您想要之輸出的動作。

1. 選擇 [ **顯示原始輸出** ] 連結。

1. 複製完整的 JavaScript 物件標記法 (JSON) 物件或您想要使用的特定子區段（例如，[輸出] 區段，或甚至只是 [標頭] 區段）。

1. 遵循在 [設定靜態結果](#set-up-static-results)中為您的動作開啟 **靜態結果** 方塊的步驟。

1. 在 [ **靜態結果** ] 方塊開啟後，選擇任一步驟：

   * 若要貼上完整的 JSON 物件，請選擇 [ **切換至 Json 模式** ![ ] (選擇 [切換至 json 模式] ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) ：

     ![針對完成的物件選擇 [切換至 JSON 模式]](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * 若要只貼上 JSON 區段，請在該區段的標籤旁邊選擇 [ **切換至 Json 模式** ] 區段，例如：

     ![針對輸出選擇 [切換至 JSON 模式]](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. 在 JSON 編輯器中，貼上您先前複製的 JSON。

   ![JSON 模式](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 完成之後，選擇 [完成]。 或者，若要返回設計工具，請選擇 [ **切換編輯器模式** ] (![ 選擇 [切換編輯器模式] ](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)) 。

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>尋找使用靜態結果的執行

邏輯應用程式的執行歷程記錄會識別動作使用靜態結果的執行。 若要尋找這些執行，請遵循下列步驟：

1. 在邏輯應用程式的主功能表上，選取 **[總覽** ]。 

1. 在右窗格的 [ **執行歷程記錄** ] 底下，尋找 [ **靜態結果** ] 資料行。 

   包含結果之動作的任何執行都會將 **靜態結果** 資料行集設定為 **啟用** ，例如：

   ![執行歷程記錄-靜態結果資料行](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 若要查看使用靜態結果的動作，請選取 [ **靜態結果** ] 資料行設為 [ **已啟用** ] 時要執行的 [執行]。

   使用靜態結果的動作會顯示靜態結果的測試小燒杯 (![ 圖示 ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) 圖示，例如：

   ![執行歷程記錄-使用靜態結果的動作](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>停用靜態結果

關閉靜態結果不會擲回您上次設定的值。 因此，當您下一次開啟靜態結果時，您可以繼續使用先前的值。

1. 尋找您想要停用靜態輸出的動作。 在動作的右上角，選擇 [測試小燒杯] 圖示 (![ 靜態結果) 的圖示 ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) 。

   ![螢幕擷取畫面顯示 H T T P 動作，您可以在其中選取測試小燒杯圖示。](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. 選擇 [  。

   ![螢幕擷取畫面：顯示您可以選取的 [停用靜態結果] 選項。](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>參考

如需基礎工作流程定義中此設定的詳細資訊，請參閱 [靜態結果-工作流程定義語言](../logic-apps/logic-apps-workflow-definition-language.md#static-results) 和 Runtimeconfiguration.concurrency 的架構參考 [。 staticResult-執行時間設定](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)