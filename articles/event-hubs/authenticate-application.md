---
title: 驗證應用程式以存取 Azure 事件中心資源
description: 本文提供有關使用 Azure 活動目錄驗證應用程式以存取 Azure 事件中心資源的資訊
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 4cef49f138b96848b8e59cb5b2d0b185d4568aa9
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520989"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>使用 Azure 活動目錄對應用程式進行身份驗證以存取事件中心資源
Microsoft Azure 針對以 Azure Active Directory (Azure AD) 為基礎的資源和應用程式提供了整合式的存取控制管理功能。 將 Azure AD 與 Azure 事件中心一起使用的一個關鍵優點是,您不再需要在代碼中儲存認證。 相反,您可以從 Microsoft 標識平臺請求 OAuth 2.0 訪問權杖。 請求權杖的資源名稱是`https://eventhubs.azure.net/`(對於 Kafka 用戶端,請求權杖`https://<namespace>.servicebus.windows.net`的資源為 )。 Azure AD 對執行應用程式的安全主體(使用者、組或服務主體)進行身份驗證。 如果身份驗證成功,Azure AD 會向應用程式返回訪問權杖,然後應用程式可以使用存取權杖授權對 Azure 事件中心資源的請求。

將角色分配給 Azure AD 安全主體時,Azure 會授予對該安全主體的這些資源的訪問。 訪問範圍可限定為訂閱級別、資源組、事件中心命名空間或其下的任何資源。 Azure AD 安全性可以為 Azure 資源配置角色給使用者、組、應用程式服務主體或[託管識別](../active-directory/managed-identities-azure-resources/overview.md)。 

