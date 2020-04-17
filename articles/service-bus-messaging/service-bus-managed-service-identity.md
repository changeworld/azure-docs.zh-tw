---
title: 搭配服務匯流排之 Azure 資源的受控識別
description: 本文介紹如何使用託管標識訪問 Azure 服務總線實體(佇列、主題和訂閱)。
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 46a1db94d576174b837a40c646fcf9e082e339c8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461611"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>使用 Azure 活動目錄對託管識別進行身分驗證以存取 Azure 服務匯流排資源
[Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)是一個跨 Azure 功能，可讓您建立與應用程式程式碼執行所在之部署相關聯的安全識別。 然後您可以將該識別與存取控制角色產生關連，該角色會授與用來存取應用程式所需之特定 Azure 資源的自訂權限。

使用受控識別，Azure 平台就能管理此執行階段識別。 您不需要為了識別本身或您需要存取的資源，在應用程式程式碼或設定中儲存及保護存取金鑰。 若服務匯流排用戶端應用程式在 Azure App Service 應用程式中執行，或在已啟用 Azure 資源的受控識別支援的虛擬機器中執行，則不需處理 SAS 規則和金鑰，或任何其他存取權杖。 用戶端應用程式只需要服務匯流排傳訊命名空間的端點位址。 當應用程式連線時，服務匯流排會將受控實體的內容繫結至作業 (在此文章稍後的範例顯示) 中的用戶端。 一旦它與受控識別相關聯，您的服務匯流排用戶端就能執行所有授權的作業。 授權是藉由將受控實體與服務匯流排角色相關聯來授與。 

## <a name="overview"></a>概觀
當安全主體(使用者、組或應用程式)嘗試訪問服務總線實體時,必須授權該請求。 使用 Azure AD,訪問資源是一個兩步過程。 

 1. 首先,安全主體的標識經過身份驗證,並返回 OAuth 2.0 令牌。 要求權杖的資源名稱稱為`https://servicebus.azure.net`。
 1. 接下來,令牌作為請求的一部分傳遞給服務總線服務,以授權對指定資源的訪問。

身份驗證步驟要求應用程式請求在運行時包含 OAuth 2.0 訪問權杖。 如果應用程式在 Azure 實體(如 Azure VM、虛擬機縮放集或 Azure 函數應用)內運行,則可以使用託管標識訪問資源。 

