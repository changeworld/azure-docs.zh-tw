---
title: 計畫定義中的法規合規性
description: 說明如何使用計畫定義，依規範網域（例如存取控制、設定管理和其他）將原則分組。
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 15b03b5a40cba1290859b02883df70e3df54c5cf
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89645530"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Azure 原則中的法規合規性

Azure 原則中的法規合規性會提供內建的計畫定義，以根據責任 (_客戶_、 _Microsoft_、_共用_) 來查看**控制項**和**合規性網域**的清單。
針對 Microsoft 負責任的控制項，我們會根據協力廠商證明和我們的執行詳細資料，提供更詳細的審核結果，以達成該合規性。
Microsoft 負責的控制項是 `type` [靜態](./definition-structure.md#type)的。

> [!NOTE]
> 法規合規性是預覽功能。 針對已更新的內建，方案控制項會對應到對應的合規性標準。 現有的合規性標準計畫正在進行更新，以支援法規合規性。

## <a name="regulatory-compliance-defined"></a>已定義法規合規性

法規合規性是以計畫定義的 [分組](./initiative-definition-structure.md#policy-definition-groups) 部分為基礎。 在內建中，計畫定義中的每個群組都會定義名稱 (**控制項**) 、分類 (**符合性網域**) ，以及提供具有該**控制項**相關資訊之[policyMetadata](./initiative-definition-structure.md#metadata-objects)物件的參考。 法規合規性計畫定義必須將 `category` 屬性設定為 **符合規範**。 作為其他標準的方案定義，法規合規性計畫支援建立動態指派的 [參數](./initiative-definition-structure.md#parameters) 。

客戶可以建立自己的法規合規性計畫。 這些定義可以是原始的，也可以從現有的內建定義複製。 如果使用內建的法規合規性計畫定義作為參考，建議您在 [Azure 原則 GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance)存放庫中監視法規合規性定義的來源。

若要將自訂法規合規性計畫連結至 Azure 資訊安全中心儀表板，請參閱 [Azure 資訊安全中心-使用自訂安全性原則](../../../security-center/custom-security-policies.md)。

## <a name="regulatory-compliance-in-portal"></a>入口網站中的法規合規性

使用 [群組](./initiative-definition-structure.md#policy-definition-groups)建立計畫定義之後，入口網站中該方案的 [ **合規性** 詳細資料] 頁面會有其他資訊。 

新的索引標籤會將 **控制項** 加入至頁面。 篩選適用于 **合規性網域** ，原則定義會依 `title` **policyMetadata** 物件中的欄位分組。 每個資料列都代表顯示其合規性狀態、其所屬的**合規性網域**、責任資訊，以及有多少不符合規範的原則定義組成該**控制項**的**控制項**。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="適用于 NIST SP 800-53 R4 內建定義（顯示符合規範和不符合規範的控制項）之法規合規性總覽的螢幕擷取畫面。":::

選取 **控制項** 時，會開啟該控制項詳細資料的頁面。 **總覽**包含與的資訊 `description` `requirements` 。 [ **原則** ] 索引標籤下的所有個別原則定義都是參與此 **控制項**的計畫。 [ **資源相容性** ] 索引標籤可讓您更精細地查看由目前所查看 **控制項**的成員原則所評估的每個資源。

> [!NOTE]
> **Microsoft managed**評估類型適用于[靜態](./definition-structure.md#type)原則定義 `type` 。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="適用于 NIST SP 800-53 R4 內建定義之界限保護控制的法規合規性詳細資料的螢幕擷取畫面。":::

在相同的 **控制** 頁面上，變更為 [ **資源合規性** ] 索引標籤會顯示此 **控制項**的原則定義所包含的所有資源。 篩選準則可用於名稱或識別碼、合規性狀態、資源類型和位置。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="針對 NIST SP 800-53 R4 內建定義之界限保護控制的資源合規性的螢幕擷取畫面。":::

## <a name="regulatory-compliance-in-sdk"></a>SDK 中的法規合規性

如果計畫定義上已啟用法規合規性，則評估掃描記錄、事件和原則狀態 SDK 都會傳回其他屬性。 這些額外的屬性會依合規性狀態分組，並提供每個狀態的群組數目的相關資訊。

下列程式碼是從呼叫新增結果的範例 `summarize` ：

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>接下來的步驟

- 請參閱 [計畫定義結構](./initiative-definition-structure.md)
- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱[了解原則效果](./effects.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