> [!NOTE]
> 角色定義是一個權限集合。 基於角色的訪問控制 (RBAC) 控制如何通過角色分配來強制執行這些許可權。 角色指派由三項元素所組成：安全性主體、角色定義和範圍。 有關詳細資訊,請參閱[瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="built-in-roles-for-azure-event-hubs"></a>Azure 事件中心的內建角色
Azure 提供以下內建 RBAC 角色,用於使用 Azure AD 和 Auth 授權存取事件中心資料:

- [Azure 事件中心數據擁有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner):使用此角色可以完全訪問事件中心資源。
- [Azure 事件中心數據發送方](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender):使用此角色授予對事件中心資源的發送訪問許可權。
- [Azure 事件中心數據接收器](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver):使用此角色授予對事件中心資源的接收訪問許可權。   

> [!IMPORTANT]
> 我們的預覽版本支援將事件中心數據訪問許可權添加到擁有者或參與者角色。 但是,不再授予擁有者和參與者角色的數據訪問許可權。 如果使用"擁有者"或"參與者"角色,請切換到使用 Azure 事件中心數據擁有者角色。

## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 門戶配置 RBAC 角色  
要瞭解有關使用 RBAC 和 Azure 閘戶管理對 Azure 資源的存取的更多內容,請參閱[本文](..//role-based-access-control/role-assignments-portal.md)。 

確定角色分配的適當範圍后,在 Azure 門戶中導航到該資源。 顯示資源的存取控制 (IAM) 設定,並按照以下說明管理角色分配:

> [!NOTE]
> 下面介紹的步驟在事件中心命名空間下將角色分配給事件中心,但您可以按照相同的步驟分配範圍為任何事件中心資源的角色。

1. 在[Azure 門戶](https://portal.azure.com/)中,導航到事件中心命名空間。
2. 在 **「概述」** 頁上,選擇要為其分配角色的事件中心。

    ![選擇活動中心](./media/authenticate-application/select-event-hub.png)
1. 選擇 **「存取控制」(IAM)** 以顯示事件中心的存取控制設定。 
1. 選取 [角色指派]**** 索引標籤，以查看角色指派的清單。 選擇工具列上的 **「添加**」按鈕,然後選擇 **「添加角色分配**」。 

    ![在工具列上新增按鈕](./media/authenticate-application/role-assignments-add-button.png)
1. 在 **'新增角色配置'** 頁上,執行以下步驟:
    1. 選擇要分配的**事件中心角色**。 
    1. 搜索以查找要為其分配角色**的安全主體**(使用者、組、服務主體)。
    1. 選擇 **「儲存」** 以儲存角色分配。 

        ![將角色配置給使用者](./media/authenticate-application/assign-role-to-user.png)
    4. 您對其指派角色的身分識別會出現在該角色下方。 例如,下圖顯示 Azure 用戶位於 Azure 事件中心數據擁有者角色中。 
        
        ![清單中的使用者](./media/authenticate-application/user-in-list.png)

您可以按照類似的步驟將角色分配給事件中心命名空間、資源組或訂閱。 定義角色及其範圍后,可以使用[此 GitHub 位置中的](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)範例測試此行為。


## <a name="authenticate-from-an-application"></a>從應用程式進行驗證
將 Azure AD 與事件中心一起使用的一個關鍵優點是,您的憑據不再需要存儲在代碼中。 相反,您可以從 Microsoft 標識平臺請求 OAuth 2.0 訪問權杖。 Azure AD 對執行應用程式的安全主體(使用者、組或服務主體)進行身份驗證。 如果身份驗證成功,Azure AD 會將訪問權杖返回到應用程式,然後應用程式可以使用存取權杖授權對 Azure 事件中心的請求。

以下各節介紹如何配置本機應用程式或 Web 應用程式以使用 Microsoft 標識平臺 2.0 進行身份驗證。 有關 Microsoft 識別平臺 2.0 的詳細資訊,請參閱[Microsoft 標識平臺 (v2.0) 概述](../active-directory/develop/v2-overview.md)。

如需 OAuth 2.0 程式碼授與流程的概觀，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../active-directory/develop/v2-oauth2-auth-code-flow.md)。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>向 Azure AD 租用戶註冊應用程式
使用 Azure AD 授權事件中心資源的第一步是從[Azure 門戶](https://portal.azure.com/)向 Azure AD 租戶註冊用戶端應用程式。 註冊用戶端應用程式時,向AD提供有關該應用程式的資訊。 然後,Azure AD 提供一個用戶端 ID(也稱為應用程式 ID),可用於將應用程式與 Azure AD 運行時相關聯。 若要深入了解用戶端識別碼，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。 

下圖顯示了註冊 Web 應用程式的步驟:

![註冊應用程式](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 如果將應用程式註冊為本機應用程式,則可以為重定向URI指定任何有效的URI。 對於本機應用程式,此值不必是真正的 URL。 對於 Web 應用程式,重定向 URI 必須是有效的 URI,因為它指定提供令牌的 URL。

註冊應用程式後,您將在 **「設定」** 下看到**應用程式(用戶端)ID:**

![已登記申請的申請 ID](./media/authenticate-application/application-id.png)

如需有關向 Azure AD 註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md)。


### <a name="create-a-client-secret"></a>建立用戶端密碼   
應用程式在請求權杖時需要用戶端金鑰來證明其身份。 要添加用戶端機密,請按照以下步驟操作。

1. 導航到 Azure 門戶中的應用註冊。
1. 選擇 **"證書&機密**設置。
1. 在**用戶端機密**「下,選擇 **「新用戶端」秘密**以創建新機密。
1. 提供機密的說明,並選擇所需的過期間隔。
1. 立即將新機密的值複製到安全位置。 填充值僅顯示一次。

    ![用戶端密碼](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>存取的用戶端庫  
註冊應用程式並授予應用程式在 Azure 事件中心中發送/接收數據的許可權後,可以將代碼添加到應用程式以驗證安全主體並獲取 OAuth 2.0 權杖。 要驗證和獲取權杖,可以使用其中一個[Microsoft識別平臺身份驗證庫](../active-directory/develop/reference-v2-libraries.md)或支援OpenID或Connect 1.0的另一個開源庫。 然後,應用程式可以使用訪問權杖授權針對 Azure 事件中心的請求。

有關支援獲取權杖的方案清單,請參閱 .NET GitHub 儲存庫的[Microsoft 身份驗證庫 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [的方案](https://aka.ms/msal-net-scenarios)部分。

## <a name="samples"></a>範例
- [微軟.Azure.事件中心範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    這些示例使用舊的**Microsoft.Azure.EventHubs**庫,但可以輕鬆地將其更新為使用最新的**Azure.消息.事件中心**庫。 要將範例從使用舊函式庫移到新函式庫,請參閱[從 Microsoft.Azure.事件中心遷移到 Azure.訊息集的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)。
- [Azure.訊息傳遞.事件中心範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    此示例已更新為使用最新的**Azure.消息.事件中心**庫。

## <a name="next-steps"></a>後續步驟
- 要瞭解有關 RBAC 的更多內容,請參閱[什麼是基於角色的訪問控制 (RBAC)?](../role-based-access-control/overview.md)
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派和管理 RBAC 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure 資源管理員樣本管理基於角色的存取控制 (RBAC)](../role-based-access-control/role-assignments-template.md)

請參考以下的文章:
- [使用 Azure 活動目錄對託管識別進行身分驗證以存取事件中心資源](authenticate-managed-identity.md)
- [使用分享存取簽署對 Azure 事件中心的要求進行身份驗證](authenticate-shared-access-signature.md)
- [使用 Azure 活動目錄授權存取事件中心資源](authorize-access-azure-active-directory.md)
- [使用分享存取簽署授權存取事件中心資源](authorize-access-shared-access-signature.md)