授權步驟要求將一個或多個 RBAC 角色分配給安全主體。 Azure 服務總線提供 RBAC 角色,這些角色包含服務總線資源的許可權集。 分配給安全主體的角色確定主體將具有的許可權。 要瞭解有關將 RBAC 角色分配給 Azure 服務總線的更多詳細資訊,請參閱[Azure 服務匯流排的內建 RBAC 角色](#built-in-rbac-roles-for-azure-service-bus)。 

向服務總線發出請求的本機應用程式和 Web 應用程式也可以使用 Azure AD 進行授權。 本文介紹如何請求訪問令牌,並用它來授權服務總線資源的請求。 


## <a name="assigning-rbac-roles-for-access-rights"></a>為存取權限配置 RBAC 角色
Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 服務總線定義了一組內建 RBAC 角色,這些角色包含用於存取服務總線實體的通用許可權集,還可以定義用於存取資料的自定義角色。

將 RBAC 角色分配給 Azure AD 安全主體時,Azure 會授予對該安全主體的這些資源的訪問許可權。 訪問範圍可限定為訂閱級別、資源組或服務總線命名空間。 Azure AD 安全主體可以是 Azure 資源的使用者、組、應用程式服務主體或託管標識。

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Azure 服務匯流排的內建 RBAC 角色
對於 Azure 服務匯流排來說，透過 Azure 入口網站和 Azure 資源管理 API 來的管理命名空間和所有相關資源的作業，已使用「角色型存取控制** (RBAC)」模型來加以保護。 Azure 提供以下內建 RBAC 角色,用於授權存取服務總線命名空間:

- [Azure 服務匯流排資料擁有者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner):啟用對服務總線命名空間及其實體(佇列、主題、訂閱和篩選器)的資料存取
- [Azure 服務總線數據發送方](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender):使用此角色授予對服務總線命名空間及其實體的發送訪問許可權。
- [Azure 服務總線數據接收器](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver):使用此角色授予對服務總線命名空間及其實體的接收訪問許可權。 

## <a name="resource-scope"></a>資源範圍 
在將 RBAC 角色分配給安全主體之前,請確定安全主體應具有的訪問範圍。 最佳做法規定,最好只授予盡可能窄的範圍。

下面的清單描述了您可以從最窄的範圍開始對服務總線資源的存取的範圍的級別:

- **佇列**、**主題**或**訂閱**:角色分配適用於特定的服務總線實體。 目前,Azure 門戶不支援在訂閱級別將使用者/組/託管標識分配給服務總線 RBAC 角色。 下面是使用 Azure CLI 指令的範例[:az 角色配置建立](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)來將識別分配給服務總線 RBAC 角色: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **服務總線命名空間**:角色分配跨越命名空間下服務總線的整個拓撲以及與其關聯的消費者組。
- **資源組**:角色分配應用於資源組下的所有服務總線資源。
- **訂閱**:角色分配應用於訂閱中的所有資源組中的所有服務總線資源。

> [!NOTE]
> 請記住,RBAC 角色分配可能需要長達五分鐘才能傳播。 

有關如何定義內建角色的詳細資訊,請參閱[瞭解角色定義](../role-based-access-control/role-definitions.md#management-and-data-operations)。 有關建立自訂 RBAC 角色的資訊,請參考[Azure 的控制程式產生 Azure 的控制程式產生自訂角色](../role-based-access-control/custom-roles.md)。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上啟用受控識別
在可以使用 Azure Resources 的託管標識從 VM 授權服務總線資源之前,必須首先在 VM 上為 Azure 資源啟用託管標識。 若要了解如何啟用 Azure 資源的受控識別，請參閱下列其中一篇文章：

- [Azure 入口網站](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 範本](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 資源管理員用戶端庫](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>向 Azure AD 中的管理識別權碼識別權限
要授權從應用程式中的託管標識請求服務總線服務,請先為該託管標識配置基於角色的訪問控制 (RBAC) 設置。 Azure 服務總線定義 RBAC 角色,這些角色包括從服務總線發送和讀取的許可權。 將 RBAC 角色分配給託管標識時,將授予託管標識對相應作用域的服務總線實體的訪問許可權。

有關配置 RBAC 角色的詳細資訊,請參考[Azure 的活動目錄進行身份驗證與授權來存取服務總線資源](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus)。

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>搭配使用服務匯流排和 Azure 資源的受控識別
要使用具有託管標識的服務總線,您需要為標識分配角色和適當的作用域。 本節中的過程使用在託管標識下運行並訪問服務總線資源的簡單應用程式。

在這裏,我們使用在[Azure 應用服務](https://azure.microsoft.com/services/app-service/)中託管的範例 Web 應用程式。 有關建立 Web 應用程式的分步說明,請參閱在 Azure[中建立 ASP.NET 核心 Web 應用](../app-service/app-service-web-get-started-dotnet.md)

建立應用程式後,按照以下步驟操作: 

1. 轉到 **"設定"** 並選擇 **"標識**"。 
1. 選擇「**狀態**為**打開**」。 
1. 選取 [儲存]**** 以儲存設定。 

    ![Web 應用的託管識別](./media/service-bus-managed-service-identity/identity-web-app.png)

啟用此設定後,將在 Azure 活動目錄 (Azure AD) 中創建新的服務標識,並將其配置到應用服務主機中。

現在,將此服務標識分配給服務總線資源中所需作用域中的角色。

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 門戶配置 RBAC 角色
要將角色分配給服務總線命名空間,請導航到 Azure 門戶中的命名空間。 顯示資源的存取控制 (IAM) 設定,並按照以下說明管理角色分配:

> [!NOTE]
> 以下步驟將服務標識角色分配給服務總線命名空間。 您可以按照相同的步驟在其他受支援的範圍(資源組和訂閱)中分配角色。 
> 
> 如果沒有[服務總線消息命名空間](service-bus-create-namespace-portal.md),則創建命名空間。 

1. 在 Azure 門戶中,導覽到服務匯流排的空間並顯示命名空間的**概述**。 
1. 選擇左側功能表上**的存取控制 (IAM)** 以顯示服務總線命名空間的存取控制設定。
1.  選取 [角色指派]**** 索引標籤，以查看角色指派的清單。
3.  選擇 **「新增」** 以新增新角色。
4.  在「**新增角色分配」** 頁上,選擇要分配的 Azure 服務總線角色。 然後搜索以查找已註冊的服務標識以分配角色。
    
    ![新增角色分配頁](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  選取 [儲存]  。 您對其指派角色的身分識別會出現在該角色下方。 例如,下圖顯示服務標識具有 Azure 服務總線數據擁有者。
    
    ![配置給角色的識別](./media/service-bus-managed-service-identity/role-assigned.png)

分配角色後,Web 應用程式將有權訪問定義範圍內的服務總線實體。 

### <a name="run-the-app"></a>執行應用程式

現在修改您建立之 ASP.NET 應用程式的預設分頁。 您可以使用來自[這個 GitHub 存放庫](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)的 Web 應用程式程式碼。  

Default.aspx 頁面是您的登陸頁面。 您可以在 Default.aspx.cs 檔案中找到程式碼。 結果是最小的 Web 應用程式，具有數個項目欄位，以及具有連線到服務匯流排的 [傳送]**** 和 [接收]**** 按鈕，以傳送或接收訊息。

請注意 [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 物件如何初始化。 程式碼不會使用共用存取權杖 (SAS) 權杖提供者，而會改用 `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` 呼叫來建立受控識別的權杖提供者。 因此，不會保留及使用密碼。 受控識別內容到服務匯流排和授權交握的流程，都是由權杖提供者自動處理。 它是比使用 SAS 更簡單的模型。

當您進行這些變更之後，請發行並執行應用程式。 您只要下載發行設定檔然後在 Visual Studio 中匯入，就能取得正確的發行資料：

![取得設定檔](./media/service-bus-managed-service-identity/msi3.png)
 
若要傳送或接收訊息，請輸入命名空間名稱和您所建立之實體的名稱。 然後，按一下 [傳送]**** 或 [接收]****。


> [!NOTE]
> - 受控識別只能在 Azure 環境中、在應用程式服務、Azure VM 和擴展集上運作。 對於 .NET 應用程式，Microsoft.Azure.Services.AppAuthentication 程式庫 (由服務匯流排 NuGet 套件使用) 提供讓應用程式透過此通訊協定進行提取的功能，也能支援本機部署經驗。 該程式庫還能讓您使用來自 Visual Studio、Azure CLI 2.0 或 Active Directory 整合式驗證的使用者帳戶，在部署機器上以本機方式測試程式碼。 如需使用此程式庫的本機開發選項詳細資訊，請參閱[使用 .NET 對 Azure Key Vault 進行服務對服務驗證](../key-vault/general/service-to-service-authentication.md)。  
> 
> - 受控識別目前不會使用 App Service 部署位置。

## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)
