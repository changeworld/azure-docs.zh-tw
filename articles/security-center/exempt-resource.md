---
title: 豁免資源、訂用帳戶、管理群組和安全分數的 Azure 資訊安全中心建議
description: 瞭解如何建立規則以豁免訂用帳戶或管理群組的安全性建議，並防止他們影響您的安全分數
author: memildin
ms.author: memildin
ms.date: 01/22/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 4012c7417345678717800f4fdede95947e00b828
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756776"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>從您的安全分數排除資源和建議 

每個安全性小組的核心優先順序，是要確保分析師可以專注于與組織相關的工作和事件。 「安全性中心」有許多自訂體驗的功能，並確定您的安全分數會反映貴組織的安全性優先權。 **豁免** 選項是其中一項功能。

當您調查 Azure 資訊安全中心中的安全性建議時，您複習的第一項資訊是受影響的資源清單。

有時候，系統會列出您認為不應該包含的資源。 或者，建議會顯示在您覺得不屬於的範圍中。 資源可能已由安全性中心未追蹤的進程補救。 建議對於特定訂用帳戶可能不適當。 或者，您的組織只決定接受與特定資源或建議相關的風險。

在這種情況下，您可以建立一項免除建議的豁免：

- **豁免資源** 以確保在未來未與狀況不良的資源一起列出，且不會影響您的安全分數。 資源將列為不適用，且原因會顯示為「豁免」，並顯示您所選取的特定理由。

- **豁免訂用帳戶或管理群組** ，以確保建議不會影響您的安全分數，未來將不會顯示該訂用帳戶或管理群組。 這與現有資源和您在未來建立的任何資源相關。 建議將會標示您針對所選範圍選取的特定理由。

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|預覽<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|定價：|這是免費提供給 Azure Defender 客戶的 Azure 原則功能，不需額外費用。 對於其他使用者，未來可能會收取費用。|
|必要的角色和權限：|建立豁免的訂用帳戶 **擁有** 者或 **原則參與者**<br>若要建立規則，您需要在 Azure 原則中編輯原則的許可權。<br>在 Azure 原則中深入瞭解 [AZURE RBAC 許可權](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)。|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||

## <a name="define-an-exemption"></a>定義豁免

若要微調「安全性中心」為您的訂用帳戶、管理群組或資源所做的安全性建議，您可以建立豁免規則，以：

- 標記特定的 **建議** 或「緩和」或「已接受風險」。 您可以針對訂用帳戶、多個訂用帳戶或整個管理群組建立建議豁免。
- 針對特定的建議，將 **一或多個資源** 標示為「緩和」或「已接受風險」。

> [!TIP]
> 您也可以使用 API 來建立豁免。 如需 JSON 範例以及相關結構的說明，請參閱 [Azure 原則豁免結構](../governance/policy/concepts/exemption-structure.md)。

若要建立豁免規則：

1. 開啟特定建議的 [建議詳細資料] 頁面。

1. 從頁面頂端的工具列中，選取 [ **豁免**]。

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="針對要從訂用帳戶或管理群組豁免的建議建立豁免規則。":::

1. 在 [ **豁免** ] 窗格中：
    1. 選取此豁免規則的範圍：
        - 如果您選取管理群組，則會從該群組內的所有訂用帳戶中豁免該建議
        - 如果您要建立此規則以豁免建議中的一或多個資源，請選擇 [選取的資源]，然後從清單中選取相關的資源

    1. 輸入此豁免規則的名稱。
    1. （選擇性）設定到期日。
    1. 選取豁免的類別：
        - **透過協力廠商解決 (降低)** –如果您使用的是未識別之安全性中心的協力廠商服務。 

            > [!NOTE]
            > 當您豁免建議時，您不會獲得安全分數的點數。 但因為不會 *移除* 狀況不良資源的點，所以結果會增加。

        - **(棄權) 所接受的風險** –如果您已決定接受未緩和此建議的風險
    1. （選擇性）輸入描述。
    1. 選取 [建立]  。

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="建立豁免規則以豁免訂用帳戶或管理群組中之建議的步驟":::

    當豁免生效時 (可能需要最多30分鐘的時間) ：
    - 建議或資源不會影響您的安全分數。
    - 如果您豁免特定資源，這些資源將會列在 [建議詳細資料] 頁面的 [ **不適用** ] 索引標籤中。
    - 如果您豁免建議，預設會在資訊安全中心的建議頁面上隱藏該建議。 這是因為該頁面上 **建議狀態** 篩選準則的預設選項是排除 **不適用** 的建議。 如果您豁免安全性控制中的所有建議也是如此。

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Azure 資訊安全中心的建議頁面上的預設篩選器會隱藏不適用的建議和安全性控制項":::

    - [建議詳細資料] 頁面頂端的資訊帶會更新豁免的資源數目：
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="豁免的資源數目":::

