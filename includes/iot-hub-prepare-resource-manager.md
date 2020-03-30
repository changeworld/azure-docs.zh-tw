---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 4eb794fa35164e3f86a5e3d6f67d446321f91f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67133074"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>準備驗證 Azure Resource Manager 要求
您必須使用 [Azure Resource Manager][lnk-authenticate-arm]搭配 Azure Active Directory (AD) 來驗證所有針對資源執行的作業。 最簡單的設定方式是使用 PowerShell 或 Azure CLI。

請先安裝 [Azure PowerShell Cmdlet][lnk-powershell-install] 再繼續。

下列步驟示範如何使用 PowerShell 設定 AD 應用程式的密碼驗證。 您可以在標準 PowerShell 工作階段中執行這些命令。

1. 使用下列命令來登入您的 Azure 訂用帳戶：

    ```powershell
    Connect-AzAccount
    ```

1. 如果您有多個 Azure 訂用帳戶，則登入 Azure 即會為您授與和您認證相關聯之所有 Azure 訂用帳戶的存取權。 使用下列命令來列出可供您使用的 Azure 訂用帳戶：

    ```powershell
    Get-AzSubscription
    ```

    使用下列命令，來選取您想要用來執行命令以管理 IoT 中樞的訂用帳戶。 您可以使用來自上一個命令之輸出內的訂用帳戶名稱或識別碼︰

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. 請記下您的 **TenantId** 和 **SubscriptionId**。 您稍後需要這些資訊。
3. 使用下列命令並取代預留位置，以建立新的 Azure Active Directory 應用程式：
   
   * **{Display name}：** 應用程式的顯示名稱，如 **MySampleApp**
   * **[主頁 URL]：** 應用主頁的 URL，如**HTTP：\//mysampleapp/home**。 此 URL 不需要指向實際的應用程式。
   * **[應用程式識別碼]：** 唯一識別碼，如**HTTP：\//mysampleapp**。 此 URL 不需要指向實際的應用程式。
   * **{Password}：** 您用來驗證應用程式的密碼。
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. 請記下建立之應用程式的 **ApplicationId** 。 您稍後需要此資訊。
5. 使用下列命令，並將 **{MyApplicationId}** 取代為上一個步驟的 **ApplicationId**，藉此建立新的服務主體：
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. 使用下列命令，並將 **{MyApplicationId}** 取代為 **ApplicationId**，藉此設定角色指派。
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

您已建立可從自訂 C# 應用程式驗證的 Azure AD 應用程式。 在本教學課程後續的內容當中，您將需要以下各值：

* TenantId
* SubscriptionId
* ApplicationId
* 密碼

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: /powershell/azure/install-az-ps
