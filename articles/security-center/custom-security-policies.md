---
title: 在 Azure 資訊安全中心建立自訂安全性原則 | Microsoft Docs
description: Azure 資訊安全中心監控的 Azure 自訂原則定義。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 12/03/2020
ms.author: memildin
ms.openlocfilehash: 8d2b43ab57ea7a3b1dc1d13bcdea9932ccecb9dc
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559026"
---
# <a name="using-custom-security-policies"></a>使用自訂安全性原則

為了協助您保護系統和環境，Azure 資訊安全中心會產生安全性建議。 這些建議會以業界最佳做法為基礎，並併入提供給所有客戶的一般預設安全性原則中。 也可能來自資訊安全中心的業界和法規標準知識。

透過這項功能，您可以加入自己的「自訂」方案。 如果您的環境未遵循您所建立的原則，您就會收到建議。 您所建立的任何自訂方案都會與法規合規性儀表板中的內建方案一起出現，如[改善您的法規合規性](security-center-compliance-dashboard.md)教學課程中所述。

如[Azure 原則文件](../governance/policy/concepts/definition-structure.md#definition-location)中所述，當您指定自訂方案的位置時，其必須是管理群組或訂用帳戶。 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>將自訂方案新增至您的訂用帳戶 

1. 從資訊安全中心的提要欄位中，開啟 [安全性原則] 頁面。

1. 選取您想要對其新增自訂方案的訂用帳戶或管理群組。

    [![選取您要對其建立自訂原則的訂用帳戶](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > 您必須在訂用帳戶層級 (或更高層級) 上新增自訂標準，以便在資訊安全中心中評估和顯示這些標準。 
    >
    > 當您新增自訂標準時，其會將「方案」指派給該範圍。 因此，我們建議您為選取該指派所需的最大範圍。

1. 在 [安全性原則] 頁面的自訂方案底下，按一下 [新增自訂方案]。

    [![按一下 [新增自訂方案]](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    下列頁面隨即出現：

    ![建立或新增原則](media/custom-security-policies/create-or-add-custom-policy.png)

1. 在 [新增自訂方案] 頁面中，檢閱您組織中已建立的自訂原則清單。 如果您看到您想要指派給訂用帳戶的帳戶，請按一下 [新增]。 如果清單中沒有符合您需求的方案，請略過此步驟。

1. 若要建立新的自訂方案：

    1. 按一下 [新建]。
    1. 輸入定義的位置和名稱。
    1. 選取要包含的原則，然後按一下 [新增]。
    1. 輸入任何所需的參數。
    1. 按一下 [檔案] 。
    1. 在 [新增自訂方案] 頁面中，按一下 [重新整理]。 您的新方案將會顯示為可用。
    1. 按一下 [新增]，並將其指派給您的訂用帳戶。

    > [!NOTE]
    > 建立新的方案需要訂用帳戶擁有者認證。 如需有關 Azure 角色的詳細資訊，請參閱 [Azure 資訊安全中心內的權限](security-center-permissions.md)。

    您的新方案將會生效，而且您可以透過下列兩種方式看到影響：

    * 在資訊安全中心提要欄位中的 [原則與合規性] 底下，選取 [法規合規性]。 合規性儀表板會隨即開啟，並顯示新的自訂方案及內建方案。
    
    * 如果您的環境未遵循您所定義的原則，您將會開始收到建議。

1. 若要查看針對您原則所產生的建議，請按一下提要欄位中的 [建議] 來開啟建議頁面。 建議會隨著「自訂」標籤顯示，並大約會在一小時內提供。

    [![自訂建議](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>使用詳細資訊增強您的自訂建議

隨著 Azure 資訊安全中心提供的內建建議會包含詳細資料，例如嚴重性層級和補救指示。 如果您想要將這類資訊新增至您的自訂建議，使其出現在 Azure 入口網站或任何您存取建議的位置，您將需要使用 REST API。 

您可以新增兩種類型的資訊：

- **RemediationDescription** – 字串
- **Severity** – Enum [低, 中, 高]

針對屬於自訂方案一部份的原則，您應將中繼資料新增至原則定義。 其應該位於 ‘securityCenter’ 屬性中，如下所示：

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
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
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
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

如需使用 securityCenter 屬性的另一個範例，請參閱 [REST API 文件的這一節](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)。


## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何建立自訂安全性原則。 

如需其他相關內容，請參閱下列文章： 

- [安全性原則概觀](tutorial-security-policy.md)
- [內建安全性原則的清單](./policy-reference.md)