1. 若要檢查豁免的資源，請開啟 [ **不適用** ] 索引標籤：

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="修改豁免":::

    每項豁免的原因都包含在資料表 (1) 。

    若要修改或刪除豁免，請選取省略號功能表 ( "..." ) 如 (2) 所示。

1. 若要檢查您訂用帳戶上的所有豁免規則，請從資訊區域選取 [ **查看豁免** ]：

    > [!IMPORTANT]
    > 若要查看與某個建議相關的特定豁免，請根據相關的範圍和建議名稱篩選清單。

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure 原則的豁免頁面":::

    > [!TIP]
    > 或者，您也 [可以使用 Azure Resource Graph 來尋找豁免的建議](#find-recommendations-with-exemptions-using-azure-resource-graph)。

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>監視在訂用帳戶中建立的豁免

如本頁面稍早所述，豁免規則是功能強大的工具，可讓您更精確地控制對訂用帳戶和管理群組中的資源所造成的建議。 

為了追蹤您的使用者如何運用這項功能，我們建立了一個 Azure Resource Manager 的 (ARM) 範本，以部署邏輯應用程式腳本和所有必要的 API 連線，以在豁免建立時通知您。

- 若要深入瞭解腳本，請參閱[技術社區 blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)中的這篇文章
- 您會在[Azure 資訊安全中心 GitHub 存放庫](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)中找到 ARM 範本
- 您可以按一下 [這裡](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json) 來部署所有必要的元件 


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>使用 Azure Resource Graph 尋找豁免的建議

Azure Resource Graph (ARG) 可讓您透過強大的篩選、分組和排序功能，立即存取整個雲端環境中的資源資訊。 這是一種快速且有效率的方式，可透過程式設計方式或從 Azure 入口網站中查詢 Azure 訂用帳戶的資訊。

若要查看所有具有豁免規則的建議：

1. 開啟 **Azure Resource Graph Explorer**。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="啟動 Azure Resource Graph Explorer * * 建議頁面" :::

1. 輸入下列查詢，然後選取 [ **執行查詢**]。

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


深入瞭解下列頁面：
- [深入了解 Azure 資源群組](../governance/resource-graph/index.yml)。
- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)
- [Kusto 查詢語言 (KQL)](/azure/data-explorer/kusto/query/)





## <a name="exemption-rule-faq"></a>豁免規則常見問題

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>當有多個原則計畫中的一個建議時，會發生什麼事？

有時候，安全性建議會出現在一個以上的原則方案中。 如果您有多個相同建議的實例指派給相同的訂用帳戶，且您針對該建議建立了豁免，則會影響您有權編輯的所有計劃。 

例如，建議的 * * * * 是 Azure 資訊安全中心指派給所有 Azure 訂用帳戶的預設原則方案的一部分。 它也在 XXXXX 中。

如果您嘗試建立此建議的豁免，您將會看到下列兩則訊息之一：

- 如果您有編輯這兩個計畫的必要許可權，您將會看到：

    *這項建議包含在數個原則計畫中： [計畫名稱（以逗號分隔）]。所有的豁免都將建立。*  

- 如果您沒有這兩個方案的足夠許可權，您將會看到下列訊息：

    *您的許可權有限，無法在所有原則方案上套用豁免，只會在具有足夠許可權的方案上建立豁免。*


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何豁免建議的資源，使其不會影響您的安全分數。 如需安全分數的詳細資訊，請參閱：

- [Azure 資訊安全中心的安全分數](secure-score-security-controls.md)