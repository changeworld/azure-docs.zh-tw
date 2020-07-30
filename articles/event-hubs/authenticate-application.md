---
title: 驗證應用程式以存取 Azure 事件中樞資源
description: 本文提供的資訊說明如何使用 Azure Active Directory 來驗證應用程式，以存取 Azure 事件中樞資源
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 37f76d99ac7f676b260b8a0e23f8a6e31792e835
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87413862"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>使用 Azure Active Directory 來驗證應用程式，以存取事件中樞資源
Microsoft Azure 針對以 Azure Active Directory (Azure AD) 為基礎的資源和應用程式提供了整合式的存取控制管理功能。 搭配 Azure 事件中樞使用 Azure AD 的主要優點是您不再需要將認證儲存在程式碼中。 相反地，您可以從 Microsoft 身分識別平臺要求 OAuth 2.0 存取權杖。 要求權杖的資源名稱是 `https://eventhubs.azure.net/` （針對 Kafka 用戶端，要求權杖的資源是 `https://<namespace>.servicebus.windows.net` ）。 Azure AD 會驗證執行應用程式的安全性主體（使用者、群組或服務主體）。 如果驗證成功，Azure AD 會將存取權杖傳回給應用程式，然後應用程式就可以使用存取權杖來授權 Azure 事件中樞資源的要求。

