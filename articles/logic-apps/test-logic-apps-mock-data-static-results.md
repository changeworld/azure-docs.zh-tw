---
title: 使用模擬資料測試邏輯應用程式
description: 設置靜態結果，以便使用類比資料測試邏輯應用，而不會影響生產環境
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790273"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>通過設置靜態結果，使用類比資料測試邏輯應用

測試邏輯應用時，由於各種原因，您可能還沒有準備好實際調用或訪問應用、服務和系統。 通常在這些情況下，您可能需要運行不同的條件路徑、強制錯誤、提供特定的消息回應正文，甚至嘗試跳過某些步驟。 通過在邏輯應用中為操作設置靜態結果，可以類比該操作的輸出資料。 在操作上啟用靜態結果不會運行操作，而是返回類比資料。

例如，如果為 Outlook 365 發送郵件操作設置靜態結果，邏輯應用引擎只會返回您指定為靜態結果的類比資料，而不是調用 Outlook 併發送電子郵件。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先<a href="https://azure.microsoft.com/free/" target="_blank">註冊免費的 Azure 帳戶</a>。

* [有關如何創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 要設置靜態結果的邏輯應用

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>設置靜態結果

1. 如果尚未在[Azure 門戶](https://portal.azure.com)中，請在邏輯應用設計器中打開邏輯應用。

1. 在要設置靜態結果的操作中，請按照以下步驟操作： 

   1. 在操作的右上角，選擇橢圓 *（...）* 按鈕，然後選擇**靜態結果**，例如：

      ![選擇"靜態結果">"啟用靜態結果"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. 選擇**啟用靜態結果**。 對於所需的 （*） 屬性，請指定要為操作的回應返回的類比輸出值。

      例如，以下是 HTTP 操作所需的屬性：

      | 屬性 | 描述 |
      |----------|-------------|
      | **狀態** | 要返回的操作狀態 |
      | **狀態碼** | 要返回的特定狀態碼 |
      | **標頭** | 要返回的標頭內容 |
      |||

      ![選擇"啟用靜態結果"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      要以 JavaScript 物件標記法 （JSON） 格式輸入類比資料，請選擇 **"切換到 JSON 模式**"（![選擇"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)切換到 JSON 模式"）。

   1. 對於可選屬性，打開 **"選擇可選欄位**"清單，然後選擇要類比的屬性。

      ![選擇可選屬性](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 準備好保存時，請選擇 **"完成**"。

   在操作的右上角，標題列現在顯示一個測試燒杯圖示（![靜態結果](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)圖示），表示您已啟用靜態結果。

   ![顯示已啟用靜態結果的圖示](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   要查找以前使用類比資料的運行，請參閱在本主題稍後使用[靜態結果查找運行](#find-runs-mock-data)。

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>重用以前的輸出

如果邏輯應用以前運行的輸出可以作為類比輸出重用，則可以複製和粘貼該運行中的輸出。

1. 如果尚未在[Azure 門戶](https://portal.azure.com)中，請在邏輯應用設計器中打開邏輯應用。

1. 在邏輯應用的主功能表上，選擇 **"概述**"。

1. 在 **"執行歷程記錄"** 部分中，選擇所需的邏輯應用運行。

1. 在邏輯應用的工作流中，查找並展開具有所需輸出的操作。

1. 選擇"**顯示原始輸出"** 連結。

1. 複製完整的 JavaScript 物件標記法 （JSON） 物件或要使用的特定子節，例如輸出部分，甚至僅複製標題部分。

1. 按照在["設置靜態結果"](#set-up-static-results)中為操作打開 **"靜態結果**"框的步驟操作。

1. 打開 **"靜態結果"** 框後，選擇任一步驟：

   * 要粘貼完整的 JSON 物件，請選擇 **"切換到 JSON 模式**"（![選擇"切換到](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)JSON 模式"）：

     ![為完整物件選擇"切換到 JSON 模式"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * 要僅粘貼 JSON 部分（該部分的標籤旁邊），請選擇該部分的 **"切換到 JSON 模式**"，例如：

     ![為輸出選擇"切換到 JSON 模式"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. 在 JSON 編輯器中，粘貼以前複製的 JSON。

   ![JSON 模式](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 完成之後，選擇 [完成]****。 或者，要返回到設計器，請選擇 **"切換編輯器模式**"（![選擇"切換編輯器模式](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)"）。

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>查找使用靜態結果的運行

邏輯應用的執行歷程記錄標識操作使用靜態結果的運行。 要查找這些運行，請按照以下步驟操作：

1. 在邏輯應用的主功能表上，選擇 **"概述**"。 

1. 在右側窗格中，**在"執行歷程記錄"** 下，查找 **"靜態結果**"列。 

   任何包含具有結果的操作的運行都將 **"靜態結果**"列設置為 **"已啟用"，** 例如：

   ![執行歷程記錄 - 靜態結果列](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 要查看使用靜態結果的操作，請選擇"**靜態結果**"列設置為 **"已啟用**"的要運行。

   使用靜態結果的操作顯示測試燒杯（![靜態結果](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)圖示）圖示，例如：

   ![執行歷程記錄 - 使用靜態結果的操作](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>禁用靜態結果

關閉靜態結果不會丟棄上次設置中的值。 因此，下次打開靜態結果時，可以繼續使用以前的值。

1. 查找要禁用靜態輸出的操作。 在操作的右上角，選擇測試燒杯圖示（![用於靜態結果](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)的圖示）。

   ![禁用靜態結果](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. 選擇**禁用靜態結果** > **完成**。

   ![禁用靜態結果](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>參考資料

有關基礎工作流定義中此設置的詳細資訊，請參閱[靜態結果 - 工作流定義語言和運行時配置的架構引用](../logic-apps/logic-apps-workflow-definition-language.md#static-results).[靜態結果 - 運行時配置設置](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>後續步驟

* 瞭解有關[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)的更多資訊