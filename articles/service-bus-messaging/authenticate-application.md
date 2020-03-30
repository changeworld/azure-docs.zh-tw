---
title: 驗證應用程式以訪問 Azure 服務匯流排實體
description: 本文提供有關使用 Azure 活動目錄驗證應用程式以訪問 Azure 服務匯流排實體（佇列、主題等）的資訊。
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259288"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>使用 Azure 活動目錄驗證和授權應用程式訪問 Azure 服務匯流排實體
Azure 服務匯流排支援使用 Azure 活動目錄 （Azure AD） 授權對服務匯流排實體（佇列、主題、訂閱或篩選器）的請求。 使用 Azure AD，可以使用基於角色的存取控制 （RBAC） 向安全主體授予許可權，安全主體可以是使用者、組或應用程式服務主體。 要瞭解有關角色和角色指派的更多內容，請參閱[瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>總覽
當安全主體（使用者、組或應用程式）嘗試訪問服務匯流排實體時，必須授權該請求。 使用 Azure AD，訪問資源是一個兩步過程。 

 1. 首先，安全主體的標識經過身份驗證，並返回 OAuth 2.0 權杖。 請求權杖的資源名稱為`https://servicebus.azure.net`。
 1. 接下來，權杖作為請求的一部分傳遞給服務匯流排服務，以授權對指定資源的訪問。

身份驗證步驟要求應用程式請求在運行時包含 OAuth 2.0 訪問權杖。 如果應用程式在 Azure 實體（如 Azure VM、虛擬機器縮放集或 Azure 函數應用）內運行，則可以使用託管標識訪問資源。 要瞭解如何對託管標識對服務匯流排服務發出的請求進行身份驗證，請參閱使用[Azure 活動目錄和 Azure 資源的託管標識對 Azure 服務匯流排資源進行身份驗證訪問](service-bus-managed-service-identity.md)。 

授權步驟要求將一個或多個 RBAC 角色指派給安全主體。 Azure 服務匯流排提供 RBAC 角色，這些角色包含服務匯流排資源的許可權集。 分配給安全主體的角色確定主體將具有的許可權。 要瞭解有關將 RBAC 角色指派給 Azure 服務匯流排的更多詳細資訊，請參閱[Azure 服務匯流排的內置 RBAC 角色](#built-in-rbac-roles-for-azure-service-bus)。 

向服務匯流排發出請求的本機應用程式和 Web 應用程式也可以使用 Azure AD 進行授權。 本文介紹如何請求訪問權杖，並用它來授權服務匯流排資源的請求。 


## <a name="assigning-rbac-roles-for-access-rights"></a>為存取權限分配 RBAC 角色
Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 服務匯流排定義了一組內置 RBAC 角色，這些角色包含用於訪問服務匯流排實體的通用許可權集，還可以定義用於訪問資料的自訂角色。

將 RBAC 角色指派給 Azure AD 安全主體時，Azure 會授予對該安全主體的這些資源的存取權限。 訪問範圍可限定為訂閱級別、資源組或服務匯流排命名空間。 Azure AD 安全主體可以是 Azure 資源的使用者、組、應用程式服務主體或[託管標識](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Azure 服務匯流排的內置 RBAC 角色
對於 Azure 服務匯流排來說，透過 Azure 入口網站和 Azure 資源管理 API 來的管理命名空間和所有相關資源的作業，已使用「角色型存取控制** (RBAC)」模型來加以保護。 Azure 提供了以下內置 RBAC 角色，用於授權訪問服務匯流排命名空間：

- [Azure 服務匯流排資料擁有者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)：啟用對服務匯流排命名空間及其實體（佇列、主題、訂閱和篩選器）的資料訪問
- [Azure 服務匯流排資料發送方](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)：使用此角色授予對服務匯流排命名空間及其實體的發送存取權限。
- [Azure 服務匯流排資料接收器](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)：使用此角色授予對服務匯流排命名空間及其實體的接收存取權限。 

## <a name="resource-scope"></a>資源範圍 
在將 RBAC 角色指派給安全主體之前，請確定安全主體應具有的訪問範圍。 最佳做法規定，最好只授予盡可能窄的範圍。

下面的清單描述了您可以從最窄的範圍開始對服務匯流排資源的訪問的範圍的級別：

- **佇列**、**主題**或**訂閱**：角色指派適用于特定的服務匯流排實體。 目前，Azure 門戶不支援在訂閱級別將使用者/組/託管標識分配給服務匯流排 RBAC 角色。 
- **服務匯流排命名空間**：角色指派跨越命名空間下服務匯流排的整個拓撲以及與其關聯的消費者組。
- **資源組**：角色指派應用於資源組下的所有服務匯流排資源。
- **訂閱**：角色指派應用於訂閱中的所有資源組中的所有服務匯流排資源。

> [!NOTE]
> 請記住，RBAC 角色指派可能需要長達五分鐘才能傳播。 

有關如何定義內置角色的詳細資訊，請參閱[瞭解角色定義](../role-based-access-control/role-definitions.md#management-and-data-operations)。 有關創建自訂 RBAC 角色的資訊，請參閱[為 Azure 基於角色的存取控制創建自訂角色](../role-based-access-control/custom-roles.md)。


## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 門戶分配 RBAC 角色  
要瞭解有關使用 RBAC 和 Azure 門戶管理對 Azure 資源的訪問的更多內容，請參閱[本文](..//role-based-access-control/role-assignments-portal.md)。 

確定角色指派的適當範圍後，在 Azure 門戶中導航到該資源。 顯示資源的存取控制 （IAM） 設置，並按照以下說明管理角色指派：

> [!NOTE]
> 下面描述的步驟將角色指派給服務匯流排命名空間。 您可以按照相同的步驟將角色指派給其他受支援的範圍（資源組、訂閱等）。

1. 在[Azure 門戶](https://portal.azure.com/)中，導航到服務匯流排命名空間。 選擇左側功能表上**的存取控制 （IAM）** 以顯示命名空間的存取控制設置。 如果需要創建服務匯流排命名空間，請按照本文的說明操作：[創建服務匯流排消息命名空間](service-bus-create-namespace-portal.md)。

    ![選擇左側功能表上的存取控制](./media/authenticate-application/select-access-control-menu.png)
1. 選取 [角色指派]**** 索引標籤，以查看角色指派的清單。 選擇工具列上的 **"添加**"按鈕，然後選擇 **"添加角色指派**"。 

    ![在工具列上添加按鈕](./media/authenticate-application/role-assignments-add-button.png)
1. 在 **"添加角色指派"** 頁上，執行以下步驟：
    1. 選擇要分配的**服務匯流排角色**。 
    1. 搜索以查找要為其分配角色**的安全主體**（使用者、組、服務主體）。
    1. 選擇 **"保存"** 以保存角色指派。 

        ![將角色指派給使用者](./media/authenticate-application/assign-role-to-user.png)
    4. 您對其指派角色的身分識別會出現在該角色下方。 例如，下圖顯示 Azure 使用者位於 Azure 服務匯流排資料擁有者角色中。 
        
        ![清單中的使用者](./media/authenticate-application/user-in-list.png)

您可以按照類似的步驟將範圍為資源組或訂閱的角色指派。 定義角色及其範圍後，可以使用[GitHub 上的示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)測試此行為。


## <a name="authenticate-from-an-application"></a>從應用程式進行驗證
將 Azure AD 與服務匯流排一起使用的一個關鍵優點是，您的憑據不再需要存儲在代碼中。 相反，您可以從 Microsoft 標識平臺請求 OAuth 2.0 訪問權杖。 Azure AD 對運行應用程式的安全主體（使用者、組或服務主體）進行身份驗證。 如果身份驗證成功，Azure AD 會將訪問權杖返回到應用程式，然後應用程式可以使用訪問權杖授權對 Azure 服務匯流排的請求。

以下各節介紹如何配置本機應用程式或 Web 應用程式以使用 Microsoft 標識平臺 2.0 進行身份驗證。 有關 Microsoft 標識平臺 2.0 的詳細資訊，請參閱[Microsoft 標識平臺 （v2.0） 概述](../active-directory/develop/v2-overview.md)。

如需 OAuth 2.0 程式碼授與流程的概觀，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../active-directory/develop/v2-oauth2-auth-code-flow.md)。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>向 Azure AD 租用戶註冊應用程式
使用 Azure AD 授權服務匯流排實體的第一步是從[Azure 門戶](https://portal.azure.com/)向 Azure AD 租戶註冊用戶端應用程式。 註冊用戶端應用程式時，向 AD 提供有關該應用程式的資訊。 然後，Azure AD 提供一個用戶端 ID（也稱為應用程式 ID），可用於將應用程式與 Azure AD 運行時相關聯。 若要深入了解用戶端識別碼，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。 

下圖顯示了註冊 Web 應用程式的步驟：

![註冊應用程式](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 如果將應用程式註冊為本機應用程式，則可以為重定向 URI 指定任何有效的 URI。 對於本機應用程式，此值不必是真正的 URL。 對於 Web 應用程式，重定向 URI 必須是有效的 URI，因為它指定提供權杖的 URL。

註冊應用程式後，您將在 **"設置"** 下看到**應用程式（用戶端）ID：**

![已登記申請的申請 ID](./media/authenticate-application/application-id.png)

如需有關向 Azure AD 註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md)。

> [!IMPORTANT]
> 記下**租戶 Id**和應用程式**Id**。 您將需要這些值來運行應用程式。

### <a name="create-a-client-secret"></a>建立用戶端密碼   
應用程式在請求權杖時需要用戶端金鑰來證明其身份。 要添加用戶端機密，請按照以下步驟操作。

1. 如果尚未在頁面上，則導航到 Azure 門戶中的應用註冊。
1. 選擇左側功能表上**的"證書&機密**。
1. 在 **"用戶端機密**"下，選擇 **"新用戶端"秘密**以創建新機密。

    ![新用戶端金鑰 - 按鈕](./media/authenticate-application/new-client-secret-button.png)
1. 提供機密的說明，然後選擇所需的過期間隔，然後選擇 **"添加**"。

    ![添加用戶端機密頁](./media/authenticate-application/add-client-secret-page.png)
1. 立即將新機密的值複製到安全位置。 填充值僅顯示一次。

    ![用戶端密碼](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>服務匯流排 API 的許可權
如果應用程式是主控台應用程式，則必須註冊本機應用程式，並將**Microsoft.ServiceBus**的 API 許可權添加到**所需的許可權**集。 本機應用程式還需要 Azure AD 中的**重定向 URI，** 該 URI 充當識別碼;URI 不需要是網路目標。 在此範例中使用 `https://servicebus.microsoft.com`，因為程式碼範例已使用該 URI。

### <a name="client-libraries-for-token-acquisition"></a>用於權杖獲取的用戶端庫  
註冊應用程式並授予應用程式在 Azure 服務匯流排中發送/接收資料的許可權後，可以將代碼添加到應用程式以驗證安全主體並獲取 OAuth 2.0 權杖。 要驗證和獲取權杖，可以使用其中一個[Microsoft 標識平臺身份驗證庫](../active-directory/develop/reference-v2-libraries.md)或支援 OpenID 或 Connect 1.0 的另一個開源庫。 然後，應用程式可以使用訪問權杖授權針對 Azure 服務匯流排的請求。

有關支援獲取權杖的方案清單，請參閱 .NET GitHub 存儲庫的[Microsoft 身份驗證庫 （MSAL）](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [的方案](https://aka.ms/msal-net-scenarios)部分。

## <a name="sample-on-github"></a>GitHub 上的範例
請參閱 GitHub 上的以下示例：[服務匯流排的角色基存取控制](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)。 

使用**用戶端機密登錄**選項，而不是**互動式使用者登錄**選項。 使用用戶端機密選項時，看不到快顯視窗。 應用程式使用租戶 ID 和應用 ID 進行身份驗證。 

### <a name="run-the-sample"></a>執行範例

在運行示例之前，請編輯**app.config**檔，並根據方案設置以下值：

- `tenantId`：設定為 **TenantId** 值。
- `clientId`：設定為 **ApplicationId** 值。
- `clientSecret`：如果您要使用用戶端密碼來登入，請在 Azure AD 中建立該用戶端密碼。 另外，請使用 Web 應用程式或 API 而非使用原生應用程式。 還有，請將應用程式新增到您先前所建立之命名空間中的 [存取控制 (IAM)]**** 底下。
- `serviceBusNamespaceFQDN`：設定為新建立之服務匯流排命名空間的完整 DNS 名稱；例如 `example.servicebus.windows.net`。
- `queueName`：設定為您所建立之佇列的名稱。
- 您在前面步驟的應用程式中所指定的重新導向 URI。

運行主控台應用程式時，系統會提示您選擇方案。 通過鍵入其編號並按 ENTER 選擇**互動式使用者登錄**。 應用程式隨即會顯示登入視窗，要求您同意存取服務匯流排，然後使用該服務並利用登入身分識別來執行傳送/接收案例。


## <a name="next-steps"></a>後續步驟
- 要瞭解有關 RBAC 的更多內容，請參閱[什麼是基於角色的存取控制 （RBAC）？](../role-based-access-control/overview.md)
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派和管理 RBAC 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure 資源管理器範本管理基於角色的存取控制 （RBAC）](../role-based-access-control/role-assignments-template.md)

若要深入了解服務匯流排訊息，請參閱下列主題。

- [服務匯流排 RBAC 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
- [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
- [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)
