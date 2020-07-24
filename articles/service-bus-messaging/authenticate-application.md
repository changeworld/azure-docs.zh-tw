---
title: 驗證應用程式以存取 Azure 服務匯流排實體
description: 本文提供的資訊說明如何使用 Azure Active Directory 來驗證應用程式，以存取 Azure 服務匯流排實體（佇列、主題等）
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 371fa42420c2cc90a581b05ac47d7a65f28fc473
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128483"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>使用 Azure Active Directory 來驗證和授權應用程式，以存取 Azure 服務匯流排實體
Azure 服務匯流排支援使用 Azure Active Directory （Azure AD）來授權服務匯流排實體（佇列、主題、訂用帳戶或篩選器）的要求。 使用 Azure AD，您可以使用角色型存取控制（RBAC），將許可權授與安全性主體，這可能是使用者、群組或應用程式服務主體。 若要深入瞭解角色和角色指派，請參閱[瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>總覽
當安全性主體（使用者、群組或應用程式）嘗試存取服務匯流排實體時，要求必須獲得授權。 使用 Azure AD，對資源的存取是兩個步驟的程序。 

 1. 首先，安全性主體的身分識別已通過驗證，並傳回 OAuth 2.0 權杖。 要求權杖的資源名稱是 `https://servicebus.azure.net` 。
 1. 接下來，權杖會當做要求的一部分傳遞給服務匯流排服務，以授權存取指定的資源。

驗證步驟要求應用程式要求在執行階段包含 OAuth 2.0 存取權杖。 如果應用程式是在 azure 實體（例如 Azure VM、虛擬機器擴展集或 Azure 函式應用程式）內執行，它可以使用受控識別來存取資源。 若要瞭解如何驗證受控識別對服務匯流排服務所提出的要求，請參閱[使用 Azure 資源的 Azure Active Directory 和受控識別來驗證 Azure 服務匯流排資源的存取權](service-bus-managed-service-identity.md)。 

授權步驟需要將一或多個 RBAC 角色指派給安全性主體。 Azure 服務匯流排提供 RBAC 角色，其中包含服務匯流排資源的許可權集。 指派給安全性主體的角色會決定主體將擁有的許可權。 若要深入瞭解如何將 RBAC 角色指派給 Azure 服務匯流排，請參閱[Azure 服務匯流排的內建 rbac 角色](#built-in-rbac-roles-for-azure-service-bus)。 

向服務匯流排提出要求的原生應用程式和 web 應用程式也可以使用 Azure AD 進行授權。 本文說明如何要求存取權杖，並使用它來授權服務匯流排資源的要求。 


## <a name="assigning-rbac-roles-for-access-rights"></a>指派存取權限的 RBAC 角色
Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 服務匯流排定義一組內建的 RBAC 角色，其中包含用來存取服務匯流排實體的常用許可權集，而且您也可以定義自訂角色來存取資料。

當 RBAC 角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取權的範圍可以是訂用帳戶、資源群組或服務匯流排命名空間的層級。 Azure AD 的安全性主體可以是使用者、群組、應用程式服務主體，或[適用于 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Azure 服務匯流排的內建 RBAC 角色
對於 Azure 服務匯流排來說，透過 Azure 入口網站和 Azure 資源管理 API 來的管理命名空間和所有相關資源的作業，已使用「角色型存取控制** (RBAC)」模型來加以保護。 Azure 提供下列內建的 RBAC 角色，以授權存取服務匯流排命名空間：

- [Azure 服務匯流排資料擁有](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)者：啟用服務匯流排命名空間及其實體（佇列、主題、訂用帳戶和篩選器）的資料存取
- [Azure 服務匯流排資料](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)傳送者：使用此角色可授與服務匯流排命名空間及其實體的傳送存取權。
- [Azure 服務匯流排資料接收器](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)：使用此角色可授與服務匯流排命名空間及其實體的接收存取權。 

## <a name="resource-scope"></a>資源範圍 
將 RBAC 角色指派給安全性主體之前，請先決定安全性主體應該具備的存取範圍。 最佳做法規定，最好只授與最少的可能範圍。

下列清單說明您可以將存取範圍限定為服務匯流排資源的層級，從最窄的範圍開始：

- **佇列**、**主題**或**訂**用帳戶：角色指派會套用至特定的服務匯流排實體。 目前，Azure 入口網站不支援在訂用帳戶層級將使用者/群組/受控識別指派給服務匯流排 RBAC 角色。 
- **服務匯流排命名空間**：角色指派會跨越命名空間下服務匯流排的整個拓撲，以及與其相關聯的取用者群組。
- **資源群組**：角色指派會套用至資源群組下的所有服務匯流排資源。
- **訂**用帳戶：角色指派會套用至訂用帳戶中所有資源群組內的所有服務匯流排資源。

> [!NOTE]
> 請記住，RBAC 角色指派最多可能需要五分鐘的時間來傳播。 

如需如何定義內建角色的詳細資訊，請參閱[瞭解角色定義](../role-based-access-control/role-definitions.md#management-and-data-operations)。 如需建立 Azure 自訂角色的詳細資訊，請參閱[azure 自訂角色](../role-based-access-control/custom-roles.md)。


## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 入口網站指派 RBAC 角色  
若要深入瞭解如何使用 RBAC 和 Azure 入口網站來管理 Azure 資源的存取權，請參閱[這篇文章](..//role-based-access-control/role-assignments-portal.md)。 

在決定角色指派的適當範圍之後，請流覽至 Azure 入口網站中的該資源。 顯示資源的 [存取控制（IAM）] 設定，並遵循下列指示來管理角色指派：

> [!NOTE]
> 下面所述的步驟會將角色指派給您的服務匯流排命名空間。 您可以遵循相同的步驟，將角色指派給其他支援的範圍（資源群組、訂用帳戶等）。

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，流覽至您的服務匯流排命名空間。 選取左側功能表上的 **[存取控制（IAM）** ]，以顯示命名空間的存取控制設定。 如果您需要建立服務匯流排命名空間，請遵循這篇文章中的指示：[建立服務匯流排訊息命名空間](service-bus-create-namespace-portal.md)。

    ![選取左側功能表上的 [存取控制]](./media/authenticate-application/select-access-control-menu.png)
1. 選取 [角色指派] 索引標籤，以查看角色指派的清單。 選取工具列上的 [**新增**] 按鈕，然後選取 [**新增角色指派**]。 

    ![工具列上的 [新增] 按鈕](./media/authenticate-application/role-assignments-add-button.png)
1. 在 [**新增角色指派**] 頁面上，執行下列步驟：
    1. 選取您要指派的**服務匯流排角色**。 
    1. 搜尋以找出您要為其指派角色的**安全性主體**（使用者、群組、服務主體）。
    1. 選取 [**儲存**] 以儲存角色指派。 

        ![將角色指派給使用者](./media/authenticate-application/assign-role-to-user.png)
    4. 您對其指派角色的身分識別會出現在該角色下方。 例如，下圖顯示 Azure-使用者是 Azure 服務匯流排資料擁有者角色。 
        
        ![清單中的使用者](./media/authenticate-application/user-in-list.png)

您可以遵循類似的步驟，指派範圍限定在資源群組或訂用帳戶的角色。 定義角色及其範圍之後，您可以使用[GitHub 上的範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)來測試此行為。


## <a name="authenticate-from-an-application"></a>從應用程式進行驗證
搭配服務匯流排使用 Azure AD 的主要優點是您的認證不再需要儲存在您的程式碼中。 相反地，您可以向 Microsoft 身分識別平臺要求 OAuth 2.0 存取權杖。 Azure AD 會驗證執行應用程式的安全性主體（使用者、群組或服務主體）。 如果驗證成功，Azure AD 會將存取權杖傳回給應用程式，然後應用程式就可以使用存取權杖來授權 Azure 服務匯流排的要求。

下列各節說明如何設定原生應用程式或 web 應用程式，以使用 Microsoft 身分識別平臺2.0 進行驗證。 如需 Microsoft 身分識別平臺2.0 的詳細資訊，請參閱[microsoft 身分識別平臺（v2.0）總覽](../active-directory/develop/v2-overview.md)。

如需 OAuth 2.0 程式碼授與流程的概觀，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../active-directory/develop/v2-oauth2-auth-code-flow.md)。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>向 Azure AD 租用戶註冊應用程式
使用 Azure AD 來授權服務匯流排實體的第一個步驟，是向[Azure 入口網站](https://portal.azure.com/)中的 Azure AD 租使用者註冊您的用戶端應用程式。 當您註冊用戶端應用程式時，會向 AD 提供應用程式的相關資訊。 Azure AD 接著會提供用戶端識別碼（也稱為應用程式識別碼），讓您用來將應用程式與 Azure AD 執行時間建立關聯。 若要深入了解用戶端識別碼，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。 

下列影像顯示註冊 web 應用程式的步驟：

![註冊應用程式](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 如果您將應用程式註冊為原生應用程式，您可以為重新導向 URI 指定任何有效的 URI。 對於原生應用程式，這個值不一定是真正的 URL。 對於 web 應用程式，重新導向 URI 必須是有效的 URI，因為它會指定提供權杖的 URL。

註冊應用程式之後，您會在 [**設定**] 底下看到**應用程式（用戶端）識別碼**：

![已註冊應用程式的應用程式識別碼](./media/authenticate-application/application-id.png)

如需有關向 Azure AD 註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md)。

> [!IMPORTANT]
> 記下**TenantId**和**ApplicationId**。 您將需要這些值才能執行應用程式。

### <a name="create-a-client-secret"></a>建立用戶端密碼   
應用程式需要用戶端密碼，才能在要求權杖時證明其身分識別。 若要新增用戶端密碼，請遵循下列步驟。

1. 如果您還沒有在頁面上，請流覽至您在 Azure 入口網站中的應用程式註冊。
1. 選取左側功能表上的 [**憑證 & 密碼**]。
1. 在 [**用戶端密碼**] 底下，選取 [**新增用戶端密碼**] 以建立新的密碼。

    ![[新增用戶端密碼] 按鈕](./media/authenticate-application/new-client-secret-button.png)
1. 提供密碼的描述，並選擇想要的到期間隔，然後選取 [**新增**]。

    ![[新增用戶端密碼] 頁面](./media/authenticate-application/add-client-secret-page.png)
1. 立即將新密碼的值複製到安全的位置。 填滿值只會顯示一次。

    ![用戶端密碼](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>服務匯流排 API 的許可權
如果您的應用程式是主控台應用程式，您必須註冊原生應用程式，並將**Microsoft**的 API 許可權新增至**所需的許可權**集合。 原生應用程式也需要 Azure AD 中的重新**導向 URI** ，其可作為識別碼;URI 不需要是網路目的地。 在此範例中使用 `https://servicebus.microsoft.com`，因為程式碼範例已使用該 URI。

### <a name="client-libraries-for-token-acquisition"></a>取得權杖的用戶端程式庫  
註冊應用程式並授與在 Azure 服務匯流排中傳送/接收資料的許可權之後，您就可以將程式碼新增至應用程式，以驗證安全性主體並取得 OAuth 2.0 權杖。 若要驗證並取得權杖，您可以使用其中一個 Microsoft 身分[識別平臺驗證程式庫](../active-directory/develop/reference-v2-libraries.md)，或另一個支援 OpenID 或 connect 1.0 的開放原始碼程式庫。 然後，您的應用程式就可以使用存取權杖來授權對 Azure 服務匯流排的要求。

如需支援取得權杖的案例清單，請參閱適用于 .NET GitHub 存放庫的[Microsoft 驗證程式庫（MSAL）](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)的[案例](https://aka.ms/msal-net-scenarios)一節。

## <a name="sample-on-github"></a>GitHub 上的範例
請參閱 GitHub 上的下列範例：[服務匯流排的角色基底存取控制](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)。 

使用 [**用戶端密碼] 登**入選項，而不是 [**互動式使用者登**入] 選項。 當您使用 [用戶端密碼] 選項時，您不會看到快顯視窗。 應用程式會利用租使用者識別碼和應用程式識別碼來進行驗證。 

### <a name="run-the-sample"></a>執行範例

在執行此範例之前，請先編輯**app.config**檔案，並根據您的案例設定下列值：

- `tenantId`：設定為 **TenantId** 值。
- `clientId`：設定為 **ApplicationId** 值。
- `clientSecret`：如果您要使用用戶端密碼來登入，請在 Azure AD 中建立該用戶端密碼。 另外，請使用 Web 應用程式或 API 而非使用原生應用程式。 還有，請將應用程式新增到您先前所建立之命名空間中的 [存取控制 (IAM)]**** 底下。
- `serviceBusNamespaceFQDN`：設定為新建立之服務匯流排命名空間的完整 DNS 名稱；例如 `example.servicebus.windows.net`。
- `queueName`：設定為您所建立之佇列的名稱。
- 您在前面步驟的應用程式中所指定的重新導向 URI。

當您執行主控台應用程式時，系統會提示您選取案例。 輸入 **[互動式使用者登**入]，然後按 enter 鍵。 應用程式隨即會顯示登入視窗，要求您同意存取服務匯流排，然後使用該服務並利用登入身分識別來執行傳送/接收案例。


## <a name="next-steps"></a>後續步驟
- 若要深入瞭解 RBAC，請參閱[什麼是 azure 角色型存取控制（AZURE RBAC）](../role-based-access-control/overview.md)？
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派和管理 RBAC 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure Resource Manager 範本管理角色型存取控制（RBAC）](../role-based-access-control/role-assignments-template.md)

若要深入了解服務匯流排訊息，請參閱下列主題。

- [服務匯流排 RBAC 範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
- [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
- [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)
