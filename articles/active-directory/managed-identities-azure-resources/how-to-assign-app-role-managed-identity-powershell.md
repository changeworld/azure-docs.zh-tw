---
title: 使用 PowerShell 將受控識別指派給應用程式角色-Azure AD
description: 使用 PowerShell 將受控識別存取權指派給另一個應用程式角色的逐步指示。
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: 409ba7a954830bb2370ce83989b9e8b08b742fe7
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631171"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>使用 PowerShell 將受控識別存取權指派給應用程式角色

適用于 Azure 資源的受控識別會在 Azure Active Directory 中提供具有身分識別的 Azure 服務。 它們的運作方式不需要您程式碼中的認證。 Azure 服務會使用此身分識別來向支援 Azure AD 驗證的服務進行驗證。 應用程式角色提供一種以角色為基礎的存取控制，並允許服務執行授權規則。

在本文中，您將瞭解如何使用 Azure AD PowerShell，將受控識別指派給另一個應用程式所公開的應用程式角色。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。 **請務必檢閱 [系統指派和使用者指派受控識別之間的差異](overview.md#managed-identity-types)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行範例指令碼，您有兩個選項：
    - 使用您可以使用程式碼區塊右上角的 [**試試看**] 按鈕開啟的 [Azure Cloud Shell](../../cloud-shell/overview.md)。
    - 安裝最新版的 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)以在本機執行腳本。

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>將受控識別存取權指派給另一個應用程式的應用程式角色

1. 在 Azure 資源（ [例如 AZURE VM](qs-configure-powershell-windows-vm.md)）上啟用受控識別。

1. 尋找受控識別之服務主體的物件識別碼。

   **針對系統指派的受控識別**，您可以在資源的 [身分 **識別** ] 頁面上，找到 AZURE 入口網站上的物件識別碼。 您也可以使用下列 PowerShell 腳本來尋找物件識別碼。 您將需要在步驟1中建立之資源的資源識別碼，您可以在資源的 [ **屬性** ] 頁面上的 [Azure 入口網站] 中取得該資源識別碼。

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **針對使用者指派的受控識別**，您可以在資源的 [ **總覽** ] 頁面上找到 Azure 入口網站的受控識別物件識別碼。 您也可以使用下列 PowerShell 腳本來尋找物件識別碼。 您將需要使用者指派的受控識別的資源識別碼。

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. 建立新的應用程式註冊，以代表您的受控識別將要求傳送至的服務。 如果公開應用程式角色授與受控識別的 API 或服務，在您的 Azure AD 租使用者中已經有服務主體，請略過此步驟。 例如，如果您想要將受控識別存取權授與 Microsoft Graph API，您可以略過此步驟。

1. 尋找服務應用程式服務主體的物件識別碼。 您可以使用 Azure 入口網站來尋找。 移至 Azure Active Directory 並開啟 [ **企業應用程式** ] 頁面，然後尋找應用程式並尋找 **物件識別碼**。 您也可以使用下列 PowerShell 腳本，依據其顯示名稱來尋找服務主體的物件識別碼：

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > 應用程式的顯示名稱不是唯一的，因此您應該確認是否取得正確的應用程式服務主體。

1. 將 [應用程式角色](../develop/howto-add-app-roles-in-azure-ad-apps.md) 新增至您在步驟3中建立的應用程式。 您可以使用 Azure 入口網站或使用 Microsoft Graph 來建立角色。 例如，您可以新增應用程式角色，如下所示：

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. 將應用程式角色指派給受控識別。 您將需要下列資訊來指派應用程式角色：
    * `managedIdentityObjectId`：受控識別之服務主體的物件識別碼，您可以在步驟2中找到此識別碼。
    * `serverServicePrincipalObjectId`：伺服器應用程式服務主體的物件識別碼，您可以在步驟4中找到此識別碼。
    * `appRoleId`：由伺服器應用程式公開的應用程式角色識別碼，您在步驟5中所產生的識別碼-在範例中，應用程式角色識別碼為 `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` 。
   
   執行下列 PowerShell 腳本以新增角色指派：

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>完整的指令碼

此範例腳本說明如何將 Azure web 應用程式的受控識別指派給應用程式角色。

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>下一步

- [適用於 Azure 資源的受控識別概觀](overview.md)
- 若要在 Azure VM 上啟用受控識別，請參閱[使用 PowerShell 在 Azure VM 上設定 Azure 資源的受控識別](qs-configure-powershell-windows-vm.md)。
