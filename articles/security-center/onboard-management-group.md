---
title: 將管理群組上線到 Azure 資訊安全中心
description: 了解如何使用提供的 Azure 原則定義，為管理群組中的所有訂用帳戶啟用 Azure 資訊安全中心。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: memildin
ms.openlocfilehash: 4ecd436b548c29c520a7538970d4d703cc8488d2
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027562"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>在管理群組中的所有訂用帳戶上啟用資訊安全中心

您可以使用 Azure 原則，在相同管理群組 (MG) 內的所有 Azure 訂用帳戶上啟用 Azure 資訊安全中心。 這比從入口網站個別進行存取更方便，而且即使訂用帳戶屬於不同擁有者也能運作。 

若要將管理群組與其所有訂用帳戶上線：

1. 身為具有 [安全性管理員] 權限的使用者，請開啟 Azure 原則並搜尋 [在您的訂用帳戶上啟用 Azure 資訊安全中心] 定義。

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="Azure 原則定義、在您的訂用帳戶上啟用 Azure 資訊安全中心":::

1. 選取 [指派] 並確保您將範圍設定為 MG 層級。

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="指派定義、在您的訂用帳戶上啟用 Azure 資訊安全中心":::

    > [!TIP]
    > 除了範圍以外，沒有必要的參數。

1. 選取 [建立補救工作] 以確保所有未啟用資訊安全中心的現有訂用帳戶都會上線。

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="為 Azure 原則定義建立補救工作、在您的訂用帳戶上啟用 Azure 資訊安全中心":::

1. 指派定義後，其將會：

    1. 偵測 MG 中尚未向資訊安全中心註冊的所有訂用帳戶。
    1. 將這些訂用帳戶標示為「不符合規範」。
    1. 將所有已註冊的訂用帳戶標示為「符合規範」(不論已開啟或關閉 Azure Defender)。

    然後補救工作會在不符合規範的訂用帳戶上，免費啟用資訊安全中心。

> [!IMPORTANT]
> 原則定義只會在 **現有的** 訂用帳戶上啟用資訊安全中心。 若要註冊新建立的訂用帳戶，請開啟 [相容性] 索引標籤，選取相關的不合規訂用帳戶，然後建立補救工作。如果您有一或多個要利用資訊安全中心監視的新訂用帳戶，請重複此步驟。

## <a name="optional-modifications"></a>選擇性修改

有各種方式可供您選擇來修改 Azure 原則定義： 

- **以不同的方式定義合規性** - 提供的原則會將 MG 中尚未向資訊安全中心註冊的所有訂用帳戶歸類為「不符合規範」。 您可以選擇將其設定為不具 Azure Defender 的所有訂用帳戶。

    提供的定義會將以下任何一個「定價」設定定義為符合規範。 表示設定為 'standard' 或 'free' 的訂用帳戶都符合規範。

    > [!TIP]
    > 啟用 Azure Defender 方案後，就會將其描述為 'Standard' 設定。 若已停用，則是 'Free'。 [深入了解 Azure Defender 方案](security-center-pricing.md)。

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    如果您將其變更為下列內容，則只有設定為 'standard' 的訂用帳戶會歸類為符合規範：

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **定義要在啟用資訊安全中心時套用的一些 Azure Defender 方案** - 提供的原則可啟用資訊安全中心，而不需要任何選用 Azure Defender 方案。 您可選擇啟用其中一或多個。

    提供的定義 `deployment` 區段具有參數 `pricingTier`。 根據預設，這會設定為 `free`，但您可加以修改。 


## <a name="next-steps"></a>後續步驟：

既然您已將整個管理群組上線，請啟用 Azure Defender 的進階保護。 

> [!div class="nextstepaction"]
> [啟用 Azure Defender](security-center-pricing.md)
