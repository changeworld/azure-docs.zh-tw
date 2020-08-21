---
title: 使用液體範本轉換 JSON 和 XML
description: 在 Azure Logic Apps 中使用液體範本作為地圖來轉換 JSON 和 XML
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: b3919cbbe0ba7a796a21ae566afb8e2d9fa784db
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716668"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>使用液體範本將 JSON 和 XML 轉換為 Azure Logic Apps 中的地圖

當您想要在邏輯應用程式中執行基本 JSON 轉換時，可以使用原生 [資料作業](../logic-apps/logic-apps-perform-data-operations.md) ，例如 **撰寫** 或 **剖析 JSON**。 若為具有反覆運算、控制流程和變數等專案之 JSON 轉換的 advanced 和 complex JSON 轉換，請使用「 [液體](https://shopify.github.io/liquid/) 開放原始碼」範本語言建立並使用描述這些轉換的範本。 您也可以 [執行其他轉換](#other-transformations)，例如，將 JSON 轉換成文字、將 xml 轉換為 json，以及將 xml 轉換為文字。

在您的邏輯應用程式中執行液體轉換之前，您必須先建立用來定義所需對應的液體範本。 然後，將 [範本以地圖形式上傳](../logic-apps/logic-apps-enterprise-integration-maps.md) 到您的 [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)中。 當您將 **轉換 json 新增至** 邏輯應用程式時，您可以接著選取液體範本作為要使用之動作的地圖。

本文說明如何完成這些工作：

* 建立液體範本。
* 將範本新增至整合帳戶。
* 將液體轉換動作新增至您的邏輯應用程式。
* 選取範本做為您想要使用的對應。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* 關於[液體範本語言](https://shopify.github.io/liquid/)的基本知識

  > [!NOTE]
  > 將 **Json 轉換為 json-液體** 動作會遵循 [適用于液體的 DotLiquid 實施](https://github.com/dotliquid/dotliquid)，這與液體的 [Shopify 實施](https://shopify.github.io/liquid)的特定情況不同。 如需詳細資訊，請參閱「 [液體範本考慮](#liquid-template-considerations)」。

## <a name="create-the-template"></a>建立範本

1. 建立用來作為 JSON 轉換地圖的液體範本。 您可以使用任何您想要的編輯工具。

   針對此範例，請建立如本節所述的範例液體範本：

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

1. 使用延伸模組儲存範本 `.liquid` 。 此範例會使用 `SimpleJsonToJsonTemplate.liquid`。

## <a name="upload-the-template"></a>上傳範本

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站搜尋方塊中，輸入 `integration accounts` ，然後選取 [ **整合帳戶**]。

   ![尋找 [整合帳戶]](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. 尋找並選取您的整合帳戶。

   ![選取企業整合帳戶](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. 在 [ **總覽** ] 窗格的 [ **元件**] 底下，選取 [ **對應**]。

    ![選取 [地圖] 圖格](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. 在 [ **對應** ] 窗格中，選取 [ **新增** ]，並提供您地圖的下列詳細資料：

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **名稱** | `JsonToJsonTemplate` | 對應的名稱，在此範例中是 "JsonToJsonTemplate" |
   | **對應類型** | **liquid** | 對應的類型。 對於 JSON 到 JSON 的轉換，請選取 [Liquid]****。 |
   | **地圖** | `SimpleJsonToJsonTemplate.liquid` | 用於轉換的現有 Liquid 範本或對應檔案，在此範例中是 "SimpleJsonToJsonTemplate.liquid"。 若要尋找此檔案，您可以使用檔案選擇器。 如需地圖大小的限制，請參閱 [限制和](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)設定。 |
   |||

   ![新增液體範本](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>新增液體轉換動作

1. 在 Azure 入口網站中，遵循下列步驟來[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在邏輯應用程式設計工具中，將[要求觸發程序](../connectors/connectors-native-reqres.md#add-request)新增至邏輯應用程式。

1. 在觸發程序下方，選擇 [新增步驟]。 在搜尋方塊中，輸入 `liquid` 做為篩選準則，然後選取此動作： **將 JSON 轉換為 Json-液體**

   ![尋找並選取 Liquid 動作](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. 開啟 **對應** 清單，然後選取您的液體範本，在此範例中為 "JsonToJsonTemplate"。

   ![選取對應](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   如果 maps 清單是空的，您的邏輯應用程式很可能不會連結到您的整合帳戶。 
   若要將邏輯應用程式連結至具有 Liquid 範本或對應的企業整合帳戶，請遵循下列步驟：

   1. 在企業邏輯應用程式功能表上，選取 [工作流程設定]****。

   1. 從 [ **選取整合帳戶** ] 清單中，選取您的整合帳戶，然後選取 [ **儲存**]。

      ![將邏輯應用程式連結至企業整合帳戶](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. 現在將 [ **內容** ] 屬性新增至此動作。 開啟 [ **加入新的參數** ] 清單，然後選取 [ **內容**]。

   ![將 [內容] 屬性新增至動作](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. 若要設定 [ **內容** ] 屬性值，請在 [ **內容** ] 方塊內按一下，以顯示動態內容清單。 選取 **主體** 權杖，其代表來自觸發程式的本文內容輸出。

   ![選取 "Content" 屬性值的 "Body" 權杖](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   完成上述步驟後，動作會如下列範例所示︰

   ![已完成「將 JSON 轉換為 JSON」動作](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>測試應用程式邏輯

使用 [Postman](https://www.getpostman.com/postman) 或類似的工具，將 JSON 輸入張貼至邏輯應用程式。 從邏輯應用程式轉換的 JSON 輸出如下列範例所示：

![範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>液體範本考慮

* 液體範本遵循 Azure Logic Apps 中地圖的檔案 [大小限制](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) 。

* 將 **Json 轉換為 json-液體** 動作會遵循 [DotLiquid 的液體實行](https://github.com/dotliquid/dotliquid)。 這項實作為來自 [Shopify 實施](https://shopify.github.io/liquid/) 的 .NET Framework 埠，在 [特定情況下](https://github.com/dotliquid/dotliquid/issues)則有所不同。

  以下是已知的差異：

  * 將 **Json 轉換為 json-液體** 動作會以原生方式輸出字串，其中可以包含 JSON、XML、HTML 等等。 液體動作只會指出來自液體範本的預期文字輸出是 JSON 字串。 此動作會指示邏輯應用程式以 JSON 物件的形式剖析輸入，並套用包裝函式，讓液體可以解讀 JSON 結構。 轉換之後，此動作會指示邏輯應用程式將來自液體的文字輸出剖析回 JSON。

    DotLiquid 並不會以原生方式瞭解 JSON，因此請務必將反斜線字元 (`\`) 和任何其他保留的 JSON 字元中。

  * 如果您的範本使用 [液體濾波器](https://shopify.github.io/liquid/basics/introduction/#filters)，請確定您遵循 [DotLiquid 和 c # 命名慣例](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing)，其使用 *句子大小寫*。 針對所有液體轉換，請確定您範本中的篩選器名稱也使用句子大小寫。 否則，篩選準則將無法運作。

    例如，當您使用 `replace` 篩選準則時， `Replace` 請使用，而不是 `replace` 。 如果您在 [線上 DotLiquid](http://dotliquidmarkup.org/try-online)試用範例，則適用相同的規則。 如需詳細資訊，請參閱 [Shopify 液體篩選](https://shopify.dev/docs/themes/liquid/reference/filters) 和 [DotLiquid 液濾波器](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters)。 Shopify 規格包含每個篩選準則的範例，因此為了進行比較，您可以在 DotLiquid 嘗試這些範例 [-請在線上試用](https://dotliquidmarkup.org/try-online)。

  * Shopify 延伸模組篩選器中的 `json` 篩選器目前 [不會在 DotLiquid 中執行](https://github.com/dotliquid/dotliquid/issues/384)。 一般而言，您可以使用此篩選來準備要進行 JSON 字串剖析的文字輸出，但您必須改為使用 `Replace` 篩選。

  * `Replace` [DotLiquid 執行](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425)中的標準篩選會使用[正則運算式 (RegEx) ](/dotnet/standard/base-types/regular-expression-language-quick-reference)比對，而[Shopify 的執行](https://shopify.github.io/liquid/filters/replace/)則是使用簡單的[字串](https://github.com/Shopify/liquid/issues/202)比對。 這兩種實現的運作方式都相同，除非您在 match 參數中使用 RegEx 保留字元或 escape 字元。

    例如，若要將 RegEx 保留的反斜線 (`\`) escape 字元，請使用 `| Replace: '\\', '\\'` ，而不是 `| Replace: '\', '\\'` 。 這些範例顯示 `Replace` 當您嘗試將反斜線字元換用時，篩選準則的運作方式不同。 雖然此版本可順利運作：

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    結果如下：

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    此版本失敗：

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    發生此錯誤：

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    如需詳細資訊，請參閱 [取代標準篩選使用 RegEx 模式](https://github.com/dotliquid/dotliquid/issues/385)比對 ...。

  * `Sort` [DotLiquid 執行](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326)中的篩選會依屬性排序陣列或集合中的專案，但有下列差異：<p>

    * 遵循 [Shopify 的 sort_natural 行為](https://shopify.github.io/liquid/filters/sort_natural/)，而不 [是 Shopify 的排序行為](https://shopify.github.io/liquid/filters/sort/)。

    * 只會以字串-英數位元順序排序。 如需詳細資訊，請參閱 [數值排序](https://github.com/Shopify/liquid/issues/980)。

    * 使用不區分 *大小* 寫的順序，而不是區分大小寫的順序。 如需詳細資訊，請參閱 [排序篩選不遵循 Shopify 規格中的大小寫行為]( https://github.com/dotliquid/dotliquid/issues/393)。

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>使用液體進行其他轉換

液體不限於僅限 JSON 轉換。 您也可以使用液體來執行其他轉換，例如：

* [JSON 至文字](#json-text)
* [XML 至 JSON](#xml-json)
* [XML 到文字](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>JSON 轉換為文字

以下是用於此範例的液體範本：

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

以下是範例輸入和輸出：

![JSON 轉換為文字的範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>XML 轉換為 JSON

以下是用於此範例的液體範本：

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

`JSONArrayFor`迴圈是 XML 輸入的自訂迴圈機制，可讓您建立可避免使用尾端逗號的 JSON 承載。 此外， `where` 這個自訂迴圈機制的條件會使用 XML 專案的名稱進行比較，而不是專案的值，就像其他液體濾波器一樣。 如需詳細資訊，請參閱集合 [-主體原則-專案集合的深入探討](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy)。

以下是範例輸入和輸出：

![XML 轉換為 JSON 的範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>XML 轉換為文字

以下是用於此範例的液體範本：

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

以下是範例輸入和輸出：

![XML 轉換為文字的範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>後續步驟

* [Shopify 的液體語言和範例](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid-線上試用](https://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [DotLiquid GitHub 問題](https://github.com/dotliquid/dotliquid/issues/)
* 深入瞭解 [地圖](../logic-apps/logic-apps-enterprise-integration-maps.md)
