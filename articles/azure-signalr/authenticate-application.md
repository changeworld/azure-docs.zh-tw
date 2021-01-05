---
title: 驗證應用程式以存取 Azure SignalR Service
description: 本文提供有關使用 Azure Active Directory 來驗證應用程式的資訊 Azure SignalR Service
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 97386b18360e22b457dbcdda53c4f81e7d4ed272
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797482"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>使用 Azure Active Directory 來驗證應用程式，以存取 Azure SignalR Service
Microsoft Azure 針對以 Azure Active Directory (Azure AD) 為基礎的資源和應用程式提供了整合式的存取控制管理功能。 使用 Azure AD 搭配 Azure SignalR Service 的主要優點是您不再需要將認證儲存在程式碼中。 相反地，您可以從 Microsoft 身分識別平臺要求 OAuth 2.0 存取權杖。 要求權杖的資源名稱是 `https://signalr.azure.com/` 。 Azure AD 會 (應用程式、資源群組或服務主體) 執行應用程式來驗證安全性主體。 如果驗證成功，Azure AD 會將存取權杖傳回給應用程式，然後應用程式就可以使用存取權杖來授權 Azure SignalR Service 資源的要求。

將角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取權的範圍可以是訂用帳戶、資源群組或 Azure SignalR 資源的等級。 Azure AD 安全性可將角色指派給使用者、群組、應用程式服務主體或 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。 

> [!NOTE]
> 角色定義是一個權限集合。 以角色為基礎的存取控制 (RBAC) 控制如何透過角色指派來強制執行這些許可權。 角色指派由三項元素所組成：安全性主體、角色定義和範圍。 如需詳細資訊，請參閱 [瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>向 Azure AD 租用戶註冊應用程式
使用 Azure AD 來授權 Azure SignalR Service 資源的第一個步驟，是向 [Azure 入口網站](https://portal.azure.com/)的 Azure AD 租使用者註冊您的應用程式。 當您註冊應用程式時，會將應用程式的相關資訊提供給 AD。 Azure AD 接著提供用戶端識別碼 (也稱為應用程式識別碼) ，您可以用來將應用程式與 Azure AD 執行時間產生關聯。 若要深入了解用戶端識別碼，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。 

下列影像顯示註冊 web 應用程式的步驟：

![註冊應用程式](./media/authenticate/app-registrations-register.png)

> [!Note]
> 如果您將應用程式註冊為原生應用程式，可以為重新導向 URI 指定任何有效的 URI。 若為原生應用程式，此值不需要是實際的 URL。 針對 web 應用程式，重新導向 URI 必須是有效的 URI，因為它會指定提供權杖的 URL。

註冊您的應用程式之後，您會在 [**設定**] 底下看到 **應用程式 (用戶端) 識別碼**：

![已註冊應用程式的應用程式識別碼](./media/authenticate/application-id.png)

如需有關向 Azure AD 註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../active-directory/develop/quickstart-register-app.md)。


### <a name="create-a-client-secret"></a>建立用戶端密碼   
應用程式需要用戶端密碼，才能在要求權杖時證明其身分識別。 若要新增用戶端密碼，請遵循下列步驟。

1. 在 Azure 入口網站中，流覽至您的應用程式註冊。
1. 選取 **憑證 & 秘密** 設定。
1. 在 [ **用戶端密碼**] 下，選取 [ **新增用戶端密碼** ] 以建立新的密碼。
1. 提供秘密的描述，並選擇想要的到期間隔。
1. 立即將新密碼的值複製到安全的位置。 填滿值只會顯示一次。

![建立用戶端密碼](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>上傳憑證

您也可以上傳認證，而不是建立用戶端密碼。

![上傳認證](./media/authenticate/certification.png)

## <a name="add-rbac-roles-using-the-azure-portal"></a>使用 Azure 入口網站新增 RBAC 角色  
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

## <a name="sample-codes-while-configuring-your-app-server"></a>設定應用程式伺服器時的範例程式碼。

當下列情況時，請新增下列選項 `AddAzureSignalR` ：

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

或直接 `ConnectionString` 在您的檔案中設定 `appsettings.json` 。

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

使用時 `certificate` ，請將變更 `clientSecret` 為， `clientCert` 如下所示：

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>後續步驟
- 若要深入瞭解 RBAC，請參閱 [什麼是 AZURE rbac)  (azure 角色型存取控制 ](../role-based-access-control/overview.md)？
- 若要瞭解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派及管理 Azure 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure Resource Manager 範本管理角色型存取控制 (RBAC) ](../role-based-access-control/role-assignments-template.md)

請參閱下列相關文章：
- [使用 Azure Active Directory 來驗證受控識別，以存取 Azure SignalR Service](authenticate-managed-identity.md)
- [使用 Azure Active Directory 授權存取 Azure SignalR Service](authorize-access-azure-active-directory.md)