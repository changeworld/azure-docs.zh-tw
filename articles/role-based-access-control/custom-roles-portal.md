---
title: 使用 Azure 門戶（預覽） 創建或更新 Azure 自訂角色 - Azure RBAC
description: 瞭解如何使用 Azure 門戶為基於 Azure 角色的存取控制 （Azure RBAC） 創建 Azure 自訂角色。 這包括如何列出、建立、更新及刪除自訂角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.openlocfilehash: 3204cdf51f3f37588f684f801a811f569b337d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77674866"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>使用 Azure 門戶創建或更新 Azure 自訂角色（預覽）

> [!IMPORTANT]
> 使用 Azure 門戶的 Azure 自訂角色當前處於公共預覽中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果[Azure 內置角色](built-in-roles.md)不能滿足組織的特定需求，則可以創建自己的 Azure 自訂角色。 與內置角色一樣，您可以將自訂角色指派給訂閱和資源組作用域中的使用者、組和服務主體。 自訂角色是存放在 Azure Active Directory (Azure AD) 目錄中，而且可以跨訂用帳戶共用。 每個目錄最多可以有 5000 個自訂角色。 可以使用 Azure 門戶、Azure PowerShell、Azure CLI 或 REST API 創建自訂角色。 本文介紹如何使用 Azure 門戶（當前預覽版）創建自訂角色。

## <a name="prerequisites"></a>Prerequisites

若要建立自訂角色，您需要：

