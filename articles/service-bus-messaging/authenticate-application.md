---
title: 驗證應用程式以存取 Azure 服務匯流排實體
description: '本文提供的資訊說明如何使用 Azure Active Directory 來驗證應用程式，以存取 (佇列、主題等 Azure 服務匯流排實體 ) '
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: b12f2f294a66159a7035240c361ab93f9f84718e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88064820"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>使用 Azure Active Directory 來驗證和授權應用程式，以存取 Azure 服務匯流排實體
Azure 服務匯流排支援使用 Azure Active Directory (Azure AD) 來授權 (佇列、主題、訂用帳戶或篩選) 的服務匯流排實體的要求。 使用 Azure AD 時，您可以使用 Azure 角色型存取控制 (Azure RBAC) 來授與許可權給安全性主體，該安全性主體可能是使用者、群組或應用程式服務主體。 若要深入瞭解角色和角色指派，請參閱 [瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概觀
當安全性主體 (使用者、群組或應用程式) 嘗試存取服務匯流排實體時，要求必須獲得授權。 使用 Azure AD，對資源的存取是兩個步驟的程序。 

 1. 首先，會驗證安全性主體的身分識別，並傳回 OAuth 2.0 權杖。 要求權杖的資源名稱是 `https://servicebus.azure.net` 。
 1. 接下來，權杖會作為要求的一部分傳遞至服務匯流排服務，以授權存取指定的資源。

驗證步驟要求應用程式要求在執行階段包含 OAuth 2.0 存取權杖。 如果應用程式是在 azure 實體中執行，例如 Azure VM、虛擬機器擴展集或 Azure 函式應用程式，則可以使用受控識別來存取資源。 若要瞭解如何驗證受控識別對服務匯流排服務提出的要求，請參閱 [使用 Azure 資源的 Azure Active Directory 和受控識別來驗證對 Azure 服務匯流排資源的存取](service-bus-managed-service-identity.md)。 

授權步驟需要將一或多個 Azure 角色指派給安全性主體。 Azure 服務匯流排提供的 Azure 角色包含服務匯流排資源的一組許可權。 指派給安全性主體的角色會決定主體將擁有的許可權。 若要深入瞭解如何將 Azure 角色指派給 Azure 服務匯流排，請參閱 [適用于 Azure 服務匯流排的 azure 內建角色](#azure-built-in-roles-for-azure-service-bus)。 

對服務匯流排提出要求的原生應用程式和 web 應用程式，也可以使用 Azure AD 來授權。 本文說明如何要求存取權杖，並使用它來授權服務匯流排資源的要求。 


## <a name="assigning-azure-roles-for-access-rights"></a>指派存取權限的 Azure 角色
Azure Active Directory (Azure AD) 透過 [AZURE RBAC](../role-based-access-control/overview.md)授與受保護資源的存取權限。 Azure 服務匯流排定義一組 Azure 內建角色，其中包含用來存取服務匯流排實體的一般許可權集，您也可以定義自訂角色來存取資料。

當 Azure 角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取權的範圍可以是訂用帳戶、資源群組或服務匯流排命名空間的等級。 Azure AD 的安全性主體可能是使用者、群組、應用程式服務主體，或 [適用于 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="azure-built-in-roles-for-azure-service-bus"></a>適用于 Azure 服務匯流排的 Azure 內建角色
針對 Azure 服務匯流排，透過 Azure 入口網站和 Azure 資源管理 API 來管理命名空間和所有相關資源的功能，已使用 Azure RBAC 模型來保護。 Azure 提供下列 Azure 內建角色，以授權存取服務匯流排命名空間：

- [Azure 服務匯流排資料擁有](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)者：允許對服務匯流排命名空間及其實體的資料存取 (佇列、主題、訂用帳戶和篩選) 
- [Azure 服務匯流排資料](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)傳送者：使用此角色來授與服務匯流排命名空間及其實體的傳送存取權。
- [Azure 服務匯流排資料接收者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)：使用此角色來授與接收服務匯流排命名空間及其實體的存取權。 

## <a name="resource-scope"></a>資源範圍 
將 Azure 角色指派給安全性主體之前，請先決定安全性主體應該具備的存取範圍。 最佳做法的要求是，最好只授與最小的可能範圍。

下列清單描述從最小範圍開始，您可以範圍存取服務匯流排資源的層級：

- **佇列**、 **主題**或 **訂**用帳戶：角色指派會套用至特定的服務匯流排實體。 目前，Azure 入口網站不支援將使用者/群組/受控識別指派給訂用帳戶層級的服務匯流排 Azure 角色。 
- **服務匯流排命名空間**：角色指派橫跨命名空間下的整個服務匯流排拓撲，以及與其相關聯的取用者群組。
- **資源群組**：角色指派會套用至資源群組下的所有服務匯流排資源。
- **訂**用帳戶：角色指派會套用至訂用帳戶中所有資源群組中的所有服務匯流排資源。

> [!NOTE]
> 請記住，Azure 角色指派最多可能需要五分鐘的時間來傳播。 

如需如何定義內建角色的詳細資訊，請參閱 [瞭解角色定義](../role-based-access-control/role-definitions.md#management-and-data-operations)。 如需建立 Azure 自訂角色的詳細資訊，請參閱 [azure 自訂角色](../role-based-access-control/custom-roles.md)。


## <a name="assign-azure-roles-using-the-azure-portal"></a>使用 Azure 入口網站指派 Azure 角色  
若要深入瞭解如何使用 Azure RBAC 和 Azure 入口網站來管理 Azure 資源的存取權，請參閱 [這篇文章](..//role-based-access-control/role-assignments-portal.md)。 

確定角色指派的適當範圍之後，請流覽至 Azure 入口網站中的該資源。 顯示資源的存取控制 (IAM) 設定，並遵循下列指示來管理角色指派：

> [!NOTE]
> 下面所述的步驟會將角色指派給您的服務匯流排命名空間。 您可以遵循相同的步驟，將角色指派給其他支援的範圍 (資源群組、訂用帳戶等 ) 。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，流覽至您的服務匯流排命名空間。 選取左側功能表上的 [ **存取控制] (IAM) ** ，以顯示命名空間的存取控制設定。 如果您需要建立服務匯流排命名空間，請遵循這篇文章中的指示： [建立服務匯流排訊息命名空間](service-bus-create-namespace-portal.md)。

    ![選取左側功能表上的 [存取控制]](./media/authenticate-application/select-access-control-menu.png)
1. 選取 [角色指派] 索引標籤，以查看角色指派的清單。 選取工具列上的 [ **新增** ] 按鈕，然後選取 [ **新增角色指派**]。 

    ![工具列上的 [新增] 按鈕](./media/authenticate-application/role-assignments-add-button.png)
1. 在 [ **新增角色指派** ] 頁面上，執行下列步驟：
    1. 選取您要指派的 **服務匯流排角色** 。 
    1. 搜尋以找出您要指派角色 (使用者、群組、服務主體) 的 **安全性主體** 。
    1. 選取 [ **儲存** ] 以儲存角色指派。 

        ![將角色指派給使用者](./media/authenticate-application/assign-role-to-user.png)
    4. 您對其指派角色的身分識別會出現在該角色下方。 例如，下圖顯示 Azure 使用者位於 Azure 服務匯流排資料擁有者角色中。 
        
        ![清單中的使用者](./media/authenticate-application/user-in-list.png)

您可以依照類似的步驟，指派範圍為資源群組或訂用帳戶的角色。 定義角色及其範圍之後，您可以使用 [GitHub 上的範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)來測試此行為。


## <a name="authenticate-from-an-application"></a>從應用程式進行驗證
使用 Azure AD 與服務匯流排的主要優點是，您的認證不再需要儲存在程式碼中。 相反地，您可以向 Microsoft 身分識別平臺要求 OAuth 2.0 存取權杖。 Azure AD 會 (使用者、群組或服務主體) 執行應用程式來驗證安全性主體。 如果驗證成功，Azure AD 會將存取權杖傳回給應用程式，然後應用程式就可以使用存取權杖來授權 Azure 服務匯流排的要求。

下列各節說明如何設定原生應用程式或 web 應用程式，以使用 Microsoft 身分識別平臺2.0 進行驗證。 如需 Microsoft 身分識別平臺2.0 的詳細資訊，請參閱 [microsoft 身分識別平臺 (v2.0) 總覽](../active-directory/develop/v2-overview.md)。

如需 OAuth 2.0 程式碼授與流程的概觀，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../active-directory/develop/v2-oauth2-auth-code-flow.md)。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>向 Azure AD 租用戶註冊應用程式
使用 Azure AD 來授權服務匯流排實體的第一個步驟，是向 [Azure 入口網站](https://portal.azure.com/)向 Azure AD 租使用者註冊用戶端應用程式。 當您註冊用戶端應用程式時，會將應用程式的相關資訊提供給 AD。 Azure AD 接著提供用戶端識別碼 (也稱為應用程式識別碼) ，您可以用來將應用程式與 Azure AD 執行時間產生關聯。 若要深入了解用戶端識別碼，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。 

下列影像顯示註冊 web 應用程式的步驟：

![註冊應用程式](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 如果您將應用程式註冊為原生應用程式，可以為重新導向 URI 指定任何有效的 URI。 若為原生應用程式，此值不需要是實際的 URL。 針對 web 應用程式，重新導向 URI 必須是有效的 URI，因為它會指定提供權杖的 URL。

註冊您的應用程式之後，您會在 [**設定**] 底下看到**應用程式 (用戶端) 識別碼**：

![已註冊應用程式的應用程式識別碼](./media/authenticate-application/application-id.png)

如需有關向 Azure AD 註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../active-directory/develop/quickstart-register-app.md)。

> [!IMPORTANT]
> 記下 **TenantId** 和 **ApplicationId**。 您將需要這些值來執行應用程式。

### <a name="create-a-client-secret"></a>建立用戶端密碼   
應用程式需要用戶端密碼，才能在要求權杖時證明其身分識別。 若要新增用戶端密碼，請遵循下列步驟。

1. 如果您還沒有在頁面上，請流覽至您在 Azure 入口網站中的應用程式註冊。
1. 在左側功能表上選取 [ **憑證 & 秘密** ]。
1. 在 [ **用戶端密碼**] 下，選取 [ **新增用戶端密碼** ] 以建立新的密碼。

    ![新用戶端密碼-按鈕](./media/authenticate-application/new-client-secret-button.png)
1. 提供秘密的描述，並選擇想要的到期間隔，然後選取 [ **新增**]。

    ![[新增用戶端密碼] 頁面](./media/authenticate-application/add-client-secret-page.png)
1. 立即將新密碼的值複製到安全的位置。 填滿值只會顯示一次。

    ![用戶端密碼](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>服務匯流排 API 的許可權
如果您的應用程式是主控台應用程式，您必須註冊原生應用程式，並將 **Microsoft** 的 API 許可權新增至 **所需的許可權** 集合。 原生應用程式也需要 Azure AD 中的重新 **導向 URI** ，作為識別碼;URI 不需要是網路目的地。 在此範例中使用 `https://servicebus.microsoft.com`，因為程式碼範例已使用該 URI。

### <a name="client-libraries-for-token-acquisition"></a>取得權杖的用戶端程式庫  
當您註冊應用程式，並授與它在 Azure 服務匯流排中傳送/接收資料的許可權之後，您可以將程式碼新增至應用程式，以驗證安全性主體並取得 OAuth 2.0 權杖。 若要驗證並取得權杖，您可以使用其中一個 Microsoft 身分 [識別平臺驗證程式庫](../active-directory/develop/reference-v2-libraries.md) ，或另一個支援 OpenID 或 connect 1.0 的開放原始碼程式庫。 然後，您的應用程式就可以使用存取權杖來對 Azure 服務匯流排授權要求。

如需支援取得權杖的案例清單，請參閱[Microsoft 驗證程式庫 (MSAL) 中的「適用于 .Net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub 存放庫的[案例](https://aka.ms/msal-net-scenarios)」一節。

## <a name="sample-on-github"></a>GitHub 上的範例
請參閱 GitHub 上的下列範例： [服務匯流排的角色-基底存取控制](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)。 

使用 **用戶端密碼登** 入選項，而非 **互動式使用者登** 入選項。 當您使用 [用戶端密碼] 選項時，您不會看到快顯視窗。 應用程式會利用租使用者識別碼和應用程式識別碼進行驗證。 

### <a name="run-the-sample"></a>執行範例

在您可以執行範例之前，請先編輯 **app.config** 檔案，然後根據您的案例設定下列值：

- `tenantId`：設定為 **TenantId** 值。
- `clientId`：設定為 **ApplicationId** 值。
- `clientSecret`：如果您要使用用戶端密碼來登入，請在 Azure AD 中建立該用戶端密碼。 另外，請使用 Web 應用程式或 API 而非使用原生應用程式。 還有，請將應用程式新增到您先前所建立之命名空間中的 [存取控制 (IAM)]**** 底下。
- `serviceBusNamespaceFQDN`：設定為新建立之服務匯流排命名空間的完整 DNS 名稱；例如 `example.servicebus.windows.net`。
- `queueName`：設定為您所建立之佇列的名稱。
- 您在前面步驟的應用程式中所指定的重新導向 URI。

當您執行主控台應用程式時，系統會提示您選取案例。 輸入 **互動式使用者登** 入，方法是輸入其號碼，然後按 enter。 應用程式隨即會顯示登入視窗，要求您同意存取服務匯流排，然後使用該服務並利用登入身分識別來執行傳送/接收案例。


## <a name="next-steps"></a>後續步驟
- 若要深入瞭解 Azure RBAC，請參閱 [什麼是 AZURE rbac)  (azure 角色型存取控制 ](../role-based-access-control/overview.md)？
- 若要瞭解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派及管理 Azure 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure Resource Manager 範本來新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-template.md)

若要深入了解服務匯流排訊息，請參閱下列主題。

- [服務匯流排 Azure RBAC 範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
- [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
- [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)