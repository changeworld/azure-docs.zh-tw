---
title: 驗證 XML 進行 B2B 企業集成
description: 使用 Azure 邏輯應用中的架構與企業集成包一起驗證 XML
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792157"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在採用 Enterprise Integration Pack 的 Azure Logic Apps 中驗證適用於 B2B 企業整合的 XML

通常，在 B2B 方案中，協定中的交易夥伴需要確保他們交換的消息在開始任何資料處理之前有效。 您可以使用企業集成包提供的 XML 驗證操作，根據預定義的架構驗證文檔。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有訂閱，[請註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 要使用 XML 驗證操作的空白或現有邏輯應用。 如果您還不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 與 Azure 訂閱關聯的[集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)連結到計畫使用 XML 驗證操作的邏輯應用，並包含要用於驗證 XML 內容的架構。 邏輯應用和集成帳戶必須存在於同一位置或 Azure 區域中。

## <a name="add-xml-validation-action"></a>添加 XML 驗證操作

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中打開邏輯應用。

1. 如果在"邏輯應用設計器"中，在搜索框中有一個空白邏輯應用，請`HTTP request`輸入為篩選器，然後選擇 **"何時收到 HTTP 要求"** 觸發器。 否則，請繼續下一個步驟。

1. 在工作流的最後一步下，選擇 **"新建步驟**"。

   要在現有步驟之間添加操作，請將指標移到連接這些步驟的箭頭上，以便出現加號 （**+**）。 選擇該加號，然後選擇 **"添加操作**"。

1. 在 [選擇動作]**** 底下，選取 [內建]****。 在搜尋方塊中，輸入 `xml validation` 作為篩選條件。 從動作清單中，選擇**XML 驗證**。

   ![查找並選擇"XML 驗證"操作](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. 要指定要驗證的 XML 內容，請在 **"內容"** 框中按一下，以便顯示動態內容清單。

   ![打開動態內容清單](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   動態內容清單顯示表示工作流中以前步驟的輸出的屬性權杖。 如果清單未顯示預期屬性，請檢查觸發器或操作標題，是否可以選擇 **"查看更多**"。

1. 從動態內容清單中選擇具有要驗證的內容的屬性。

   本示例從觸發器中選擇 **"正文**"輸出。

   ![選擇要驗證的內容](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. 要指定要用於驗證的架構，請打開**架構名稱**清單，然後選擇添加到連結集成帳戶的驗證架構。

   ![選擇要用於驗證的架構](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. 儲存您的邏輯應用程式。

   現在，您已完成設置驗證。 在真實應用中，您可能希望將驗證的資料存儲在業務線 （LOB） 應用中（如 SalesForce）。 若要將驗證的輸出傳送到 Salesforce，請新增一個動作。

1. 要測試驗證操作，可以發送請求以觸發邏輯應用的工作流。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[企業集成包的更多](../logic-apps/logic-apps-enterprise-integration-overview.md)