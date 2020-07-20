---
title: 驗證適用于 B2B 企業整合的 XML
description: 使用 Enterprise Integration Pack Azure Logic Apps 中的架構來驗證 XML
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74792157"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在採用 Enterprise Integration Pack 的 Azure Logic Apps 中驗證適用於 B2B 企業整合的 XML

通常在 B2B 案例中，合約中的交易夥伴必須確定其交換的訊息是有效的，然後才可以開始進行任何資料處理。 您可以使用可用於企業整合套件的 XML 驗證動作，針對預先定義的架構來驗證檔。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您還沒有訂用帳戶，請[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您想要使用 XML 驗證動作的空白或現有邏輯應用程式。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 與您的 Azure 訂用帳戶相關聯的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)會連結至您打算使用 xml 驗證動作的邏輯應用程式，並包含您想要用來驗證 xml 內容的架構。 邏輯應用程式與整合帳戶必須位於相同位置或相同的 Azure 區域中。

## <a name="add-xml-validation-action"></a>新增 XML 驗證動作

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 如果您有空白的邏輯應用程式，請在邏輯應用程式設計工具的 [搜尋] 方塊中，輸入 `HTTP request` 作為篩選準則，然後選取 [**收到 HTTP 要求時**] 觸發程式。 否則，請繼續下一個步驟。

1. 在工作流程的最後一個步驟底下，選取 [**新增步驟**]。

   若要在現有步驟之間新增動作，請將指標移到連接這些步驟的箭號上，如此就 **+** 會出現加號（）。 選取該加號，然後選取 [**新增動作**]。

1. 在 [選擇動作]**** 底下，選取 [內建]****。 在搜尋方塊中，輸入 `xml validation` 作為篩選條件。 從 [動作] 清單中，選取 [ **XML 驗證**]。

   ![尋找並選取 [XML 驗證] 動作](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. 若要指定您要驗證的 XML 內容，請按一下 [**內容**] 方塊內部，讓動態內容清單出現。

   ![開啟動態內容清單](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   動態內容清單會顯示內容 token，這些權杖代表工作流程中先前步驟的輸出。 如果清單未顯示預期的屬性，請檢查 [觸發程式] 或 [動作] 標題，您是否可以選取 [**查看更多**]。

1. 從動態內容清單中，選取包含您要驗證之內容的屬性。

   這個範例會選取觸發程式的**主體**輸出。

   ![選取要驗證的內容](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. 若要指定您要用於驗證的架構，請開啟 [**架構名稱**] 清單，然後選取您已新增至連結整合帳戶的驗證架構。

   ![選取要用於驗證的架構](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. 儲存您的邏輯應用程式。

   您現在已完成設定驗證。 在真實世界的應用程式中，您可能會想要將已驗證的資料儲存在商務營運系統（LOB）應用程式中，例如 SalesForce。 若要將驗證的輸出傳送到 Salesforce，請新增一個動作。

1. 若要測試您的驗證動作，您可以傳送要求來觸發邏輯應用程式的工作流程。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)