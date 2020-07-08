---
title: 管理整合帳戶的成品中繼資料
description: 搭配 Enterprise Integration Pack 從 Azure Logic Apps 中的整合帳戶新增或取得成品中繼資料
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74792463"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>搭配 Azure Logic Apps 和 Enterprise Integration Pack 管理整合帳戶中的成品中繼資料

您可以在整合帳戶中定義成品的自訂中繼資料，並在執行階段期間取得該中繼資料以供邏輯應用程式使用。 例如，您可以提供成品的中繼資料 (如合作夥伴、合約、結構描述及對應)，這些全都會使用索引鍵值組來儲存中繼資料。 

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請<a href="https://azure.microsoft.com/free/" target="_blank">註冊一個免費的 Azure 帳戶</a>。

* 具有您要新增中繼資料之成品的基本[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，例如： 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [合約](logic-apps-enterprise-integration-agreements.md)
  * [結構描述](logic-apps-enterprise-integration-schemas.md)
  * [地圖](logic-apps-enterprise-integration-maps.md)

* 已連結到您想要使用之整合帳戶和成品中繼資料的邏輯應用程式。 如果您的邏輯應用程式尚未連結，請了解[如何將邏輯應用程式連結到整合帳戶](logic-apps-enterprise-integration-create-integration-account.md#link-account)。 

  如果您尚未擁有邏輯應用程式，請了解[如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 
  新增您想要用來管理成品中繼資料的觸發程序和動作。 或者您也可以嘗試新增如 [要求]**** 或 [HTTP]**** 等觸發程序到您的邏輯應用程式。

## <a name="add-metadata-to-artifacts"></a>將中繼資料新增到成品

1. 使用您的 Azure 帳戶認證登入 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>。 尋找並開啟您的整合帳戶。

1. 選取要新增中繼資料的成品，然後選擇 [編輯]****。 為該成品輸入中繼資料詳細資料，例如：

   ![輸入中繼資料](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. 完成時，選擇 [確定]。

1. 若要在針對整合帳戶的 JavaScript 物件標記法 (JSON) 定義中檢視此中繼資料，請選擇 [編輯為 JSON]**** 來開啟 JSON 編輯器： 

   ![合作夥伴中繼資料的 JSON](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>取得成品中繼資料

1. 在 Azure 入口網站中，開啟已連結到所需整合帳戶的邏輯應用程式。 

1. 在邏輯應用程式設計工具中，如果您要新增在工作流程中的觸發程式或最後一個動作底下取得中繼資料的步驟，請選擇 [**新增步驟**] [  >  **加入動作**]。 

1. 在搜尋方塊中，輸入「整合帳戶」。 在搜尋方塊下方，選擇 [全部]。 從 [動作] 清單中，選取此動作：**整合帳戶成品查閱-整合帳戶**

   ![選取 [整合帳戶成品查閱]](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. 針對您想要尋找的成品提供此資訊：

   | 屬性 | 必要 | 值 | 說明 | 
   |----------|---------|-------|-------------| 
   | **成品類型** | Yes | [結構描述]****、[對應]****、[合作夥伴]****、[合約]****，或自訂類型 | 所需的成品類型 | 
   | **成品名稱** | Yes | <*成品-名稱*> | 所需的成品名稱 | 
   ||| 

   例如，假設您想要取得合作對象成品的中繼資料：

   ![選取成品類型並輸入成品名稱](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. 新增您想要用來處理該中繼資料的動作，例如：

   1. 在 [整合帳戶成品查閱]**** 動作底下，選擇 [下一步]****，然後選取 [新增動作]****。 

   1. 在搜尋方塊中，輸入 "http"。 在搜尋方塊底下，選擇 [內**建**]，然後選取此動作： **HTTP-HTTP**

      ![新增 HTTP 動作](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. 針對您想要管理的成品中繼資料提供資訊。 

      例如，假設您想要取得先前在本主題中所新增的 `routingUrl` 中繼資料。 以下是您可以指定的屬性值： 

      | 屬性 | 必要 | 值 | 描述 | 
      |----------|----------|-------|-------------| 
      | **方法** | Yes | <*作業對執行*> | 要在成品上執行的 HTTP 作業。 例如，此 HTTP 動作會使用 **GET** 方法。 | 
      | **URI** | 是 | <*中繼資料-位置*> | 若要存取來自所擷取之成品的 `routingUrl` 中繼資料值，您可以使用如下的運算式： <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **標頭** | No | <*標頭-值*> | 來自您想要傳遞到 HTTP 動作之觸發程序的任何標頭輸出。 例如，若要傳遞觸發程序的 `headers` 屬性值，您可以使用如下的運算式： <p>`@triggeroutputs()['headers']` | 
      | **本文** | 否 | <*本文-內容*> | 您想要透過 HTTP 動作的 `body` 屬性傳遞的任何其他內容。 此範例會將成品的 `properties` 值傳遞到 HTTP 動作： <p>1. 按一下 [ **Body** ] 屬性內部，讓動態內容清單出現。 如果沒有出現屬性，請選擇 [更多資訊]****。 <br>2. 從動態內容清單的 [**整合帳戶成品查閱**] 底下，選取 [**屬性**]。 | 
      |||| 

      例如：

      ![針對 HTTP 動作指定值和運算式](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. 若要檢查針對 HTTP 動作所提供的資訊，請檢視邏輯應用程式的 JSON 定義。 在 [邏輯應用程式設計工具] 工作列上，選擇 [程式碼檢視]**** 以顯示應用程式的 JSON 定義，例如：

      ![邏輯應用程式 JSON 定義](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      在您切換回 [邏輯應用程式設計工具] 之後，您所使用的所有運算式現在都會顯示為已解析，例如：

      ![[邏輯應用程式設計工具] 中的已解析運算式](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>後續步驟

* [深入了解合約](logic-apps-enterprise-integration-agreements.md)
