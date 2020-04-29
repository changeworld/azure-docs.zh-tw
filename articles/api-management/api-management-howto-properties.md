---
title: 如何在 Azure API 管理原則中使用已命名的值
description: 瞭解如何在 Azure API 管理原則中使用已命名的值。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260916"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>如何在 Azure API 管理原則中使用已命名的值

API 管理原則是系統的強大功能，可讓 Azure 入口網站透過設定來變更 API 的行為。 原則是陳述式的集合，會因 API 的要求或回應循序執行。 原則陳述式可以使用常值文字值、原則運算式和具名值來建構。

每個 API 管理服務實例都有一個索引鍵/值組的集合（稱為「命名值」），對服務實例而言是全域的。 集合中的專案數沒有強加的限制。 已命名的值可用來管理所有 API 設定和原則的常數位串值。 每個指名的值都可能具有下列屬性：

| 屬性      | 類型            | 描述                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | 字串          | 用於參考原則中的已命名值。 一到256個字元的字串。 只允許字母、數位、點和破折號。 |
| `Value`        | 字串          | 實際值。 不得為空白或只包含空白字元。 長度上限為4096個字元。                                        |
| `Secret`       | boolean         | 決定該值是否為密碼且是否應該加密。                                                               |
| `Tags`         | 字串陣列 | 用來篩選指名的值清單。 最多32個標記。                                                                                    |

![具名值](./media/api-management-howto-properties/named-values.png)

指名的值可以包含常值字串和[原則運算式](/azure/api-management/api-management-policy-expressions)。 例如，`Expression` 的值是原則運算式，會傳回包含目前日期與時間的字串。 已將指名`Credential`的值標示為秘密，因此預設不會顯示其值。

| 名稱       | 值                      | 祕密 | Tags          |
| ---------- | -------------------------- | ------ | ------------- |
| 值      | 42                         | False  | 重要-數位 |
| 認證 | ••••••••••••••••••••••     | True   | security      |
| 運算是 | @(DateTime.Now.ToString()) | False  |               |

> [!NOTE]
> 您可以使用儲存在[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)服務中的值，而不是儲存在 API 管理服務中的已命名值，如此[範例](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)所示。

## <a name="to-add-and-edit-a-named-value"></a>若要新增和編輯已命名的值

![新增名為的值](./media/api-management-howto-properties/add-property.png)

1. 從 [ **API 管理**] 底下選取 [ **api** ]。
2. 選取 [具名值]****。
3. 按下 [ **+ 新增**]。

    [名稱] 和 [值] 都是必要值。 如果 [值] 是密碼，請選取 [_這是密碼_] 核取方塊。 輸入一或多個選擇性標籤來協助組織您的具名值，然後按一下 [儲存]。

4. 按一下 [建立]  。

建立命名值之後，您可以按一下它來編輯它。 如果您變更了已命名的值名稱，任何參考該指名值的原則都會自動更新為使用新的名稱。

如需使用 REST API 編輯已命名值的詳細資訊，請參閱[使用 REST API 編輯已命名的值](/rest/api/apimanagement/2019-12-01/property?patch)。

## <a name="to-delete-a-named-value"></a>若要刪除已命名的值

若要刪除已命名的值，請按一下要刪除之指名值旁的 [**刪除**]。

> [!IMPORTANT]
> 如果指定的值是由任何原則所參考，除非您從使用它的所有原則中移除已命名的值，否則將無法成功刪除它。

如需使用 REST API 刪除已命名值的詳細資訊，請參閱[使用 REST API 刪除名為的值](/rest/api/apimanagement/2019-12-01/property/delete)。

## <a name="to-search-and-filter-named-values"></a>搜尋和篩選已命名的值

[具名值]**** 索引標籤包括可協助您管理具名值的搜尋與篩選功能。 若要依名稱篩選已命名的值清單，請在 [**搜尋屬性**] 文字方塊中輸入搜尋字詞。 若要顯示所有具名值，請清除 [搜尋屬性]**** 文字方塊，然後按 Enter 鍵。

若要依標記篩選清單，請在 [**依標記篩選**] 文字方塊中輸入一或多個標記。 若要顯示所有具名值，請清除 [依標記篩選]**** 文字方塊，然後按 Enter 鍵。

## <a name="to-use-a-named-value"></a>使用已命名的值

若要在原則中使用已命名的值，請將其名稱放在雙大`{{ContosoHeader}}`括弧（如）內，如下列範例所示：

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

在此範例中，`ContosoHeader` 是做為 `set-header` 原則中標頭的名稱，且 `ContosoHeaderValue` 是用來做為該標頭的值。 當此原則在對 API 管理閘道提出要求或回應期間進行評估`{{ContosoHeader}}`時`{{ContosoHeaderValue}}` ，會將其取代為其各自的值。

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

如果您查看 API 偵測[器追蹤](api-management-howto-api-inspector.md)，以取得包含前兩個具有已命名值之範例原則的呼叫，您可以看到`set-header`兩個具有已命名值的原則，以及包含原則運算式之已命名值的原則運算式評估。

![API 檢查器追蹤][api-management-api-inspector-trace]

雖然命名值可以包含原則運算式，但不能包含其他的已命名值。 如果包含已命名值參考的文字用於值（例如`Text: {{MyProperty}}`），該參考將不會解析並取代。

## <a name="next-steps"></a>後續步驟

-   深入了解原則的使用方式
    -   [API 管理中的原則](api-management-howto-policies.md)
    -   [原則參考文件](/azure/api-management/api-management-policies)
    -   [原則運算式](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
