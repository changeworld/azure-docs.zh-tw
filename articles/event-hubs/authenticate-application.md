---
title: 驗證應用程式以存取 Azure 事件中樞資源
description: 本文提供的資訊說明如何使用 Azure Active Directory 來驗證應用程式，以存取 Azure 事件中樞資源
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 25ec5f11ca7b5e801e18155f1a3da6474c8e66e2
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913308"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>使用 Azure Active Directory 來驗證應用程式，以存取事件中樞資源
Microsoft Azure 針對以 Azure Active Directory (Azure AD) 為基礎的資源和應用程式提供了整合式的存取控制管理功能。 使用 Azure AD 搭配 Azure 事件中樞的主要優點是您不再需要將認證儲存在程式碼中。 相反地，您可以從 Microsoft 身分識別平臺要求 OAuth 2.0 存取權杖。 要求權杖的資源名稱是 `https://eventhubs.azure.net/` ，針對 Kafka 用戶端 (的所有雲端/租使用者，其要求權杖的資源 `https://<namespace>.servicebus.windows.net`) 。 Azure AD 會 (使用者、群組或服務主體) 執行應用程式來驗證安全性主體。 如果驗證成功，Azure AD 會將存取權杖傳回給應用程式，然後應用程式就可以使用存取權杖來授權 Azure 事件中樞資源的要求。

將角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取範圍可設定為訂用帳戶層級、資源群組、事件中樞命名空間或其下的任何資源。 Azure AD 安全性可將角色指派給使用者、群組、應用程式服務主體或 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。 

