---
title: 豁免 Azure 資訊安全中心安全性建議和安全分數的資源
description: 瞭解如何從安全性建議和安全分數豁免資源
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b4d043812800bec1070624382755aeb355ea595a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934731"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>豁免建議和安全分數的資源

每個安全性小組的核心優先順序，是要確保分析師可以專注于與組織相關的工作和事件。 資訊安全中心有許多功能可供您自訂最高優先順序的資訊，並確保您的安全分數是您組織安全性決策的有效反映。 排除資源就是其中一項功能。

當您調查 Azure 資訊安全中心中的安全性建議時，您複習的第一項資訊是受影響的資源清單。

有時候，系統會列出您認為不應該包含的資源。 它可能已由安全性中心未追蹤的進程補救。 或者，您的組織已決定接受該特定資源的風險。 

在這種情況下，您可以建立豁免規則，並確定未來不會列出資源和狀況不良的資源，且不會影響您的安全分數。 

資源將列為不適用，且原因會顯示為「豁免」，並顯示您所選取的理由。

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|預覽|
|定價：|這是免費提供給 Azure Defender 客戶的 Azure 原則功能，不需額外費用。 對於其他使用者，未來可能會收取費用。|
|必要的角色和許可權：|建立豁免的訂用帳戶**擁有**者或**原則參與者**<br>若要建立規則，您需要在 Azure 原則中編輯原則的許可權。<br>在 Azure 原則中深入瞭解 [RBAC 許可權](../governance/policy/overview.md#rbac-permissions-in-azure-policy)。|
|雲端：|![Yes](./media/icons/yes-icon.png) 商業雲端<br>![No](./media/icons/no-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||


## <a name="create-an-exemption-rule"></a>建立豁免規則

1. 在狀況不良的資源清單中，針對您要豁免的資源選取省略號功能表 ( [...] ) 。

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="從內容功能表建立豁免選項":::

    [建立豁免] 窗格隨即開啟。

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="建立豁免窗格":::

1. 輸入您的準則，然後選取應豁免此資源的準則：
    - 緩和-此問題與**資源無關，** 因為它是由不同于所建議的工具或進程所處理。
    - **棄權** -接受此資源的風險
1. 選取 [儲存]。
1. 在一段時間後 (可能需要24小時的時間) ：
    - 資源不會影響您的安全分數。
    - 資源會列在 [建議詳細資料] 頁面的 [ **不適用** ] 索引標籤中
    - [建議詳細資料] 頁面頂端的資訊區域會列出豁免的資源數目：
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="豁免的資源數目":::

1. 若要檢查豁免的資源，請開啟 [ **不適用** ] 索引標籤。

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="修改豁免":::

    表格中包含每個豁免的原因。 

    若要修改或刪除豁免，請選取省略號功能表 ( "..." ) 如 (2) 所示。


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>查看您訂用帳戶上的所有豁免規則

豁免規則會使用 Azure 原則來建立原則指派的資源豁免。

您可以使用 Azure 原則來追蹤 **豁免** 頁面中的所有豁免：

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure 原則的豁免頁面":::



## <a name="next-steps"></a>下一步

在本文中，您已瞭解如何豁免建議的資源，使其不會影響您的安全分數。 如需安全分數的詳細資訊，請參閱：

- [Azure 資訊安全中心的安全分數](secure-score-security-controls.md)