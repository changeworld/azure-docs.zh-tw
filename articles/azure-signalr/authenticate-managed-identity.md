---
title: 使用 Azure Active Directory 驗證受控識別
description: 本文提供有關使用 Azure Active Directory 存取受控識別來驗證的資訊 Azure SignalR Service
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 46d66451bb8f2cd6c5d4448131b5f4842a728fd0
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797497"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>使用 Azure Active Directory 來驗證受控識別，以存取 Azure SignalR 資源
Azure SignalR Service 支援使用 [適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別 Azure Active Directory (Azure AD) 驗證。 適用于 Azure 資源的受控識別可以從 Azure 虛擬機器中執行的應用程式（ (Vm) 、函式應用程式、虛擬機器擴展集和其他服務）使用 Azure AD 認證，來授權存取 Azure SignalR Service 資源。 藉由使用適用于 Azure 資源的受控識別搭配 Azure AD authentication，您可以避免將認證儲存在雲端中執行的應用程式。

本文說明如何使用 Azure VM 中的受控識別，來授權 Azure SignalR Service 的存取權。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上啟用受控識別
您必須先在 VM 上啟用 Azure 資源的受控識別，才能使用 Azure 資源的受控識別從您的 VM 授權 Azure SIgnalR Service 資源。 若要了解如何啟用 Azure 資源的受控識別，請參閱下列其中一篇文章：

- [Azure 入口網站](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 範本](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 用戶端程式庫](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>在 Azure AD 中將許可權授與受控識別
若要授權要求從應用程式中的受控識別進行 Azure SignalR Service，請先針對該受控識別 (RBAC) 設定來設定角色型存取控制。 Azure SignalR Service 會定義包含取得或之許可權的 RBAC 角色 `AccessKey` `ClientToken` 。 當 RBAC 角色指派給受控識別時，受控識別會被授與適當範圍內 Azure SignalR Service 的存取權。

如需指派 RBAC 角色的詳細資訊，請參閱 [使用 Azure Active Directory 進行驗證，以存取 Azure SignalR Service 資源](authorize-access-azure-active-directory.md)。

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>使用受控識別連接到 Azure SignalR Service
若要使用受控識別來連線到 Azure SignalR Service，您必須將角色和適當的範圍指派給身分識別。 本節中的程式會使用以受控身分識別執行的簡單應用程式，並存取 Azure SignalR Service 資源。

在這裡，我們會使用範例 Azure 虛擬機器資源。

1. 移至 [ **設定** ]，然後選取 [身分 **識別**]。 
1. 選取要 **開啟** 的 **狀態**。 
1. 選取 [儲存]  以儲存設定。 

    ![虛擬機器的受控識別](./media/authenticate/identity-virtual-machine.png)

一旦您啟用此設定，就會在 Azure Active Directory (Azure AD) 中建立新的服務識別，並將其設定為 App Service 主機。

現在，將此服務識別指派給您 Azure SignalR Service 資源中所需範圍內的角色。

## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 入口網站指派 RBAC 角色  
若要深入瞭解如何使用 RBAC 和 Azure 入口網站來管理 Azure 資源的存取權，請參閱 [這篇文章](..//role-based-access-control/role-assignments-portal.md)。 

確定角色指派的適當範圍之後，請流覽至 Azure 入口網站中的該資源。 顯示資源的存取控制 (IAM) 設定，並遵循下列指示來管理角色指派：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，流覽至您的 SignalR 資源。
1. 選取 [ **存取控制] (IAM)** 以顯示 Azure SignalR 的存取控制設定。 
1. 選取 [角色指派] 索引標籤，以查看角色指派的清單。 選取工具列上的 [ **新增** ] 按鈕，然後選取 [ **新增角色指派**]。 

    ![工具列上的 [新增] 按鈕](./media/authenticate/role-assignments-add-button.png)

1. 在 [ **新增角色指派** ] 頁面上，執行下列步驟：
    1. 選取您要指派的 **Azure SignalR 角色** 。 
    1. 搜尋以找出您要指派角色 (使用者、群組、服務主體) 的 **安全性主體** 。
    1. 選取 [ **儲存** ] 以儲存角色指派。 

        ![將角色指派給應用程式](./media/authenticate/assign-role-to-application.png)

    1. 您對其指派角色的身分識別會出現在該角色下方。 例如，下圖顯示該應用程式， `signalr-dev` 並 `signalr-service` 在 SignalR 應用程式伺服器角色中。 
        
        ![角色指派清單](./media/authenticate/role-assignment-list.png)

您可以依照類似的步驟，指派範圍為資源群組或訂用帳戶的角色。 定義角色及其範圍之後，您可以使用 [此 GitHub 位置中](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)的範例來測試此行為。

## <a name="samples-code-while-configuring-your-app-server"></a>設定應用程式伺服器時的範例程式碼

當下列情況時，請新增下列選項 `AddAzureSignalR` ：

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>後續步驟
- 若要深入瞭解 RBAC，請參閱 [什麼是 AZURE rbac)  (azure 角色型存取控制 ](../role-based-access-control/overview.md)？
- 若要瞭解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派及管理 Azure 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure Resource Manager 範本管理角色型存取控制 (RBAC) ](../role-based-access-control/role-assignments-template.md)

請參閱下列相關文章：
- [使用 Azure Active Directory 來驗證應用程式，以存取 Azure SignalR Service](authenticate-application.md)
- [使用 Azure Active Directory 授權存取 Azure SignalR Service](authorize-access-azure-active-directory.md)