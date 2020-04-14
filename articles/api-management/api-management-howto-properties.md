---
title: 如何在 Azure API 管理原則中使用命名值
description: 瞭解如何在 Azure API 管理原則中使用命名值。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 4362d0875ac2c20fc6963d404f86898a12387dad
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260916"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>如何在 Azure API 管理原則中使用命名值

API 管理原則是系統的強大功能，可讓 Azure 入口網站透過設定來變更 API 的行為。 原則是陳述式的集合，會因 API 的要求或回應循序執行。 原則陳述式可以使用常值文字值、原則運算式和具名值來建構。

每個 API 管理服務實例都有一個鍵/ 值對的集合,稱為命名值,這些鍵 / 值對是服務實例的全域。 集合中的項數沒有強制限制。 命名值可用於管理所有 API 設定和策略中的恆定字串值。 每個命名值可能具有以下屬性:

| 屬性      | 類型            | 描述                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | 字串          | 用於引用策略中的命名值。 一個到256個字元的字串。 只允許字母、數位、點和破折號。 |
| `Value`        | 字串          | 實際值。 不能為空或僅由空格組成。 最大 4096 個字元長。                                        |
| `Secret`       | boolean         | 決定該值是否為密碼且是否應該加密。                                                               |
| `Tags`         | 字串陣列 | 用於篩選命名值清單。 最多32個標籤。                                                                                    |

![具名值](./media/api-management-howto-properties/named-values.png)

命名值可以包含文字字串與[策略表示式](/azure/api-management/api-management-policy-expressions)。 例如，`Expression` 的值是原則運算式，會傳回包含目前日期與時間的字串。 命名值`Credential`標記為機密,因此默認情況下不顯示其值。

| 名稱       | 值                      | 祕密 | Tags          |
| ---------- | -------------------------- | ------ | ------------- |
| 值      | 42                         | False  | 生命數位 |
| 認證 | ••••••••••••••••••••••     | True   | security      |
| 運算是 | @(DateTime.Now.ToString()) | False  |               |

> [!NOTE]
> 可以使用存儲在[Azure 密鑰保管庫](https://azure.microsoft.com/services/key-vault/)服務中的值,而不是存儲在 API 管理服務中的命名值,如[本示例](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)所示。

## <a name="to-add-and-edit-a-named-value"></a>新增及編輯命名值

![新增命名值](./media/api-management-howto-properties/add-property.png)

1. 從**API 管理**下選擇**API。**
2. 選取 [具名值]****。
3. 按 **+新增**。

    [名稱] 和 [值] 都是必要值。 如果值是機密,請選中「_這是機密_」複選框。 輸入一或多個選擇性標籤來協助組織您的具名值，然後按一下 [儲存]。

4. 按一下頁面底部的 [新增]  。

創建命名值後,可以通過單擊它對其進行編輯。 如果更改命名值名稱,則引用命名值的任何策略將自動更新以使用新名稱。

有關使用 REST API 編輯命名值的資訊,請參閱[使用 REST API 編輯命名值](/rest/api/apimanagement/2019-12-01/property?patch)。

## <a name="to-delete-a-named-value"></a>刪除命名值

要刪除命名值,請按下要刪除的命名值旁邊的 **「刪除**」 。

> [!IMPORTANT]
> 如果命名值被任何策略引用,則在從使用它的所有策略中刪除命名值之前,您將無法成功刪除它。

有關使用 REST API 刪除命名值的資訊,請參閱[使用 REST API 刪除命名值](/rest/api/apimanagement/2019-12-01/property/delete)。

## <a name="to-search-and-filter-named-values"></a>搜尋與篩選命名值

[具名值]**** 索引標籤包括可協助您管理具名值的搜尋與篩選功能。 要按名稱篩選命名值清單,請在 **「搜尋」屬性**文字框中輸入搜索詞。 若要顯示所有具名值，請清除 [搜尋屬性]**** 文字方塊，然後按 Enter 鍵。

要按標記篩選清單,請在 **「按標記」** 文字框的「篩選器」中輸入一個或多個標記。 若要顯示所有具名值，請清除 [依標記篩選]**** 文字方塊，然後按 Enter 鍵。

## <a name="to-use-a-named-value"></a>使用命名值

要在策略中使用命名值,將其名稱放在雙括弧中,如`{{ContosoHeader}}`, 如下例所示:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

在此範例中，`ContosoHeader` 是做為 `set-header` 原則中標頭的名稱，且 `ContosoHeaderValue` 是用來做為該標頭的值。 當在請求或回應 API 管理閘道期間評估此`{{ContosoHeader}}`策略,`{{ContosoHeaderValue}}`並將其 替換為其各自的值時。

如前述範例所示，具名值可以作為完整的屬性或元素值使用，但它們也可以插入部分的常值文字運算式或與之結合，如以下範例所示：`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

具名值也可以包含原則運算式。 以下範例使用 `ExpressionProperty`。

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

當評估此原則時，`{{ExpressionProperty}}` 會替代為其值 `@(DateTime.Now.ToString())`。 因為該值是原則運算式，所以會評估運算式，且原則會繼續執行。

您可以在開發人員入口網站測試此項目，方法是呼叫原則中包含範圍內具名值的作業。 在下列範例中，會使用前兩個含具名值的範例 `set-header` 原則來呼叫作業。 請注意，回應中包含兩個使用含具名值的原則設定的自訂標頭。

![開發人員入口網站][api-management-send-results]

如果查看包含具有命名值的前兩個範例策略的呼叫的[API 檢查器追蹤](api-management-howto-api-inspector.md),可以看到插入命名值`set-header`的兩個策略以及包含策略表達式的命名值的策略運算式計算。

![API 檢查器追蹤][api-management-api-inspector-trace]

雖然命名值可以包含策略表達式,但它們不能包含其他命名值。 如果包含命名值引用的文本用於值,例如`Text: {{MyProperty}}`,將不會解析和替換該引用。

## <a name="next-steps"></a>後續步驟

-   深入了解原則的使用方式
    -   [API 管理中的原則](api-management-howto-policies.md)
    -   [原則參考文件](/azure/api-management/api-management-policies)
    -   [原則運算式](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
