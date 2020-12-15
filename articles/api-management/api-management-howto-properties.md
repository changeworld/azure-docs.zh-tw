---
title: 如何在 Azure API 管理原則中使用具名值
description: 了解如何在 Azure API 管理原則中使用具名值。 命名值可包含常值字串、原則運算式和儲存在 Azure Key Vault 中的秘密。
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 12/14/2020
ms.author: apimpm
ms.openlocfilehash: 4cde4dadee33ec1c3f91ab4770dbfe697289cef3
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504727"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>在 Azure API 管理原則中使用命名值

[API 管理原則](api-management-howto-policies.md) 是系統的強大功能，可讓發行者透過設定來變更 API 的行為。 原則是陳述式的集合，會因 API 的要求或回應循序執行。 原則陳述式可以使用常值文字值、原則運算式和具名值來建構。

*命名值* 是每個 API 管理實例中名稱/值配對的全域集合。 集合中的項目數不受限制。 您可以使用命名值來管理所有 API 設定和原則之間的常數位串值和密碼。 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Azure 入口網站中的命名值":::

## <a name="value-types"></a>值類型

|類型  |描述  |
|---------|---------|
|一般紙張     |  常值字串或原則運算式     |
|祕密     |   由 API 管理加密的常值字串或原則運算式      |
|[Key vault](#key-vault-secrets)     |  儲存在 Azure 金鑰保存庫中的秘密識別碼。      |

純數值或秘密可包含 [原則運算式](./api-management-policy-expressions.md)。 例如，運算式會傳回 `@(DateTime.Now.ToString())` 包含目前日期和時間的字串。

如需有關命名值屬性的詳細資訊，請參閱 API 管理 [REST API 參考](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)。

## <a name="key-vault-secrets"></a>Key vault 秘密

您可以在 API 管理中將秘密值儲存為加密字串， (自訂密碼) 或參考 [Azure Key Vault](../key-vault/general/overview.md)中的密碼。 

建議使用 key vault 秘密，因為它有助於改善 API 管理安全性：

* 金鑰保存庫中儲存的秘密可以跨服務重複使用
* 細微 [存取原則](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) 可以套用至秘密
* 在金鑰保存庫中更新的秘密會在 API 管理中自動輪替。 在金鑰保存庫中更新之後，API 管理中的命名值會在4小時內更新。 

### <a name="prerequisites-for-key-vault-integration"></a>Key vault 整合的必要條件

1. 如需建立金鑰保存庫的步驟，請參閱 [快速入門：使用 Azure 入口網站建立金鑰保存庫](../key-vault/general/quick-create-portal.md)。
1. 在 API 管理實例中啟用系統指派或使用者指派的 [受控識別](api-management-howto-use-managed-service-identity.md) 。
1. 將 [key vault 存取原則](../key-vault/general/assign-access-policy-portal.md) 指派給受控識別，其具有從保存庫取得和列出秘密的許可權。 若要新增原則：
    1. 在入口網站中，流覽至您的金鑰保存庫。
    1. 選取 [ **> 存取原則] > + [新增存取原則**] 的設定。
    1. 選取 [ **秘密許可權**]，然後選取 [ **取得** 和 **列出**]。
    1. 在 [ **選取主體**] 中，選取受控識別的資源名稱。 如果您使用系統指派的身分識別，則主體是 API 管理實例的名稱。
1. 建立或匯入金鑰保存庫的秘密。 請參閱 [快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../key-vault/secrets/quick-create-portal.md)。

若要使用金鑰保存庫密碼，請 [新增或編輯命名值](#add-or-edit-a-named-value)，並指定金鑰保存 **庫** 的類型。 選取金鑰保存庫中的秘密。

> [!CAUTION]
> 在 API 管理中使用金鑰保存庫密碼時，請小心不要刪除用來存取金鑰保存庫的秘密、金鑰保存庫或受控識別。

如果您的金鑰保存庫上已啟用 [Key Vault 防火牆](../key-vault/general/network-security.md) ，則下列為使用 key vault 秘密的額外需求：

* 您必須使用 API 管理實例的 **系統指派** 受控識別來存取金鑰保存庫。
* 在 Key Vault 防火牆] 中，啟用 [ **允許信任的 Microsoft 服務略過此防火牆** ] 選項。

如果 API 管理實例部署在虛擬網路中，也請設定下列網路設定：
* 啟用 [服務端點](../key-vault/general/overview-vnet-service-endpoints.md) ，以在 API 管理子網上 Azure Key Vault。
* 設定網路安全性群組 (NSG) 規則，以允許連至 AzureKeyVault 和 AzureActiveDirectory [服務標記](../virtual-network/service-tags-overview.md)的輸出流量。 

如需詳細資訊，請參閱 [連接到虛擬網路](api-management-using-with-vnet.md#-common-network-configuration-issues)中的網路設定詳細資料。

## <a name="add-or-edit-a-named-value"></a>新增或編輯名為的值

### <a name="add-a-key-vault-secret"></a>新增金鑰保存庫秘密

請參閱 [金鑰保存庫整合的必要條件](#prerequisites-for-key-vault-integration)。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 API 管理執行個體。
1. 在 [ **api**] 下，選取 [**命名值**  >  **+ 新增**]。
1. 輸入 **名稱** 識別碼，然後輸入用來在原則中參考屬性的 **顯示名稱** 。
1. 在 [ **數值型別**] 中，選取 [ **金鑰保存庫**]。
1. 輸入金鑰保存庫密碼 (的識別碼，而不) 版本，或選擇 [ **選取** ] 以從金鑰保存庫選取秘密。
    > [!IMPORTANT]
    > 如果您自行輸入 key vault 秘密識別碼，請確定它沒有版本資訊。 否則，在金鑰保存庫中的更新之後，密碼將不會自動輪替 API 管理。
1. 在 [ **用戶端身分識別**] 中，選取系統指派或現有使用者指派的受控識別。 瞭解如何 [在您的 API 管理服務中新增或修改受控](api-management-howto-use-managed-service-identity.md)識別。
    > [!NOTE]
    > 身分識別需要取得和列出金鑰保存庫中秘密的許可權。 如果您尚未設定金鑰保存庫的存取權，API 管理會提示您，讓它可以使用必要的許可權自動設定身分識別。
1. 新增一或多個選擇性標記以協助組織您的命名值，然後 **儲存**。
1. 選取 [建立]。

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="新增金鑰保存庫秘密值":::

### <a name="add-a-plain-or-secret-value"></a>新增純文字或秘密值

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 API 管理執行個體。
1. 在 [ **api**] 下，選取 [**命名值**  >  **+ 新增**]。
1. 輸入 **名稱** 識別碼，然後輸入用來在原則中參考屬性的 **顯示名稱** 。
1. 在 [ **數值型別**] 中，選取 [ **純** 文本] 或 [ **秘密**]。
1. 在 [ **值**] 中，輸入字串或原則運算式。
1. 新增一或多個選擇性標記以協助組織您的命名值，然後 **儲存**。
1. 選取 [建立]。

一旦建立命名值之後，您就可以選取名稱來編輯它。 如果您變更顯示名稱，參考該命名值的任何原則都會自動更新為使用新的顯示名稱。

## <a name="use-a-named-value"></a>使用命名值

本節中的範例會使用下表所示的命名值。

| 名稱               | 值                      | 祕密 | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | 否  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | 否  | 

若要在原則中使用命名值，請將其顯示名稱放在雙括弧中的括弧內， `{{ContosoHeader}}` 如下列範例所示：

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

在此範例中，`ContosoHeader` 是做為 `set-header` 原則中標頭的名稱，且 `ContosoHeaderValue` 是用來做為該標頭的值。 在 API 管理閘道的要求或回應期間評估此原則時，`{{ContosoHeader}}` 和 `{{ContosoHeaderValue}}` 會換成各自的值。

如前述範例所示，具名值可以作為完整的屬性或元素值使用，但它們也可以插入部分的常值文字運算式或與之結合，如以下範例所示： 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

具名值也可以包含原則運算式。 在下列範例中， `ExpressionProperty` 會使用運算式。

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

評估此原則時， `{{ExpressionProperty}}` 會將其值取代為 `@(DateTime.Now.ToString())` 。 因為該值是原則運算式，所以會評估運算式，且原則會繼續執行。

您可以在 Azure 入口網站或 [開發人員入口網站](api-management-howto-developer-portal.md) 中進行測試，方法是呼叫在範圍內具有命名值之原則的作業。 在下列範例中，會使用前兩個含具名值的範例 `set-header` 原則來呼叫作業。 請注意，回應中包含兩個使用具有命名值的原則設定的自訂標頭。

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="測試 API 回應":::

如果您查看輸出 [API 追蹤](api-management-howto-api-inspector.md) 中是否包含兩個具有命名值的範例原則，您可以看到兩個具有已命名值的 `set-header` 原則，以及包含原則運算式的命名值的原則運算式評估。

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API 檢查器追蹤":::

> [!CAUTION]
> 如果原則參考 Azure Key Vault 中的密碼，則可存取已啟用 [API 要求追蹤](api-management-howto-api-inspector.md)之訂用帳戶的使用者，將會看到金鑰保存庫的值。

雖然具名值可以包含原則運算式，但不可包含其他具名值。 如果使用包含具名值參考的文字作為值，例如 `Text: {{MyProperty}}`，則不會解析和取代該參考。

## <a name="delete-a-named-value"></a>刪除命名值

若要刪除指定的值，請選取名稱，然後從內容功能表中選取 [ **刪除** ] (**...**) 。

> [!IMPORTANT]
> 如果任何 API 管理原則參考此命名值，您就無法將它刪除，除非您從所有使用它的原則中移除該命名值。

## <a name="next-steps"></a>後續步驟

-   深入了解原則的使用方式
    -   [API 管理中的原則](api-management-howto-policies.md)
    -   [原則參考文件](./api-management-policies.md)
    -   [原則運算式](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

