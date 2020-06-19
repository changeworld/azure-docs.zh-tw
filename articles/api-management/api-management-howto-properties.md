---
title: 如何在 Azure API 管理原則中使用具名值
description: 了解如何在 Azure API 管理原則中使用具名值。
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
ms.openlocfilehash: dc8ca7296658f4113d86765f230ca0158727255f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649208"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>如何在 Azure API 管理原則中使用具名值

API 管理原則是系統的強大功能，可讓 Azure 入口網站透過設定來變更 API 的行為。 原則是陳述式的集合，會因 API 的要求或回應循序執行。 原則陳述式可以使用常值文字值、原則運算式和具名值來建構。

每個 API 管理服務執行個體都有鍵/值組的集合 (稱為具名值)，全域適用於服務執行個體。 集合中的項目數不受限制。 具名值可用來管理所有 API 設定和原則的常數字串值。 每個具名值可以具有下列屬性：

| 屬性      | 類型            | 描述                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | 字串          | 用於原則中參考具名值。 1 到 256 個字元的字串。 只允許字母、數字、點和破折號。 |
| `Value`        | 字串          | 實際值。 不得為空白或只包含空白字元。 長度上限為 4096 個字元。                                        |
| `Secret`       | boolean         | 決定該值是否為密碼且是否應該加密。                                                               |
| `Tags`         | 字串陣列 | 用來篩選具名值清單。 最多 32 個標記。                                                                                    |

![具名值](./media/api-management-howto-properties/named-values.png)

具名值可以包含常值字串和[原則運算式](/azure/api-management/api-management-policy-expressions)。 例如，`Expression` 的值是原則運算式，會傳回包含目前日期與時間的字串。 具名值 `Credential` 已標示為密碼，因此預設不會顯示值。

| 名稱       | 值                      | 祕密 | Tags          |
| ---------- | -------------------------- | ------ | ------------- |
| 值      | 42                         | False  | vital-numbers |
| 認證 | ••••••••••••••••••••••     | True   | security      |
| 運算是 | @(DateTime.Now.ToString()) | False  |               |

> [!NOTE]
> 您可以使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 服務中儲存的值，而不是儲存在 API 管理服務中的具名值，如這個[範例](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)所示。

## <a name="to-add-and-edit-a-named-value"></a>新增和編輯具名值

![新增具名值](./media/api-management-howto-properties/add-property.png)

1. 選取 [API 管理] 下方的 [API]。
2. 選取 [具名值]。
3. 按 [+新增]。

    [名稱] 和 [值] 都是必要值。 如果值是祕密，請選取 [這是祕密] 核取方塊。 輸入一或多個選擇性標籤來協助組織您的具名值，然後按一下 [儲存]。

4. 按一下頁面底部的 [新增] 。

具名值建立之後，按一下即可編輯。 如果您變更具名值名稱，則任何參考該具名值的原則，將會自動更新為使用新的名稱。

## <a name="to-delete-a-named-value"></a>刪除具名值

若要刪除具名值，請在要刪除的具名值旁邊，按一下 [刪除]。

> [!IMPORTANT]
> 如有任何原則參考該具名值，則除非從所有使用該具名值的原則中移除，否則無法成功刪除該具名值。

## <a name="to-search-and-filter-named-values"></a>搜尋和篩選具名值

[具名值] 索引標籤包括可協助您管理具名值的搜尋與篩選功能。 若要依名稱來篩選具名值清單，請在 [搜尋屬性] 文字方塊中輸入搜尋字詞。 若要顯示所有具名值，請清除 [搜尋屬性] 文字方塊，然後按 Enter 鍵。

若要依標記來篩選清單，請在 [依標記篩選] 文字方塊中輸入一或多個標記。 若要顯示所有具名值，請清除 [依標記篩選] 文字方塊，然後按 Enter 鍵。

## <a name="to-use-a-named-value"></a>使用具名值

若要在原則中使用具名值，請以兩對大括弧括住名稱 (例如 `{{ContosoHeader}}`)，如下列範例所示：

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

在此範例中，`ContosoHeader` 是做為 `set-header` 原則中標頭的名稱，且 `ContosoHeaderValue` 是用來做為該標頭的值。 在 API 管理閘道的要求或回應期間評估此原則時，`{{ContosoHeader}}` 和 `{{ContosoHeaderValue}}` 會換成各自的值。

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

如果您在 [API 檢查器追蹤](api-management-howto-api-inspector.md)中查看呼叫，而此呼叫包含前述兩個原則範例 (有具名值)，您會看到兩個已插入具名值的 `set-header` 原則，以及具名值 (包含原則運算式) 的原則運算式評估。

![API 檢查器追蹤][api-management-api-inspector-trace]

雖然具名值可以包含原則運算式，但不可包含其他具名值。 如果使用包含具名值參考的文字作為值，例如 `Text: {{MyProperty}}`，則不會解析和取代該參考。

## <a name="next-steps"></a>後續步驟

-   深入了解原則的使用方式
    -   [API 管理中的原則](api-management-howto-policies.md)
    -   [原則參考文件](/azure/api-management/api-management-policies)
    -   [原則運算式](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