將角色指派給 Azure AD 的安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取權的範圍可以是訂用帳戶層級、資源群組、事件中樞命名空間，或其底下的任何資源。 Azure AD 安全性可以將角色指派給使用者、群組、應用程式服務主體或[Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。 

> [!NOTE]
> 角色定義是一個權限集合。 角色型存取控制（RBAC）會控制如何透過角色指派來強制執行這些許可權。 角色指派由三項元素所組成：安全性主體、角色定義和範圍。 如需詳細資訊，請參閱[瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="built-in-roles-for-azure-event-hubs"></a>Azure 事件中樞的內建角色
Azure 提供下列 Azure 內建角色，以使用 Azure AD 和 OAuth 來授權事件中樞資料的存取：

- [Azure 事件中樞資料擁有](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)者：使用此角色可提供完整的事件中樞資源存取權。
- [Azure 事件中樞的資料](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)傳送者：使用此角色可將存取權授與事件中樞資源。
- [Azure 事件中樞資料接收器](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)：使用此角色可授與事件中樞資源的接收存取權。   

> [!IMPORTANT]
> 我們的預覽版本支援將事件中樞資料存取權限新增至擁有者或參與者角色。 不過，「擁有者」和「參與者」角色的資料存取權限已不再遵守。 如果您使用的是「擁有者」或「參與者」角色，請切換到使用 Azure 事件中樞資料擁有者角色。

## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 入口網站指派 RBAC 角色  
若要深入瞭解如何使用 RBAC 和 Azure 入口網站來管理 Azure 資源的存取權，請參閱[這篇文章](..//role-based-access-control/role-assignments-portal.md)。 

在決定角色指派的適當範圍之後，請流覽至 Azure 入口網站中的該資源。 顯示資源的 [存取控制（IAM）] 設定，並遵循下列指示來管理角色指派：

> [!NOTE]
> 下面所述的步驟會將角色指派給事件中樞命名空間下的事件中樞，但是您可以遵循相同的步驟，指派範圍限於任何事件中樞資源的角色。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽到您的事件中樞命名空間。
2. 在 [**總覽**] 頁面上，選取您要為其指派角色的事件中樞。

    ![選取您的事件中樞](./media/authenticate-application/select-event-hub.png)
1. 選取 [**存取控制（IAM）** ] 以顯示事件中樞的存取控制設定。 
1. 選取 [角色指派] 索引標籤，以查看角色指派的清單。 選取工具列上的 [**新增**] 按鈕，然後選取 [**新增角色指派**]。 

    ![工具列上的 [新增] 按鈕](./media/authenticate-application/role-assignments-add-button.png)
1. 在 [**新增角色指派**] 頁面上，執行下列步驟：
    1. 選取您要指派的**事件中樞角色**。 
    1. 搜尋以找出您要為其指派角色的**安全性主體**（使用者、群組、服務主體）。
    1. 選取 [**儲存**] 以儲存角色指派。 

        ![將角色指派給使用者](./media/authenticate-application/assign-role-to-user.png)
    4. 您對其指派角色的身分識別會出現在該角色下方。 例如，下圖顯示 Azure-使用者是 Azure 事件中樞資料擁有者角色。 
        
        ![清單中的使用者](./media/authenticate-application/user-in-list.png)

您可以遵循類似的步驟，指派範圍為事件中樞命名空間、資源群組或訂用帳戶的角色。 定義角色及其範圍之後，您可以使用[此 GitHub 位置中](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)的範例來測試此行為。


## <a name="authenticate-from-an-application"></a>從應用程式進行驗證
搭配事件中樞使用 Azure AD 的主要優點是您的認證不再需要儲存在您的程式碼中。 相反地，您可以向 Microsoft 身分識別平臺要求 OAuth 2.0 存取權杖。 Azure AD 會驗證執行應用程式的安全性主體（使用者、群組或服務主體）。 如果驗證成功，Azure AD 會將存取權杖傳回給應用程式，然後應用程式就可以使用存取權杖來授權 Azure 事件中樞的要求。

下列各節說明如何設定原生應用程式或 web 應用程式，以使用 Microsoft 身分識別平臺2.0 進行驗證。 如需 Microsoft 身分識別平臺2.0 的詳細資訊，請參閱[microsoft 身分識別平臺（v2.0）總覽](../active-directory/develop/v2-overview.md)。

如需 OAuth 2.0 程式碼授與流程的概觀，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../active-directory/develop/v2-oauth2-auth-code-flow.md)。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>向 Azure AD 租用戶註冊應用程式
使用 Azure AD 來授權事件中樞資源的第一個步驟，是向[Azure 入口網站](https://portal.azure.com/)中的 Azure AD 租使用者註冊您的用戶端應用程式。 當您註冊用戶端應用程式時，會向 AD 提供應用程式的相關資訊。 Azure AD 接著會提供用戶端識別碼（也稱為應用程式識別碼），讓您用來將應用程式與 Azure AD 執行時間建立關聯。 若要深入了解用戶端識別碼，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。 

下列影像顯示註冊 web 應用程式的步驟：

![註冊應用程式](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 如果您將應用程式註冊為原生應用程式，您可以為重新導向 URI 指定任何有效的 URI。 對於原生應用程式，這個值不一定是真正的 URL。 對於 web 應用程式，重新導向 URI 必須是有效的 URI，因為它會指定提供權杖的 URL。

註冊應用程式之後，您會在 [**設定**] 底下看到**應用程式（用戶端）識別碼**：

![已註冊應用程式的應用程式識別碼](./media/authenticate-application/application-id.png)

如需有關向 Azure AD 註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../active-directory/develop/quickstart-register-app.md)。


### <a name="create-a-client-secret"></a>建立用戶端密碼   
應用程式需要用戶端密碼，才能在要求權杖時證明其身分識別。 若要新增用戶端密碼，請遵循下列步驟。

1. 在 Azure 入口網站中，流覽至您的應用程式註冊。
1. 選取 [**憑證 & 密碼**] 設定。
1. 在 [**用戶端密碼**] 底下，選取 [**新增用戶端密碼**] 以建立新的密碼。
1. 提供密碼的描述，然後選擇想要的到期間隔。
1. 立即將新密碼的值複製到安全的位置。 填滿值只會顯示一次。

    ![用戶端密碼](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>取得權杖的用戶端程式庫  
註冊應用程式並授與在 Azure 事件中樞中傳送/接收資料的許可權之後，您就可以將程式碼新增至應用程式，以驗證安全性主體並取得 OAuth 2.0 權杖。 若要驗證並取得權杖，您可以使用其中一個 Microsoft 身分[識別平臺驗證程式庫](../active-directory/develop/reference-v2-libraries.md)，或另一個支援 OpenID 或 connect 1.0 的開放原始碼程式庫。 然後，您的應用程式就可以使用存取權杖來授權對 Azure 事件中樞的要求。

如需支援取得權杖的案例清單，請參閱適用于 .NET GitHub 存放庫的[Microsoft 驗證程式庫（MSAL）](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)的[案例](https://aka.ms/msal-net-scenarios)一節。

## <a name="samples"></a>範例
- [EventHubs 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    這些範例會使用舊的**EventHubs**程式庫，但您可以使用最新的**EventHubs**程式庫輕鬆地將其更新為。 若要將範例從使用舊的程式庫移至新的程式庫，請參閱[從 EventHubs 遷移至 EventHubs 的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)。
- [EventHubs 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    此範例已更新為使用最新的**EventHubs**程式庫。

## <a name="next-steps"></a>後續步驟
- 若要深入瞭解 RBAC，請參閱[什麼是 azure 角色型存取控制（AZURE RBAC）](../role-based-access-control/overview.md)？
- 若要瞭解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派和管理 Azure 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure Resource Manager 範本管理角色型存取控制（RBAC）](../role-based-access-control/role-assignments-template.md)

請參閱下列相關文章：
- [使用 Azure Active Directory 來驗證受控識別，以存取事件中樞資源](authenticate-managed-identity.md)
- [使用共用存取簽章驗證 Azure 事件中樞的要求](authenticate-shared-access-signature.md)
- [使用 Azure Active Directory 授權事件中樞資源的存取權](authorize-access-azure-active-directory.md)
- [使用共用存取簽章授權事件中樞資源的存取權](authorize-access-shared-access-signature.md)
