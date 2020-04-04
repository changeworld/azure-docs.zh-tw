---
title: 使用液體轉換轉換 JSON 資料
description: 使用 Logic Apps 和 Liquid 範本建立進階 JSON 轉換的轉換或對應
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: 0ab9297e772a3b75a077da1c2ae74e5058b2731f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657220"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>使用 Azure Logic Apps 中的 Liquid 範本執行進階的 JSON 轉換

您可以透過原生資料作業動作 (例如**撰寫**或**剖析 JSON**)，在邏輯應用程式中進行基本 JSON 轉換。 若要執行進階的 JSON 轉換，您可以使用 [Liquid](https://shopify.github.io/liquid/) 建立範本或對應，Liquid 是適用於可變動 Web 應用程式的開放原始碼範本語言。 Liquid 模板定義如何轉換 JSON 輸出並支援更複雜的 JSON 轉換,如反覆運算、控制流、變數等。

在邏輯應用中執行 Liquid 轉換之前,必須先使用 Liquid 範本定義 JSON 到 JSON 映射,並將該映射儲存在整合帳戶中。 本文會示範如何建立及使用此 Liquid 範本或對應。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [有關如何創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 基本[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* [液體範本語言](https://shopify.github.io/liquid/)基礎知識

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>為您的企業整合帳戶建立 Liquid 範本或對應

1. 在此範例中，您會建立此步驟中描述的 Liquid 範本範例。 在液體範本中,您可以使用[液體篩檢程式](https://shopify.github.io/liquid/basics/introduction/#filters),使用[DotLiquid](https://dotliquidmarkup.org/)和 C# 命名約定。

   > [!NOTE]
   > 確保篩選器名稱在樣本中使用*句子套管*。 否則,篩選器將無法工作。 此外,地圖有[檔案大小限制](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)。

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

1. 在[Azure 門戶](https://portal.azure.com)中,從`integration accounts`Azure 搜尋 框中輸入 ,然後選擇**整合帳號**。

   ![查找"集成帳戶"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. 查找並選擇您的整合帳戶。

   ![選取企業整合帳戶](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. 在 **「概述」** 窗格中,在 **「元件**」下,選擇 **「地圖**」。

    ![選擇地圖磁貼](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. 在 **「地圖」** 窗格中,選擇 **「新增**」並為地圖提供以下詳細資訊:

   | 屬性 | 值 | 描述 | 
   |----------|-------|-------------|
   | **名稱** | `JsonToJsonTemplate` | 對應的名稱，在此範例中是 "JsonToJsonTemplate" | 
   | **對應類型** | **liquid** | 對應的類型。 對於 JSON 到 JSON 的轉換，請選取 [Liquid]****。 | 
   | **地圖** | `SimpleJsonToJsonTemplate.liquid` | 用於轉換的現有 Liquid 範本或對應檔案，在此範例中是 "SimpleJsonToJsonTemplate.liquid"。 若要尋找此檔案，您可以使用檔案選擇器。 有關地圖大小限制,請參閱[限制和設定](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)。 |
   ||| 

   ![新增液體範本](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>新增 JSON 轉換的 Liquid 動作

1. 在 Azure 入口網站中，遵循下列步驟來[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在邏輯應用程式設計工具中，將[要求觸發程序](../connectors/connectors-native-reqres.md#add-request)新增至邏輯應用程式。

1. 在觸發程序下方，選擇 [新增步驟]****。 在搜尋框中,輸入`liquid`作為篩選器,然後選擇此操作:將**JSON 轉換為 JSON - 液體**

   ![尋找並選取 Liquid 動作](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. 打開**地圖**清單,然後選擇「液體」範本,在此示例中為「JsonToJsonTemplate」。。

   ![選取對應](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   如果清單是空的，可能表示邏輯應用程式未連結至您的企業整合帳戶。 
   若要將邏輯應用程式連結至具有 Liquid 範本或對應的企業整合帳戶，請遵循下列步驟：

   1. 在企業邏輯應用程式功能表上，選取 [工作流程設定]****。

   1. 從 **「選擇集成帳戶**」清單中,選擇集成帳戶,然後選擇 **「保存**」。

      ![將邏輯應用程式連結至企業整合帳戶](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. 現在將 **「內容」** 屬性添加到此操作中。 開啟 **「新增新參數**清單」,然後選擇 **「內容」。**

   ![將「內容」屬性新增到作業](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. 要設置 **「內容屬性**」值,請按下 **「內容」** 框內,以便顯示動態內容清單。 選擇 **「正文**」令牌,該令牌表示觸發器中的正文內容輸出。

   ![為內容屬性值選擇「正文」標記](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   完成上述步驟後，動作會如下列範例所示︰

   ![已完成「將 JSON 轉換為 JSON」操作](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>測試應用程式邏輯

從 [Postman](https://www.getpostman.com/postman) 或類似的工具將 JSON 輸入張貼至邏輯應用程式。 從邏輯應用程式轉換的 JSON 輸出如下列範例所示：
  
![範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>更多 Liquid 動作範例
Liquid 並非限用於 JSON 轉換。 以下其他可使用 Liquid 的轉換動作。

* JSON 轉換為文字
  
  以下是此範例所使用的 Liquid 範本：
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   以下是範例輸入和輸出：
  
   ![JSON 轉換為文字的範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML 轉換為 JSON
  
  以下是此範例所使用的 Liquid 範本：
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   以下是範例輸入和輸出：

   ![XML 轉換為 JSON 的範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML 轉換為文字
  
  以下是此範例所使用的 Liquid 範本：

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   以下是範例輸入和輸出：

   ![XML 轉換為文字的範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>後續步驟

* [深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md "瞭解企業整合式")  
* [深入了解對應](../logic-apps/logic-apps-enterprise-integration-maps.md "瞭解企業整合地圖")  

