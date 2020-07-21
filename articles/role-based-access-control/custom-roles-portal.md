---
title: 使用 Azure 入口網站建立或更新 Azure 自訂角色-Azure RBAC
description: 瞭解如何使用 Azure 入口網站和 Azure 角色型存取控制（Azure RBAC）來建立 Azure 自訂角色。 這包括如何列出、建立、更新及刪除自訂角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2020
ms.author: rolyon
ms.openlocfilehash: 91d2605dddd6107d09e635969f5e5d98c2a02d60
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511716"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>使用 Azure 入口網站建立或更新 Azure 自訂角色 (機器翻譯)

如果[Azure 內建角色](built-in-roles.md)不符合您組織的特定需求，您可以建立自己的 Azure 自訂角色。 就像內建角色一樣，您可以將自訂角色指派給訂用帳戶和資源群組範圍中的使用者、群組和服務主體。 自訂角色是存放在 Azure Active Directory (Azure AD) 目錄中，而且可以跨訂用帳戶共用。 每個目錄最多可以有5000個自訂角色。 您可以使用 Azure 入口網站、Azure PowerShell、Azure CLI 或 REST API 來建立自訂角色。 本文說明如何使用 Azure 入口網站建立自訂角色。

## <a name="prerequisites"></a>先決條件

若要建立自訂角色，您需要：

