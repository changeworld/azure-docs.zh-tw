---
title: 授予創建 Azure 企業版訂閱的許可權
description: 了解如何授與使用者或服務主體以程式設計方式建立 Azure 企業版訂用帳戶的權限。
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478873"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>授與建立 Azure 企業版訂用帳戶的權限 (預覽)

因為您是 [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 上的 Azure 客戶，所以可以授與另一位使用者或服務主體建立由您帳戶付費的訂用帳戶。 在本文中，您將了解如何使用[角色型存取控制 (RBAC)](../../active-directory/role-based-access-control-configure.md)來共用建立訂用帳戶的能力，以及稽核訂用帳戶建立的方法。 您對於要共用的帳戶必須具有「擁有者」角色。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>授與存取權

要[在註冊帳戶下創建訂閱](programmatically-create-subscription.md)，使用者必須具有該帳戶上的[RBAC 擁有者角色](../../role-based-access-control/built-in-roles.md#owner)。 您可以按照以下步驟向註冊帳戶上的 RBAC 擁有者角色授予使用者或一組使用者：

1. 獲取要授予存取權限的註冊帳戶的物件識別碼

    要向其他人授予註冊帳戶上的 RBAC 擁有者角色，您必須是帳戶擁有者或帳戶的 RBAC 擁有者。

    # <a name="rest"></a>[休息](#tab/rest)

    請求列出您有權訪問的所有註冊帳戶：

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

    使用`principalName`該屬性標識要授予 RBAC 擁有者存取權限的帳戶。 `name`複製該帳戶。 例如，如果要授予 RBAC 擁有者對SignUpEngineering@contoso.com註冊帳戶的存取權限，則可以複製```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 這是註冊帳戶的物件識別碼。 將此值粘貼到某處，以便在下一步中將其用作`enrollmentAccountObjectId`。

    # <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

    使用 [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) Cmdlet 來列出您可以存取的所有註冊帳戶。 選擇 **"嘗試"** 以打開[Azure 雲外殼](https://shell.azure.com/)。 要粘貼代碼，請按右鍵 shell 視窗，然後選擇"**粘貼**"。

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure 使用有權訪問的註冊帳戶清單進行回應：

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    使用`principalName`該屬性標識要授予 RBAC 擁有者存取權限的帳戶。 `ObjectId`複製該帳戶。 例如，如果要授予 RBAC 擁有者對SignUpEngineering@contoso.com註冊帳戶的存取權限，則可以複製```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 將此物件識別碼 粘貼到某處，以便在下一步中將其用作`enrollmentAccountObjectId`。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    使用 [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 命令來列出您可以存取的所有註冊帳戶。 選擇 **"嘗試"** 以打開[Azure 雲外殼](https://shell.azure.com/)。 要粘貼代碼，請按右鍵 shell 視窗，然後選擇"**粘貼**"。

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure 使用有權訪問的註冊帳戶清單進行回應：

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

    使用`principalName`該屬性標識要授予 RBAC 擁有者存取權限的帳戶。 `name`複製該帳戶。 例如，如果要授予 RBAC 擁有者對SignUpEngineering@contoso.com註冊帳戶的存取權限，則可以複製```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 這是註冊帳戶的物件識別碼。 將此值粘貼到某處，以便在下一步中將其用作`enrollmentAccountObjectId`。

1. <a id="userObjectId"></a>獲取要將 RBAC 擁有者角色授予的使用者或組的物件識別碼

    1. 在 Azure 門戶中，在**Azure 活動目錄**上搜索 。
    1. 如果要授予使用者存取權限，請按一下左側功能表中的 **"使用者**"。 如果要授予對組的許可權，請按一下"**組**"。
    1. 選擇要授予 RBAC 擁有者角色的使用者或組。
    1. 如果選擇了"使用者"，您將在"設定檔"頁中找到物件識別碼。 如果選擇了組，則物件識別碼 將位於"概述"頁中。 通過按一下文字方塊右側的圖示複製**ObjectID。** 將其粘貼到某處，以便在下一步中將其用作`userObjectId`。

1. 授予註冊帳戶上的使用者或組 RBAC 擁有者角色

    使用前兩個步驟中收集的值，授予註冊帳戶上的使用者或組 RBAC 擁有者角色。

    # <a name="rest"></a>[休息](#tab/rest-2)

    運行以下命令，替換為```<enrollmentAccountObjectId>```第一步`name`中複製的命令 （）。```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` 替換為```<userObjectId>```從第二步複製的物件識別碼。

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

    # <a name="powershell"></a>[電源外殼](#tab/azure-powershell-2)

    運行以下[New-AzRole 分配](../../active-directory/role-based-access-control-manage-access-powershell.md)命令，```<enrollmentAccountObjectId>```替換為第`ObjectId`一步 （）```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```中收集的命令 。 替換為```<userObjectId>```第二步中收集的物件識別碼。

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    運行以下[az 角色指派創建](../../active-directory/role-based-access-control-manage-access-azure-cli.md)命令，```<enrollmentAccountObjectId>```替換為`name`第一步 （ 中的```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```複製命令 ）。 替換為```<userObjectId>```第二步中收集的物件識別碼。

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    使用者成為註冊帳戶的 RBAC 擁有者後，他們可以[以程式設計方式在註冊帳戶下創建訂閱](programmatically-create-subscription.md)。 由委派使用者創建的訂閱仍具有原始帳戶擁有者作為服務管理員，但預設情況下，該訂閱還具有委派的使用者為 RBAC 擁有者。

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
* 要瞭解有關使用管理組管理大量訂閱的更多資訊，請參閱使用[Azure 管理組組織資源](../../governance/management-groups/overview.md)
* 若要查看適用於大型組織在訂用帳戶治理上的完整最佳做法指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](/azure/architecture/cloud-adoption-guide/subscription-governance)