- 建立自訂角色的權限，例如[擁有者](built-in-roles.md#owner)或[使用者存取管理員](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>第 1 步：確定所需的許可權

Azure 具有數千個許可權，您可以將其包含在自訂角色中。 以下是確定要添加到自訂角色的許可權的四種方法：

| 方法 | 描述 |
| --- | --- |
| 查看現有角色 | 您可以查看現有角色，查看正在使用的許可權。 有關詳細資訊，請參閱[Azure 內置角色](built-in-roles.md)。 |
| 按關鍵字搜索許可權 | 使用 Azure 門戶創建自訂角色時，可以按關鍵字搜索許可權。 例如，您可以搜索*虛擬機器*或*計費*許可權。 此搜索功能在[步驟 4： 許可權](#step-4-permissions)中稍後描述。 |
| 下載擁有權限 | 使用 Azure 門戶創建自訂角色時，可以將擁有權限下載為 CSV 檔，然後搜索此檔。 在"**添加許可權"** 窗格中，按一下"**下載擁有權限**"按鈕以下載擁有權限。 有關"添加許可權"窗格的詳細資訊，請參閱[步驟 4：許可權](#step-4-permissions)。 |
| 查看文檔中的許可權 | 您可以在[Azure 資源管理器資源管理器資源管理器操作](resource-provider-operations.md)中查看可用許可權。 |

## <a name="step-2-choose-how-to-start"></a>第 2 步：選擇如何開始

有三種方法可以開始創建自訂角色。 您可以克隆現有角色、從頭開始或從 JSON 檔開始。 最簡單的方法是查找具有您所需的大部分許可權的現有角色，然後為您的方案克隆和修改它。 

### <a name="clone-a-role"></a>克隆角色

如果現有角色沒有所需的許可權，則可以克隆它，然後修改許可權。 按照以下步驟開始克隆角色。

1. 在 Azure 門戶中，打開希望自訂角色可分配的訂閱或資源組，然後打開**訪問控制項 （IAM）。**

    以下螢幕截圖顯示了為訂閱打開的存取控制 （IAM） 頁面。

    ![訂閱的存取控制 （IAM） 頁](./media/custom-roles-portal/access-control-subscription.png)

1. 按一下 [角色]**** 索引標籤以查看所有內建與自訂角色清單。

1. 搜索要克隆的角色，如計費閱讀器角色。

1. 在行的末尾，按一下省略號 （**...），** 然後按一下 **"克隆**"。

    ![克隆內容功能表](./media/custom-roles-portal/clone-menu.png)

    這將打開自訂角色編輯器，並選擇 **"克隆"角色**選項。

1. 繼續[步驟 3：基礎知識](#step-3-basics)。

### <a name="start-from-scratch"></a>從頭開始

如果您願意，可以按照以下步驟從頭開始自訂角色。

1. 在 Azure 門戶中，打開希望自訂角色可分配的訂閱或資源組，然後打開**訪問控制項 （IAM）。**

1. 按一下 **"添加**"，然後按一下"**添加自訂角色（預覽）"。**

    ![添加自訂角色功能表](./media/custom-roles-portal/add-custom-role-menu.png)

    這將打開自訂角色編輯器，並選擇"**從頭開始**"選項。

1. 繼續[步驟 3：基礎知識](#step-3-basics)。

### <a name="start-from-json"></a>從 JSON 開始

如果您願意，可以在 JSON 檔中指定大多數自訂角色值。 您可以在自訂角色編輯器中打開該檔，進行其他更改，然後創建自訂角色。 按照以下步驟從 JSON 檔開始。

1. 創建具有以下格式的 JSON 檔：

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. 在 JSON 檔中，指定各種屬性的值。 下面是添加了一些值的示例。 有關不同屬性的資訊，請參閱[瞭解角色定義](role-definitions.md)。

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. 在 Azure 門戶中，打開**訪問控制項 （IAM）** 頁。

1. 按一下 **"添加**"，然後按一下"**添加自訂角色（預覽）"。**

    ![添加自訂角色功能表](./media/custom-roles-portal/add-custom-role-menu.png)

    這將打開自訂角色編輯器。

1. 在"基本"選項卡上，在 **"基線"許可權**中，選擇**從 JSON 開始**。

1. 在"選擇檔框"旁邊，按一下資料夾按鈕以打開"打開"對話方塊。

1. 選擇您的 JSON 檔，然後按一下 **"打開**"。

1. 繼續[步驟 3：基礎知識](#step-3-basics)。

## <a name="step-3-basics"></a>第 3 步：基礎知識

在 **"基本"** 選項卡上，您可以指定自訂角色的名稱、說明和基線許可權。

1. 在 **"自訂角色名稱"** 框中，為自訂角色指定名稱。 該名稱對於 Azure AD 目錄必須是唯一的。 名稱可以包括字母、數位、空格和特殊字元。

1. 在 **"描述"** 框中，為自訂角色指定可選說明。 這將成為自訂角色的工具提示。

    應根據上一步設置 **"基線許可權**"選項，但您可以更改。

    ![指定值的基礎知識選項卡](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>第 4 步：許可權

在 **"許可權"** 選項卡上，您可以指定自訂角色的許可權。 根據是克隆角色還是從 JSON 開始，"許可權"選項卡可能已經列出了一些許可權。

![創建自訂角色的許可權選項卡](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>添加或刪除許可權

按照以下步驟添加或刪除自訂角色的許可權。

1. 要添加許可權，請按一下"**添加許可權**"以打開"添加許可權"窗格。

    此窗格列出以卡片格式分組到不同類別的所有可用許可權。 每個類別表示*一個資來源提供者*，它是提供 Azure 資源的服務。

1. 在 **"搜索許可權**"框中，鍵入字串以搜索許可權。 例如，搜索*發票*以查找與發票相關的許可權。

    資來源提供者卡的清單將根據您的搜索字串顯示。 有關資來源提供者如何映射到 Azure 服務的清單，請參閱[Azure 服務的資來源提供者](../azure-resource-manager/management/azure-services-resource-providers.md)。

    ![使用資來源提供者添加許可權窗格](./media/custom-roles-portal/add-permissions-provider.png)

1. 按一下可能具有要添加到自訂角色的許可權的資源供應商卡片，例如**Microsoft 計費**。

    根據搜索字串顯示該資來源提供者的管理許可權清單。

    ![添加許可權清單](./media/custom-roles-portal/add-permissions-list.png)

1. 如果要查找應用於資料平面的許可權，請按一下"**資料操作**"。 否則，將操作切換設置為 **"操作"** 以列出應用於管理平面的許可權。 有關詳細資訊，請參閱管理平面和資料平面之間的差異，請參閱[管理和資料操作](role-definitions.md#management-and-data-operations)。

1. 如有必要，請更新搜索字串以進一步優化搜索。

1. 找到要添加到自訂角色的一個或多個許可權後，請在許可權旁邊添加核取記號。 例如，在 **"其他：下載發票"** 旁邊添加核取記號以添加下載發票的許可權。

1. 按一下"**添加**"可將許可權添加到許可權清單中。

    許可權將添加為`Actions`或 。 `DataActions`

    ![已添加許可權](./media/custom-roles-portal/permissions-list-add.png)

1. 要刪除許可權，請按一下行末尾的刪除圖示。 在此示例中，由於使用者不需要創建支援票證的能力，因此可以刪除`Microsoft.Support/*`許可權。

### <a name="add-wildcard-permissions"></a>添加萬用字元許可權

根據選擇開始的方式，在許可權清單中可能具有萬用字元 （\*） 的許可權。 萬用字元\*（ ） 將許可權擴展到與您提供的字串匹配的所有內容。 例如，假設您要添加與 Azure 成本管理和匯出相關的擁有權限。 您可以添加所有這些許可權：

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

您可以添加萬用字元許可權，而不是添加所有這些許可權。 例如，以下萬用字元許可權等效于前五個許可權。 這還包括將來可能添加的任何匯出許可權。

```
Microsoft.CostManagement/exports/*
```

如果要添加新的萬用字元許可權，則無法使用"**添加許可權"** 窗格添加該許可權。 要添加萬用字元許可權，必須使用**JSON**選項卡手動添加它。有關詳細資訊，請參閱步驟[6：JSON](#step-6-json)。

### <a name="exclude-permissions"></a>排除許可權

如果您的角色具有萬用字元 （\*） 許可權，並且希望從該萬用字元許可權中排除或減去特定許可權，則可以排除這些許可權。 例如，假設您具有以下萬用字元許可權：

```
Microsoft.CostManagement/exports/*
```

如果不想允許刪除匯出，可以排除以下刪除許可權：

```
Microsoft.CostManagement/exports/delete
```

排除許可權時，將將其添加為`NotActions`或`NotDataActions`。 有效的管理許可權是通過添加所有 ，`Actions`然後減去 所有 。 `NotActions` 通過添加所有 ，`DataActions`然後減去 所有 ，`NotDataActions`計算有效的資料許可權。

> [!NOTE]
> 排除許可權與拒絕不同。 排除許可權只是從萬用字元許可權中減去許可權的便捷方法。

1. 要從允許的萬用字元許可權中排除或減去許可權，請按一下"**排除許可權**"以打開"排除許可權"窗格。

    在此窗格中，指定排除或減去的管理或資料許可權。

1. 找到要排除的一個或多個許可權後，請在許可權旁邊添加核取記號，然後按一下"**添加**"按鈕。

    ![排除許可權窗格 - 選擇許可權](./media/custom-roles-portal/exclude-permissions-select.png)

    許可權將添加為`NotActions`或`NotDataActions`。

    ![排除許可權](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>第 5 步：可分配作用域

在"**可分配作用域"** 選項卡上，可以指定自訂角色可用於分配的位置，例如訂閱或資源組。 根據您選擇的開始方式，此選項卡可能會列出打開訪問控制項 （IAM） 頁的範圍。 不支援將可分配的範圍設置為根作用域（"/"）。 對於此預覽，不能將管理組添加為可分配作用域。

1. 按一下"**添加可分配作用域**"以打開"添加可分配作用域"窗格。

    ![可分配作用域選項卡](./media/custom-roles-portal/assignable-scopes.png)

1. 按一下要使用的一個或多個作用域，通常是您的訂閱。

    ![添加可分配作用域](./media/custom-roles-portal/add-assignable-scopes.png)

1. 按一下"**添加**"按鈕可添加可分配作用域。

## <a name="step-6-json"></a>第 6 步：JSON

在 **"JSON"** 選項卡上，您可以看到在 JSON 中格式化的自訂角色。 如果需要，可以直接編輯 JSON。 如果要添加萬用字元 （\*） 許可權，則必須使用此選項卡。

1. 要編輯 JSON，請按一下"**編輯**"。

    ![顯示自訂角色的 JSON 選項卡](./media/custom-roles-portal/json.png)

1. 對 JSON 進行更改。

    如果 JSON 的格式不正確，您將在垂直排水溝中看到一條紅色鋸齒線和一個指示器。

1. 完成編輯後，按一下"**保存**"。

## <a name="step-7-review--create"></a>第 7 步：查看 + 創建

在 **"審閱 + 創建"** 選項卡上，您可以查看自訂角色設置。

1. 查看自訂角色設置。

    ![[檢閱 + 建立] 索引標籤](./media/custom-roles-portal/review-create.png)

1. 按一下"**創建**"以創建自訂角色。

    幾分鐘後，將顯示一個訊息方塊，指示您的自訂角色已成功創建。

    ![創建自訂角色消息](./media/custom-roles-portal/custom-role-success.png)

    如果檢測到任何錯誤，將顯示一條消息。

    ![查看 = 創建錯誤](./media/custom-roles-portal/review-create-error.png)

1. 在 **"角色**"清單中查看新的自訂角色。 如果看不到自訂角色，請按一下"**刷新**"。

     自訂角色可能需要幾分鐘時間才能顯示到所有位置。

## <a name="list-custom-roles"></a>列出自訂角色

按照以下步驟查看自訂角色。

1. 打開訂閱或資源組，然後打開**存取控制 （IAM）。**

1. 按一下 [角色]**** 索引標籤以查看所有內建與自訂角色清單。

1. 在 **"類型"** 清單中，選擇 **"自訂角色"** 以僅查看自訂角色。

    如果您剛剛創建了自訂角色，但清單中未看到它，請按一下"**刷新**"。

    ![自訂角色清單](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>更新自訂角色

1. 如本文前面所述，打開自訂角色清單。

1. 按一下要更新的自訂角色的省略號 （**...），** 然後按一下"**編輯**"。 請注意，您無法更新內置角色。

    自訂角色在編輯器中打開。

    ![自訂角色功能表](./media/custom-roles-portal/edit-menu.png)

1. 使用不同的選項卡更新自訂角色。

1. 完成更改後，按一下"審閱 +**創建**"選項卡以查看更改。

1. 按一下 **"更新**"按鈕以更新自訂角色。

## <a name="delete-a-custom-role"></a>刪除自訂角色

1. 如本文前面所述，打開自訂角色清單。

1. 刪除使用自訂角色的任何角色指派。

1. 按一下要刪除的自訂角色的省略號 （**...），** 然後按一下"**刪除**"。

    ![自訂角色功能表](./media/custom-roles-portal/delete-menu.png)

    完全刪除自訂角色可能需要幾分鐘時間。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure PowerShell 建立自訂角色](tutorial-custom-role-powershell.md)
- [Azure 中的自訂角色](custom-roles.md)
- [Azure 資源管理器資源管理器提供程式操作](resource-provider-operations.md)
