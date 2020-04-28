---
title: 在 Azure 資訊安全中心中建立自訂安全性原則 |Microsoft Docs
description: 由 Azure 資訊安全中心監視的 Azure 自訂原則定義。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: memildin
ms.openlocfilehash: d703ea38c39ed556102271ac0cf9a609ce449bc3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195913"
---
# <a name="using-custom-security-policies"></a>使用自訂安全性原則

為了協助保護您的系統和環境，Azure 資訊安全中心會產生安全性建議。 這些建議是以業界最佳作法為基礎，並併入提供給所有客戶的一般預設安全性原則中。 它們也可以來自資訊安全中心的產業和法規標準知識。

有了這項功能，您就可以加入自己的*自訂*方案。 如果您的環境未遵循您所建立的原則，您就會收到建議。 您所建立的任何自訂計畫都會與法規合規性儀表板中的內建方案一起出現，如[改善您的法規合規性](security-center-compliance-dashboard.md)教學課程中所述。

如[Azure 原則檔](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location)中所述，當您指定自訂方案的位置時，它必須是管理群組或訂用帳戶。 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>將自訂方案新增至您的訂用帳戶 

1. 從資訊安全中心的提要欄位中，開啟 [**安全性原則**] 頁面。

1. 選取您想要新增自訂方案的訂用帳戶或管理群組。

    [![選取您要建立自訂原則的訂用帳戶](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > 您必須在訂用帳戶層級（或更新版本）新增自訂標準，以便在資訊安全中心中進行評估和顯示。 
    >
    > 當您新增自訂標準時，它會將*方案*指派給該範圍。 因此，我們建議您選取該指派所需的最寬範圍。

1. 在 [安全性原則] 頁面的自訂方案底下，按一下 [**新增自訂方案**]。

    [![按一下 [新增自訂方案]](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    隨即會出現下列頁面：

    ![建立或新增原則](media/custom-security-policies/create-or-add-custom-policy.png)

1. 在 [新增自訂計畫] 頁面中，檢查您的組織中已建立的自訂原則清單。 如果您看到您想要指派給訂用帳戶的帳戶，請按一下 [**新增**]。 如果清單中沒有符合您需求的計畫，請略過此步驟。

1. 若要建立新的自訂方案：

    1. 按一下 **[新建]**。
    1. 輸入定義的 [位置] 和 [名稱]。
    1. 選取要包含的原則，然後按一下 [**新增**]。
    1. 輸入任何所需的參數。
    1. 按一下 [檔案]  。
    1. 在 [新增自訂計畫] 頁面中，按一下 [重新整理]。 您的新方案將會顯示為 [可用]。
    1. 按一下 [**新增**]，並將它指派給您的訂用帳戶。

    > [!NOTE]
    > 建立新的方案需要訂用帳戶擁有者認證。 如需 Azure 角色的詳細資訊，請參閱[Azure 資訊安全中心中的許可權](security-center-permissions.md)。

    您的新計畫會生效，而且您可以看到下列兩種方式的影響：

    * 從 [資訊安全中心] 提要欄位的 [原則 & 相容性] 底下，選取 [**法規合規性**]。 [合規性儀表板] 隨即開啟，以顯示新的自訂計畫與內建的方案。
    
    * 如果您的環境未遵循您所定義的原則，您將會開始收到建議。

1. 若要查看您的原則所產生的建議，請按一下提要欄位中的 [**建議**] 以開啟 [建議] 頁面。 建議會顯示「自訂」標籤，並在大約一小時內提供。

    [![自訂建議](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

## <a name="enhancing-your-custom-recommendations-with-detailed-information"></a>使用詳細資訊來增強您的自訂建議

Azure 資訊安全中心提供的內建建議包含詳細資料，例如嚴重性層級和補救指示。 如果您想要將這類資訊新增至您的自訂建議，使其出現在 Azure 入口網站或任何您存取建議的位置，您將需要使用 REST API。 

您可以新增兩種類型的資訊：

- **RemediationDescription** –字串
- **嚴重性**–列舉 [Low、Medium、High]

針對屬於自訂計畫的原則，應將中繼資料新增至原則定義。 它應該位於 ' securityCenter ' 屬性中，如下所示：

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High",
    },
```

以下是自訂原則的範例，包括 metadata/securityCenter 屬性：

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "remediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "severity": "High",
        },
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

如需使用 securityCenter 屬性的另一個範例，請參閱[REST API 檔的這一節](https://docs.microsoft.com/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)。


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何建立自訂安全性原則。 

如需其他相關內容，請參閱下列文章： 

- [安全性原則的總覽](tutorial-security-policy.md)
- [內建安全性原則的清單](security-center-policy-definitions.md)