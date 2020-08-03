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
ms.openlocfilehash: 5aa6b3717925146607f3785ad5ea5fb940e8c236
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503364"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>使用液體範本轉換 JSON 和 XML Azure Logic Apps 中的地圖

當您想要在邏輯應用程式中執行基本的 JSON 轉換時，您可以使用原生[資料作業](../logic-apps/logic-apps-perform-data-operations.md)，例如**撰寫**或**剖析 JSON**。 針對具有專案（例如反復專案、控制流程和變數）的 advanced 和複雜 JSON 至 JSON 轉換，請使用「[液體](https://shopify.github.io/liquid/)開放原始碼」範本語言來建立和使用描述這些轉換的範本。 您也可以[執行其他轉換](#other-transformations)，例如，JSON 至文字、XML 到 JSON，以及 xml 到文字。

在您的邏輯應用程式中執行液體轉換之前，您必須先建立一個會定義您想要之對應的液體範本。 接著，您可以將[範本當做對應上傳](../logic-apps/logic-apps-enterprise-integration-maps.md)到您的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 當您將 [**轉換 json 至 json** ] 動作新增至邏輯應用程式時，您可以接著選取 [液體] 範本做為要使用之動作的對應。

本文說明如何完成這些工作：

* 建立液體範本。
* 將範本新增至您的整合帳戶。
* 將 [液體轉換] 動作新增至您的邏輯應用程式。
* 選取範本作為您想要使用的對應。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* 關於[液體範本語言](https://shopify.github.io/liquid/)的基本知識

  > [!NOTE]
  > [將**Json 轉換為 json** ] 動作會遵循[液體的 DotLiquid 實施](https://github.com/dotliquid/dotliquid)，這在特定案例中是來自[液體的 Shopify 實](https://shopify.github.io/liquid)作為不同。 如需詳細資訊，請參閱[液體範本考慮](#template-considerations)。

## <a name="create-the-template"></a>建立範本

1. 建立您用來做為 JSON 轉換對應的液體範本。 您可以使用任何您想要的編輯工具。

   針對此範例，請建立範例液體範本，如本節中所述：

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

1. 使用延伸模組來儲存範本 `.liquid` 。 此範例會使用 `SimpleJsonToJsonTemplate.liquid`。

## <a name="upload-the-template"></a>上傳範本

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 [Azure 入口網站搜尋] 方塊中，輸入 `integration accounts` ，然後選取 [**整合帳戶**]。

   ![尋找「整合帳戶」](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. 尋找並選取您的整合帳戶。

   ![選取企業整合帳戶](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. 在 [**總覽**] 窗格的 [**元件**] 底下，選取 [**對應**]。

    ![選取 [地圖] 磚](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. 在 [**對應**] 窗格上，選取 [**新增**]，並為您的對應提供下列詳細資料：

   | 屬性 | 值 | 說明 |
   |----------|-------|-------------|
   | **名稱** | `JsonToJsonTemplate` | 對應的名稱，在此範例中是 "JsonToJsonTemplate" |
   | **對應類型** | **liquid** | 對應的類型。 對於 JSON 到 JSON 的轉換，請選取 [Liquid]****。 |
   | **地圖** | `SimpleJsonToJsonTemplate.liquid` | 用於轉換的現有 Liquid 範本或對應檔案，在此範例中是 "SimpleJsonToJsonTemplate.liquid"。 若要尋找此檔案，您可以使用檔案選擇器。 如需對應大小限制，請參閱[限制和](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)設定。 |
   |||

   ![新增液體範本](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>新增液體轉換動作

1. 在 Azure 入口網站中，遵循下列步驟來[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在邏輯應用程式設計工具中，將[要求觸發程序](../connectors/connectors-native-reqres.md#add-request)新增至邏輯應用程式。

1. 在觸發程序下方，選擇 [新增步驟]。 在搜尋方塊中，輸入 `liquid` 做為您的篩選準則，然後選取此動作：將**JSON 轉換為 Json-液體**

   ![尋找並選取 Liquid 動作](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. 開啟 [**對應**] 清單，然後選取您的液體範本，在此範例中為 "JsonToJsonTemplate"。

   ![選取對應](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   如果 [對應] 清單是空的，您的邏輯應用程式可能不會連結到您的整合帳戶。 
   若要將邏輯應用程式連結至具有 Liquid 範本或對應的企業整合帳戶，請遵循下列步驟：

   1. 在企業邏輯應用程式功能表上，選取 [工作流程設定]****。

   1. 從 [**選取整合帳戶**] 清單中，選取您的整合帳戶，然後選取 [**儲存**]。

      ![將邏輯應用程式連結至企業整合帳戶](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. 現在，將**Content**屬性加入此動作。 開啟 [**加入新的參數**] 清單，然後選取 [**內容**]。

   ![將 "Content" 屬性新增至動作](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. 若要設定 [**內容**] 屬性值，請按一下 [**內容**] 方塊內部，讓動態內容清單出現。 選取 [本文 **] 權杖，其代表**觸發程式的內文內容輸出。

   ![選取 "Content" 屬性值的 "Body" token](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   完成上述步驟後，動作會如下列範例所示︰

   ![已完成「將 JSON 轉換為 JSON」動作](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>測試應用程式邏輯

藉由使用[Postman](https://www.getpostman.com/postman)或類似的工具，將 JSON 輸入張貼至您的邏輯應用程式。 從邏輯應用程式轉換的 JSON 輸出如下列範例所示：

![範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>液體範本考慮

* 液體範本會遵循 Azure Logic Apps 中對應的檔案[大小限制](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)。

* [將**Json 轉換為 json** ] 動作遵循[適用于液體的 DotLiquid 實施](https://github.com/dotliquid/dotliquid)。 此實作為來自[Shopify 的液體執行](https://shopify.github.io/liquid/)的 .NET Framework 埠，並在[特定案例](https://github.com/dotliquid/dotliquid/issues)中有所不同。

  以下是已知的差異：

  * 將**Json 轉換為 json 的**動作原本就會輸出字串，其中可以包含 JSON、XML、HTML 等。 液體動作只會指出來自液體範本的預期文字輸出是 JSON 字串。 動作會指示邏輯應用程式將輸入剖析為 JSON 物件，並套用包裝函式，讓液體可以解讀 JSON 結構。 轉換之後，動作會指示邏輯應用程式將文字輸出從液體剖析回 JSON。

    DotLiquid 無法以原生方式瞭解 JSON，因此請確定您已將反斜線字元（ `\` ）和任何其他保留的 JSON 字元轉義。

  * 如果您的範本使用[液體濾波器](https://shopify.github.io/liquid/basics/introduction/#filters)，請確定您遵循[DotLiquid 和 c # 命名慣例](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing)，其使用*句子大小寫*。 針對所有的液體轉換，請確定範本中的篩選器名稱也會使用句子大小寫。 否則，篩選準則將無法使用。

    例如，當您使用篩選器時，請 `replace` 使用 `Replace` ，而不是 `replace` 。 如果您嘗試在[DotLiquid online](http://dotliquidmarkup.org/try-online)中的範例，也適用相同的規則。 如需詳細資訊，請參閱[Shopify 液體濾波器](https://shopify.dev/docs/themes/liquid/reference/filters)和[DotLiquid 液體濾波器](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters)。 Shopify 規格包含每個篩選準則的範例，因此若要進行比較，您可以在 DotLiquid 嘗試這些範例[-線上試用](https://dotliquidmarkup.org/try-online)。

  * `json`Shopify 延伸模組篩選器中的篩選目前[未在 DotLiquid 中執行](https://github.com/dotliquid/dotliquid/issues/384)。 一般來說，您可以使用此篩選來準備文字輸出以進行 JSON 字串剖析，但是您必須改為使用 `Replace` 篩選。

  * `Replace` [DotLiquid 執行](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425)中的標準篩選會使用[正則運算式（RegEx）](/dotnet/standard/base-types/regular-expression-language-quick-reference)比對，[而 Shopify 執行](https://shopify.github.io/liquid/filters/replace/)則會使用[簡單的字串](https://github.com/Shopify/liquid/issues/202)比對。 在您于 match 參數中使用 RegEx 保留的字元或 escape 字元之前，這兩種方法的表現方式都相同。

    例如，若要將 RegEx 保留的反斜線（ `\` ）換用字元，請使用 `| Replace: '\\', '\\'` ，而不是 `| Replace: '\', '\\'` 。 這些範例顯示 `Replace` 當您嘗試將反斜線字元轉義時，篩選的行為會有所不同。 雖然此版本可順利運作：

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    使用此結果：

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    此版本失敗：

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    發生此錯誤：

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    如需詳細資訊，請參閱[Replace standard filter 使用 RegEx 模式](https://github.com/dotliquid/dotliquid/issues/385)比對 ...。

  * `Sort` [DotLiquid 執行](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326)中的篩選會依屬性排序陣列或集合中的專案，但具有下列差異：<p>

    * 遵循[Shopify 的 sort_natural 行為](https://shopify.github.io/liquid/filters/sort_natural/)，而不[是 Shopify 的排序行為](https://shopify.github.io/liquid/filters/sort/)。

    * 僅以字串-英數位元順序排序。 如需詳細資訊，請參閱[數值排序](https://github.com/Shopify/liquid/issues/980)。

    * 使用不區分*大小寫*的順序，不區分大小寫的順序。 如需詳細資訊，請參閱[排序篩選不會遵循 Shopify 規格中的大小寫行為]( https://github.com/dotliquid/dotliquid/issues/393)。

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>使用液體進行其他轉換

液體不限於僅限 JSON 轉換。 您也可以使用液體來執行其他轉換，例如：

* [JSON 到文字](#json-text)
* [XML 到 JSON](#xml-json)
* [XML 到文字](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>JSON 轉換為文字

以下是此範例所使用的液體範本：

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

以下是範例輸入和輸出：

![JSON 轉換為文字的範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>XML 轉換為 JSON

以下是此範例所使用的液體範本：

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

`JSONArrayFor`迴圈是 XML 輸入的自訂迴圈機制，因此您可以建立可避免尾端逗號的 JSON 承載。 此外， `where` 此自訂迴圈機制的條件會使用 XML 專案的名稱進行比較，而非專案的值，例如其他液體篩選器。 如需詳細資訊，請參閱[設定-主體原則的深入探討-專案集合](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy)。

以下是範例輸入和輸出：

![XML 轉換為 JSON 的範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>XML 轉換為文字

以下是此範例所使用的液體範本：

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
* 深入瞭解[地圖](../logic-apps/logic-apps-enterprise-integration-maps.md)
