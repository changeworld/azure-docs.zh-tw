---
title: 授與建立 Azure 企業版訂用帳戶的存取權
description: 了解如何授與使用者或服務主體以程式設計方式建立 Azure 企業版訂用帳戶的權限。
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75478873"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>授與建立 Azure 企業版訂用帳戶的權限 (預覽)

因為您是 [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 上的 Azure 客戶，所以可以授與另一位使用者或服務主體建立由您帳戶付費的訂用帳戶。 在本文中，您將了解如何使用[角色型存取控制 (RBAC)](../../active-directory/role-based-access-control-configure.md)來共用建立訂用帳戶的能力，以及稽核訂用帳戶建立的方法。 您對於要共用的帳戶必須具有「擁有者」角色。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>授與存取權

若要[在註冊帳戶底下建立訂閱](programmatically-create-subscription.md)，使用者必須擁有該帳戶的[RBAC 擁有者角色](../../role-based-access-control/built-in-roles.md#owner)。 您可以遵循下列步驟，將註冊帳戶上的 RBAC 擁有者角色授與使用者或使用者群組：

1. 取得您想要授與存取權之註冊帳戶的物件識別碼

    若要將註冊帳戶的 RBAC 擁有者角色授與其他人，您必須是帳戶擁有者或帳戶的 RBAC 擁有者。

    # <a name="rest"></a>[REST](#tab/rest)

    要求列出您有權存取的所有註冊帳戶：

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure 會以您可以存取的所有註冊帳戶清單來回應：

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    使用`principalName`屬性來識別您想要授與 RBAC 擁有者存取權的帳戶。 `name`複製該帳戶的。 例如，如果您想要將 RBAC 擁有者存取權授SignUpEngineering@contoso.com與註冊帳戶，您可以```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```複製。 這是註冊帳戶的物件識別碼。 將此值貼入某處，讓您可以在下一個步驟中`enrollmentAccountObjectId`使用它做為。

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    使用 [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) Cmdlet 來列出您可以存取的所有註冊帳戶。 選取 [**試試看**] 以開啟[Azure Cloud Shell](https://shell.azure.com/)。 若要貼上程式碼，請以滑鼠右鍵按一下 shell 視窗，然後選取 [**貼**上]。

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure 會以您可以存取的註冊帳戶清單來回應：

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    使用`principalName`屬性來識別您想要授與 RBAC 擁有者存取權的帳戶。 `ObjectId`複製該帳戶的。 例如，如果您想要將 RBAC 擁有者存取權授SignUpEngineering@contoso.com與註冊帳戶，您可以```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```複製。 將此物件識別碼貼入某處，讓您可以在下一個步驟中使用`enrollmentAccountObjectId`它做為。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    使用 [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 命令來列出您可以存取的所有註冊帳戶。 選取 [**試試看**] 以開啟[Azure Cloud Shell](https://shell.azure.com/)。 若要貼上程式碼，請以滑鼠右鍵按一下 shell 視窗，然後選取 [**貼**上]。

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure 會以您可以存取的註冊帳戶清單來回應：

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    使用`principalName`屬性來識別您想要授與 RBAC 擁有者存取權的帳戶。 `name`複製該帳戶的。 例如，如果您想要將 RBAC 擁有者存取權授SignUpEngineering@contoso.com與註冊帳戶，您可以```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```複製。 這是註冊帳戶的物件識別碼。 將此值貼入某處，讓您可以在下一個步驟中`enrollmentAccountObjectId`使用它做為。

1. <a id="userObjectId"></a>取得您想要授與 RBAC 擁有者角色之使用者或群組的物件識別碼

    1. 在 [Azure 入口網站中，搜尋**Azure Active Directory**。
    1. 如果您想要授與使用者存取權，請按一下左側功能表中的 [**使用者**]。 如果您想要授與群組的存取權，請按一下 [**群組**]。
    1. 選取您想要授與 RBAC 擁有者角色的使用者或群組。
    1. 如果您選取了使用者，您會在 [設定檔] 頁面中找到物件識別碼。 如果您已選取群組，物件識別碼會在 [總覽] 頁面中。 按一下文字方塊右邊的圖示來複製**ObjectID** 。 將此貼入某處，讓您可以在下一個步驟中`userObjectId`使用它做為。

1. 將註冊帳戶上的 RBAC 擁有者角色授與使用者或群組

    使用您在前兩個步驟中收集的值，將註冊帳戶上的 RBAC 擁有者角色授與使用者或群組。

    # <a name="rest"></a>[REST](#tab/rest-2)

    執行下列命令，將取代```<enrollmentAccountObjectId>```為您`name`在第一個步驟中複製的```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```（）。 將```<userObjectId>```取代為您從第二個步驟複製的物件識別碼。

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    順利在註冊帳戶範圍指派「擁有者」角色之後，Azure 會以角色指派資訊來回應：

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    執行下列[new-azroleassignment](../../active-directory/role-based-access-control-manage-access-powershell.md)命令，並將取代```<enrollmentAccountObjectId>```為第一個`ObjectId`步驟中所收集的（```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```）。 取代```<userObjectId>```為第二個步驟中所收集的物件識別碼。

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    執行下列[az role 指派 create](../../active-directory/role-based-access-control-manage-access-azure-cli.md)命令，並將```<enrollmentAccountObjectId>```取代為`name`您在第一個步驟中複製```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```的（）。 取代```<userObjectId>```為第二個步驟中所收集的物件識別碼。

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    一旦使用者成為您註冊帳戶的 RBAC 擁有者，他們就可以在其底下以程式設計[方式建立訂閱](programmatically-create-subscription.md)。 由委派使用者所建立的訂用帳戶，仍會以服務管理員的身分擁有原始的帳戶擁有者，但根據預設，它也會有委派的使用者做為 RBAC 擁有者。

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>使用活動記錄來稽核建立訂用帳戶的人員

若要追蹤透過此 API 建立的訂用帳戶，請使用[租用戶活動記錄 API](/rest/api/monitor/tenantactivitylogs)。 目前無法使用 PowerShell、CLI 或 Azure 入口網站來追蹤訂用帳戶的建立。

1. 以 Azure AD 租用戶的租用戶管理員身分[提高存取權](../../active-directory/role-based-access-control-tenant-admin-access.md)，然後將 `/providers/microsoft.insights/eventtypes/management`範圍的「讀者」角色指派給稽核使用者。
1. 以稽核使用者身分呼叫[租用戶活動記錄 API](/rest/api/monitor/tenantactivitylogs)，以查看訂用帳戶建立活動。 範例：

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

若要以便利的方式從命令列呼叫此 API，請嘗試 [ARMClient](https://github.com/projectkudu/ARMClient)。

## <a name="next-steps"></a>後續步驟

* 使用者或服務主體在獲得建立訂用帳戶的權限後，您便可使用該身分識別[以程式設計方式建立 Azure 企業版訂用帳戶](programmatically-create-subscription.md)。
* 如需有關使用 .NET 來建立訂用帳戶的範例，請參閱 [GitHub 上的範例程式碼](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core) \(英文\)。
* 若要深入了解 Azure Resource Manager 及其 API，請參閱 [Azure Resource Manager 概觀](overview.md)。
* 若要深入瞭解如何使用管理群組來管理大量訂用帳戶，請參閱[使用 Azure 管理群組來組織資源](../../governance/management-groups/overview.md)
* 若要查看適用於大型組織在訂用帳戶治理上的完整最佳做法指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](/azure/architecture/cloud-adoption-guide/subscription-governance)