- 建立自訂角色的權限，例如[擁有者](built-in-roles.md#owner)或[使用者存取管理員](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>步驟1：決定您需要的許可權

Azure 有數以千計的許可權，您可以將其包含在您的自訂角色中。 以下四種方式可讓您決定要新增至自訂角色的許可權：

| 方法 | 描述 |
| --- | --- |
| 查看現有的角色 | 您可以查看現有的角色，以查看所使用的許可權為何。 如需詳細資訊，請參閱[Azure 內建角色](built-in-roles.md)。 |
| 依關鍵字搜尋許可權 | 當您使用 Azure 入口網站建立自訂角色時，您可以依關鍵字搜尋許可權。 例如，您可以搜尋*虛擬機器*或*帳單*許可權。 本搜尋功能稍後會在[步驟4：許可權](#step-4-permissions)中說明。 |
| 下載擁有權限 | 當您使用 Azure 入口網站建立自訂角色時，您可以下載擁有權限作為 CSV 檔案，然後搜尋此檔案。 在 [**新增許可權**] 窗格上，按一下 [**下載擁有權限**] 按鈕以下載擁有權限。 如需 [新增許可權] 窗格的詳細資訊，請參閱[步驟4：許可權](#step-4-permissions)。 |
| 在檔中查看許可權 | 您可以在[Azure Resource Manager 資源提供者作業](resource-provider-operations.md)中，查看可用的許可權。 |

## <a name="step-2-choose-how-to-start"></a>步驟2：選擇啟動方式

有三種方式可讓您開始建立自訂角色。 您可以複製現有的角色、從頭開始，或從 JSON 檔案開始。 最簡單的方式是尋找擁有您所需之大部分許可權的現有角色，然後針對您的案例進行複製和修改。 

### <a name="clone-a-role"></a>複製角色

如果現有的角色沒有您所需的許可權，您可以複製它，然後修改許可權。 請遵循下列步驟來開始複製角色。

1. 在 [Azure 入口網站中，開啟您想要讓自訂角色可供指派的訂用帳戶或資源群組，然後開啟 [**存取控制（IAM）**]。

    下列螢幕擷取畫面顯示針對訂用帳戶開啟的 [存取控制（IAM）] 頁面。

    ![訂用帳戶的 [存取控制（IAM）] 頁面](./media/custom-roles-portal/access-control-subscription.png)

1. 按一下 [角色]**** 索引標籤以查看所有內建與自訂角色清單。

1. 搜尋您想要複製的角色，例如「帳單讀者」角色。

1. 在資料列結尾處，按一下省略號（**...**），然後按一下 [**複製**]。

    ![複製內容功能表](./media/custom-roles-portal/clone-menu.png)

    這會開啟 [自訂角色編輯器]，並選取 [**複製角色**] 選項。

1. 繼續進行[步驟3：基本概念](#step-3-basics)。

### <a name="start-from-scratch"></a>從頭開始

如果您想要的話，可以依照下列步驟從頭開始自訂角色。

1. 在 [Azure 入口網站中，開啟您想要讓自訂角色可供指派的訂用帳戶或資源群組，然後開啟 [**存取控制（IAM）**]。

1. 按一下 [**新增**]，然後按一下 [**新增自訂角色**]。

    ![[新增自訂角色] 功能表](./media/custom-roles-portal/add-custom-role-menu.png)

    這會開啟 [自訂角色編輯器]，並選取 [**從頭開始**] 選項。

1. 繼續進行[步驟3：基本概念](#step-3-basics)。

### <a name="start-from-json"></a>從 JSON 啟動

如果您想要的話，也可以在 JSON 檔案中指定大部分的自訂角色值。 您可以在 [自訂角色編輯器] 中開啟檔案、進行其他變更，然後建立自訂角色。 請遵循下列步驟來開始使用 JSON 檔案。

1. 建立具有下列格式的 JSON 檔案：

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

1. 在 JSON 檔案中，指定各種屬性的值。 以下是新增一些值的範例。 如需不同屬性的相關資訊，請參閱[瞭解 Azure 角色定義](role-definitions.md)。

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
    
1. 在 [Azure 入口網站中，開啟 [**存取控制（IAM）** ] 頁面。

1. 按一下 [**新增**]，然後按一下 [**新增自訂角色**]。

    ![[新增自訂角色] 功能表](./media/custom-roles-portal/add-custom-role-menu.png)

    這會開啟 [自訂角色編輯器]。

1. 在 [基本] 索引標籤的 [**基準許可權**] 中，選取 [**從 JSON 啟動**]。

1. 在 [選取檔案] 方塊旁，按一下 [資料夾] 按鈕以開啟 [開啟] 對話方塊。

1. 選取您的 JSON 檔案，然後按一下 [**開啟**]。

1. 繼續進行[步驟3：基本概念](#step-3-basics)。

## <a name="step-3-basics"></a>步驟3：基本概念

在 [**基本**] 索引標籤上，您可以指定自訂角色的名稱、描述和基準許可權。

1. 在 [**自訂角色名稱**] 方塊中，指定自訂角色的名稱。 Azure AD 目錄的名稱必須是唯一的。 名稱可以包含字母、數位、空格和特殊字元。

1. 在 [**描述**] 方塊中，指定自訂角色的選擇性描述。 這會成為自訂角色的工具提示。

    [**基準許可權**] 選項應該已經根據上一個步驟來設定，但是您可以變更。

    ![具有指定值的 [基本] 索引標籤](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>步驟4：許可權

在 [**許可權**] 索引標籤上，您可以指定自訂角色的許可權。 視您是否複製角色或以 JSON 啟動而定，[許可權] 索引標籤可能已經列出某些許可權。

![建立自訂角色的 [許可權] 索引標籤](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>新增或移除許可權

請遵循下列步驟來新增或移除自訂角色的許可權。

1. 若要新增許可權，請按一下 [**新增許可權**] 以開啟 [新增許可權] 窗格。

    此窗格會列出以卡片格式分組到不同類別目錄的所有可用許可權。 每個類別都代表一個*資源提供者*，這是提供 Azure 資源的服務。

1. 在 [**搜尋許可權**] 方塊中，輸入要搜尋許可權的字串。 例如，搜尋*發票*以尋找與發票相關的許可權。

    系統會根據您的搜尋字串顯示資源提供者卡片清單。 如需資源提供者如何對應至 Azure 服務的清單，請參閱[azure 服務的資源提供者](../azure-resource-manager/management/azure-services-resource-providers.md)。

    ![具有資源提供者的 [新增許可權] 窗格](./media/custom-roles-portal/add-permissions-provider.png)

1. 按一下可能會有您想要新增至自訂角色之許可權的資源提供者卡，例如**Microsoft 計費**。

    系統會根據您的搜尋字串，顯示該資源提供者的管理許可權清單。

    ![新增許可權清單](./media/custom-roles-portal/add-permissions-list.png)

1. 如果您要尋找適用于資料平面的許可權，請按一下 [**資料動作**]。 否則，請將 [動作] 切換設定為 [**動作**]，以列出適用于管理平面的許可權。 如需有關管理平面和資料平面之間差異的詳細資訊，請參閱[管理和資料作業](role-definitions.md#management-and-data-operations)。

1. 如有需要，請更新搜尋字串以進一步精簡您的搜尋。

1. 一旦您找到您想要新增至自訂角色的一或多個許可權，請在許可權旁新增核取記號。 例如，在 [**其他：下載發票**] 旁新增核取記號，以新增下載發票的許可權。

1. 按一下 [**新增**]，將許可權新增至您的許可權清單。

    許可權會加入為 `Actions` 或 `DataActions` 。

    ![已新增許可權](./media/custom-roles-portal/permissions-list-add.png)

1. 若要移除許可權，請按一下資料列結尾處的 [刪除] 圖示。 在此範例中，由於使用者不需要建立支援票證的能力，因此 `Microsoft.Support/*` 可以刪除許可權。

### <a name="add-wildcard-permissions"></a>新增萬用字元許可權

視您選擇要啟動的方式而定，您 `*` 在許可權清單中可能會有萬用字元（）的許可權。 萬用字元（ `*` ）會將許可權延伸到符合您所提供之動作字串的所有專案。 例如，下列萬用字元字串會加入與 Azure 成本管理和匯出相關的擁有權限。 這也包括未來可能會新增的任何匯出許可權。

```
Microsoft.CostManagement/exports/*
```

如果您想要加入新的萬用字元許可權，就無法使用 [**新增許可權**] 窗格來新增它。 若要新增萬用字元許可權，您必須使用 [ **JSON** ] 索引標籤手動新增它。如需詳細資訊，請參閱[步驟6： JSON](#step-6-json)。

### <a name="exclude-permissions"></a>排除許可權

如果您的角色具有萬用字元（ `*` ）許可權，而您想要排除或減去該萬用字元許可權的特定許可權，您可以將其排除。 例如，假設您有下列萬用字元許可權：

```
Microsoft.CostManagement/exports/*
```

如果您不想要允許刪除匯出，您可以排除下列刪除許可權：

```
Microsoft.CostManagement/exports/delete
```

當您排除許可權時，會將它新增為 `NotActions` 或 `NotDataActions` 。 有效的管理許可權是藉由新增所有 `Actions` ，然後減去所有的來計算 `NotActions` 。 有效的資料許可權是藉由新增所有 `DataActions` ，然後減去所有的來計算 `NotDataActions` 。

> [!NOTE]
> 排除許可權與拒絕不相同。 排除許可權只是從萬用字元許可權減去許可權的便利方式。

1. 若要從允許的萬用字元許可權中排除或減去許可權，請按一下 [**排除許可權**] 以開啟 [排除許可權] 窗格。

    在此窗格中，您可以指定要排除或減去的管理或資料許可權。

1. 一旦找到您想要排除的一或多個許可權，請在許可權旁新增核取記號，然後按一下 [**新增**] 按鈕。

    ![排除許可權窗格-選取的許可權](./media/custom-roles-portal/exclude-permissions-select.png)

    許可權會加入為 `NotActions` 或 `NotDataActions` 。

    ![已排除許可權](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>步驟5：可指派的範圍

在 [可指派的**範圍**] 索引標籤上，您可以指定自訂角色可用於指派的位置，例如訂用帳戶或資源群組。 根據您選擇要啟動的方式而定，此索引標籤可能會列出您開啟 [存取控制（IAM）] 頁面的範圍。 不支援將可指派的範圍設定為根範圍（"/"）。 目前，您無法將管理群組新增為可指派的範圍。

1. 按一下 [**新增可指派的範圍**] 以開啟 [新增可指派的範圍] 窗格。

    ![可指派的範圍索引標籤](./media/custom-roles-portal/assignable-scopes.png)

1. 按一下您要使用的一或多個範圍，通常是您的訂用帳戶。

    ![新增可指派的範圍](./media/custom-roles-portal/add-assignable-scopes.png)

1. 按一下 [**新增**] 按鈕以新增可指派的範圍。

## <a name="step-6-json"></a>步驟6： JSON

在 [ **json** ] 索引標籤上，您會看到以 JSON 格式格式化的自訂角色。 如有需要，您可以直接編輯 JSON。 如果您想要新增萬用字元（ `*` ）許可權，則必須使用此索引標籤。

1. 若要編輯 JSON，請按一下 [**編輯**]。

    ![顯示自訂角色的 [JSON] 索引標籤](./media/custom-roles-portal/json.png)

1. 對 JSON 進行變更。

    如果 JSON 的格式不正確，您會在垂直裝訂邊中看到紅色的鋸齒狀線條和指標。

1. 完成編輯時，按一下 [**儲存**]。

## <a name="step-7-review--create"></a>步驟7：審查 + 建立

在 [**審查 + 建立**] 索引標籤上，您可以檢查您的自訂角色設定。

1. 檢查您的自訂角色設定。

    ![[檢閱 + 建立] 索引標籤](./media/custom-roles-portal/review-create.png)

1. 按一下 [**建立**] 以建立您的自訂角色。

    幾分鐘後，會出現訊息方塊，指出已成功建立您的自訂角色。

    ![建立自訂角色訊息](./media/custom-roles-portal/custom-role-success.png)

    如果偵測到任何錯誤，將會顯示一則訊息。

    ![審核 + 建立錯誤](./media/custom-roles-portal/review-create-error.png)

1. 在 [**角色**] 清單中查看新的自訂角色。 如果您沒有看到您的自訂角色，**請按一下 [** 重新整理]。

     可能需要幾分鐘的時間，您的自訂角色才會出現在所有位置。

## <a name="list-custom-roles"></a>列出自訂角色

請遵循下列步驟來查看您的自訂角色。

1. 開啟訂用帳戶或資源群組，然後開啟 **[存取控制（IAM）**]。

1. 按一下 [角色]**** 索引標籤以查看所有內建與自訂角色清單。

1. 在 [**類型**] 清單中，選取 [ **CustomRole** ]，只查看您的自訂角色。

    如果您剛建立自訂角色，但在清單中沒有看到它，請**按一下 [** 重新整理]。

    ![自訂角色清單](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>更新自訂角色

1. 如本文稍早所述，開啟您的自訂角色清單。

1. 針對您想要更新的自訂角色，按一下省略號（**...**），然後按一下 [**編輯**]。 請注意，您無法更新內建角色。

    自訂角色會在編輯器中開啟。

    ![自訂角色功能表](./media/custom-roles-portal/edit-menu.png)

1. 使用不同的索引標籤來更新自訂角色。

1. 當您完成變更後，請按一下 [**審查 + 建立**] 索引標籤來審查您的變更。

1. 按一下 [**更新**] 按鈕，以更新您的自訂角色。

## <a name="delete-a-custom-role"></a>刪除自訂角色

1. 如本文稍早所述，開啟您的自訂角色清單。

1. 移除任何使用自訂角色的角色指派。

1. 針對您要刪除的自訂角色，按一下省略號（**...**），然後按一下 [**刪除**]。

    ![自訂角色功能表](./media/custom-roles-portal/delete-menu.png)

    可能需要幾分鐘的時間才能完全刪除您的自訂角色。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure PowerShell 建立 Azure 自訂角色](tutorial-custom-role-powershell.md)
- [Azure 自訂角色](custom-roles.md) (機器翻譯)
- [Azure Resource Manager 資源提供者作業](resource-provider-operations.md) (機器翻譯)