> [!NOTE]
> 角色定義是一個權限集合。 Azure 角色型存取控制 (Azure RBAC) 控制如何透過角色指派來強制執行這些許可權。 角色指派由三項元素所組成：安全性主體、角色定義和範圍。 如需詳細資訊，請參閱 [瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="built-in-roles-for-azure-event-hubs"></a>Azure 事件中樞的內建角色
Azure 提供下列 Azure 內建角色，以使用 Azure AD 和 OAuth 來授權事件中樞資料的存取權：

- [Azure 事件中樞資料擁有](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)者：使用此角色可提供事件中樞資源的完整存取權。
- [Azure 事件中樞資料](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)傳送者：使用此角色可授與事件中樞資源的傳送存取權。
- [Azure 事件中樞資料接收者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)：使用此角色可授與事件中樞資源的接收存取權。   

如需 Schema Registry 內建角色，請參閱 [架構登錄角色](schema-registry-overview.md#azure-role-based-access-control)。

> [!IMPORTANT]
> 我們的預覽版本支援將事件中樞資料存取權限新增至擁有者或參與者角色。 不過，已不再接受擁有者和參與者角色的資料存取權限。 如果您使用的是擁有者或參與者角色，請切換到使用 Azure 事件中樞資料擁有者角色。


## <a name="authenticate-from-an-application"></a>從應用程式進行驗證
使用 Azure AD 搭配事件中樞的主要優點是您的認證不再需要儲存在程式碼中。 相反地，您可以向 Microsoft 身分識別平臺要求 OAuth 2.0 存取權杖。 Azure AD 會 (使用者、群組或服務主體) 執行應用程式來驗證安全性主體。 如果驗證成功，Azure AD 會將存取權杖傳回給應用程式，然後應用程式就可以使用存取權杖來授權 Azure 事件中樞的要求。

下列各節說明如何設定原生應用程式或 web 應用程式，以使用 Microsoft 身分識別平臺2.0 進行驗證。 如需 Microsoft 身分識別平臺2.0 的詳細資訊，請參閱 [microsoft 身分識別平臺 (v2.0) 總覽](../active-directory/develop/v2-overview.md)。

如需 OAuth 2.0 程式碼授與流程的概觀，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../active-directory/develop/v2-oauth2-auth-code-flow.md)。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>向 Azure AD 租用戶註冊應用程式
使用 Azure AD 來授權事件中樞資源的第一個步驟，是向 [Azure 入口網站](https://portal.azure.com/)向 Azure AD 租使用者註冊用戶端應用程式。 當您註冊用戶端應用程式時，會將應用程式的相關資訊提供給 AD。 Azure AD 接著提供用戶端識別碼 (也稱為應用程式識別碼) ，您可以用來將應用程式與 Azure AD 執行時間產生關聯。 若要深入了解用戶端識別碼，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。 

下列影像顯示註冊 web 應用程式的步驟：

![註冊應用程式](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 如果您將應用程式註冊為原生應用程式，可以為重新導向 URI 指定任何有效的 URI。 若為原生應用程式，此值不需要是實際的 URL。 針對 web 應用程式，重新導向 URI 必須是有效的 URI，因為它會指定提供權杖的 URL。

註冊您的應用程式之後，您會在 [ **設定** ] 底下看到 **應用程式 (用戶端) 識別碼** ：

![已註冊應用程式的應用程式識別碼](./media/authenticate-application/application-id.png)

如需有關向 Azure AD 註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../active-directory/develop/quickstart-register-app.md)。


### <a name="create-a-client-secret"></a>建立用戶端密碼   
應用程式需要用戶端密碼，才能在要求權杖時證明其身分識別。 若要新增用戶端密碼，請遵循下列步驟。

1. 在 Azure 入口網站中，流覽至您的應用程式註冊。
1. 選取 **憑證 & 秘密** 設定。
1. 在 [ **用戶端密碼** ] 下，選取 [ **新增用戶端密碼** ] 以建立新的密碼。
1. 提供秘密的描述，並選擇想要的到期間隔。
1. 立即將新密碼的值複製到安全的位置。 填滿值只會顯示一次。

    ![用戶端密碼](./media/authenticate-application/client-secret.png)


## <a name="assign-azure-roles-using-the-azure-portal"></a>使用 Azure 入口網站指派 Azure 角色  
註冊應用程式之後，您可以將應用程式的服務主體指派給事件中樞 Azure AD 角色，Azure 事件中樞區段的 [ [內建角色](#built-in-roles-for-azure-event-hubs) ] 區段中所述。 

1. 在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽到您的事件中樞命名空間。
2. 在 [ **總覽** ] 頁面上，選取您要指派角色的事件中樞。

    ![選取您的事件中樞](./media/authenticate-application/select-event-hub.png)
1. 選取 [ **存取控制] (IAM)** 來顯示事件中樞的存取控制設定。 
1. 選取 [角色指派] 索引標籤，以查看角色指派的清單。 選取工具列上的 [ **新增** ] 按鈕，然後選取 [ **新增角色指派** ]。 

    ![工具列上的 [新增] 按鈕](./media/authenticate-application/role-assignments-add-button.png)
1. 在 [ **新增角色指派** ] 頁面上，執行下列步驟：
    1. 選取您要指派的 **事件中樞角色** 。 
    1. 搜尋以找出您要指派角色 (使用者、群組、服務主體) 的 **安全性主體** 。 從清單中選取 **已註冊的應用程式** 。 
    1. 選取 [ **儲存** ] 以儲存角色指派。 

        ![將角色指派給使用者](./media/authenticate-application/assign-role-to-user.png)
    4. 切換至 [ **角色指派** ] 索引標籤，並確認角色指派。 例如，下圖顯示 **mywebapp** 是在 **Azure 事件中樞資料** 傳送者角色中。 
        
        ![清單中的使用者](./media/authenticate-application/user-in-list.png)

您可以依照類似的步驟，指派範圍為事件中樞命名空間、資源群組或訂用帳戶的角色。 定義角色及其範圍之後，您可以使用 [此 GitHub 位置中](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)的範例來測試此行為。 若要深入瞭解如何使用 Azure RBAC 和 Azure 入口網站來管理 Azure 資源的存取權，請參閱 [這篇文章](..//role-based-access-control/role-assignments-portal.md)。 


### <a name="client-libraries-for-token-acquisition"></a>取得權杖的用戶端程式庫  
當您註冊應用程式，並授與它在 Azure 事件中樞中傳送/接收資料的許可權之後，您可以將程式碼新增至應用程式，以驗證安全性主體並取得 OAuth 2.0 權杖。 若要驗證並取得權杖，您可以使用其中一個 Microsoft 身分 [識別平臺驗證程式庫](../active-directory/develop/reference-v2-libraries.md) ，或另一個支援 OpenID 或 connect 1.0 的開放原始碼程式庫。 然後，您的應用程式就可以使用存取權杖來對 Azure 事件中樞授權要求。

如需支援取得權杖的案例清單，請參閱[Microsoft 驗證程式庫 (MSAL) 中的「適用于 .Net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub 存放庫的[案例](https://aka.ms/msal-net-scenarios)」一節。

## <a name="samples"></a>範例
- [EventHubs 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    這些範例會使用舊的 **EventHubs** 程式庫，但您可以輕鬆地將其更新為使用最新的 **EventHubs** 程式庫。 若要將範例從使用舊的程式庫移至新的程式庫，請參閱 [從 EventHubs 遷移至 EventHubs 的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)。
- [EventHubs 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    此範例已更新為使用最新的 **EventHubs** 程式庫。

## <a name="next-steps"></a>下一步
- 若要深入瞭解 Azure RBAC，請參閱 [什麼是 AZURE rbac)  (azure 角色型存取控制 ](../role-based-access-control/overview.md)？
- 若要瞭解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派及管理 Azure 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure Resource Manager 範本新增 Azure 角色指派](../role-based-access-control/role-assignments-template.md)

請參閱下列相關文章：
- [使用 Azure Active Directory 來驗證受控識別，以存取事件中樞資源](authenticate-managed-identity.md)
- [使用共用存取簽章驗證 Azure 事件中樞的要求](authenticate-shared-access-signature.md)
- [使用 Azure Active Directory 授權存取事件中樞資源](authorize-access-azure-active-directory.md)
- [使用共用存取簽章授權存取事件中樞資源](authorize-access-shared-access-signature.md)
