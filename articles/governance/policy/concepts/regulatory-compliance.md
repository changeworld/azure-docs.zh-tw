---
title: 計畫定義中的法規合規性
description: 描述如何使用計畫定義，依法規網域（例如存取控制、設定管理及其他）將原則分組。
ms.date: 05/11/2020
ms.topic: conceptual
ms.openlocfilehash: 3bcc876cf8fd608d700e39bda11c94395beace80
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84205899"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Azure 原則中的法規遵循

Azure 原則中的法規遵循，會提供內建的計畫定義，以根據責任（_客戶_、 _Microsoft_、_共用_）來查看**控制項**和**合規性網域**的清單。
針對 Microsoft 負責的控制項，我們會根據協力廠商證明和我們的執行詳細資料，提供我們的審核結果的其他詳細資料，以達成該合規性。
Microsoft 負責的控制項為 `type` [靜態](./definition-structure.md#type)。

> [!NOTE]
> 法規合規性是預覽功能。 若為已更新的內建，則計畫控制項會對應至相對應的相容性標準。 現有的合規性標準計畫正在進行更新，以支援法規遵循。

## <a name="regulatory-compliance-defined"></a>已定義法規合規性

法規合規性是以計畫定義的[群組](./initiative-definition-structure.md#policy-definition-groups)部分為基礎。 在內建中，計畫定義中的每個群組都會定義名稱（**控制**）、類別（**合規性網域**），並提供[policyMetadata](./initiative-definition-structure.md#metadata-objects)物件的參考，其中包含該**控制項**的相關資訊。 法規合規性計畫定義必須將 `category` 屬性設定為 [**法規合規性**]。 做為標準的計畫定義，法規合規性計畫支援[參數](./initiative-definition-structure.md#parameters)來建立動態指派。

客戶可以建立自己的法規合規性計畫。 這些定義可以是原始的，或從現有的內建定義複製而來。 如果使用內建的「法規合規性計畫定義」做為參考，建議您在[Azure 原則 GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance)存放庫中監視法規合規性定義的來源。

若要將自訂法規合規性計畫連結至您的 Azure 資訊安全中心儀表板，請參閱[Azure 資訊安全中心-使用自訂安全性原則](../../../security-center/custom-security-policies.md)。

## <a name="regulatory-compliance-in-portal"></a>入口網站中的法規合規性

當您使用[群組](./initiative-definition-structure.md#policy-definition-groups)建立計畫定義時，入口網站中針對該計畫的 [**合規性**詳細資料] 頁面會提供其他資訊。 

新的索引標籤會將**控制項**新增至頁面。 篩選是由**合規性網域**提供，原則定義則是依 `title` **policyMetadata**物件中的欄位分組。 每個資料列都代表一個**控制項**，顯示其合規性狀態、其所屬的**合規性領域**、責任資訊，以及有多少不符合規範且符合規範的原則定義組成該**控制項**。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="NIST SP 800-53 R4 內建定義的法規合規性總覽範例。":::

選取**控制項**會開啟該控制項的詳細資料頁面。 此**總覽**包含來自和的 `description` 資訊 `requirements` 。 在 [**原則**] 索引標籤下，是計畫中參與此**控制項**的所有個別原則定義。 [**資源合規性**] 索引標籤會提供每個資源的細微觀點，而此資源是由目前所查看**控制項**的成員原則所評估。

> [!NOTE]
> **Microsoft 管理**的評估類型適用于[靜態](./definition-structure.md#type)原則定義 `type` 。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="NIST SP 800-53 R4 內建定義的系統和通訊保護網域的界限保護控制中的法規合規性原則定義範例。":::

從相同的**控制項**頁面，變更為 [**資源相容性**] 索引標籤會顯示此**控制項**的原則定義所包含的所有資源。 篩選器適用于名稱或識別碼、合規性狀態、資源類型和位置。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="在 NIST SP 800-53 R4 內建定義的系統和通訊保護網域的界限保護控制中，受原則定義影響的法規合規性資源範例。":::

## <a name="regulatory-compliance-in-sdk"></a>SDK 中的法規合規性

如果已在計畫定義上啟用法規合規性，評估掃描記錄、事件和原則狀態 SDK 會各自傳回額外的屬性。 這些額外的屬性會依合規性狀態分組，並提供每個狀態中有多少群組的相關資訊。

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

## <a name="next-steps"></a>後續步驟

- 請參閱[計畫定義結構](./initiative-definition-structure.md)
- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱[了解原則效果](./effects.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
