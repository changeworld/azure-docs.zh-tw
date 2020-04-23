---
title: 透過 Azure Active Directory 授與存取權
description: 本文提供有關使用 Azure 活動目錄授權訪問事件中心資源的資訊。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 6216b56c8e8f0de4f9cd60306bbf9c5ed49a11ad
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025198"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>使用 Azure 活動目錄授權存取事件中心資源
Azure 事件中心支援使用 Azure 活動目錄 (Azure AD) 授權對事件中心資源的請求。 使用 Azure AD,可以使用基於角色的存取控制 (RBAC) 向安全主體(可能是使用者)或應用程式服務主體授予許可權。 要瞭解有關角色和角色分配的更多內容,請參閱[瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概觀
當安全主體(使用者或應用程式)嘗試訪問事件中心資源時,必須授權請求。 使用 Azure AD,訪問資源是一個兩步過程。 

 1. 首先,安全主體的標識經過身份驗證,並返回 OAuth 2.0 令牌。 要求權杖的資源名稱稱為`https://eventhubs.azure.net/`。 對 Kafka 客戶端,要求權杖`https://<namespace>.servicebus.windows.net`資源為 。
 1. 接下來,令牌作為請求的一部分傳遞給事件中心服務,以授權對指定資源的訪問。

身份驗證步驟要求應用程式請求在運行時包含 OAuth 2.0 訪問權杖。 如果應用程式在 Azure 實體(如 Azure VM、虛擬機縮放集或 Azure 函數應用)內運行,則可以使用託管標識訪問資源。 要瞭解如何驗證託管識別對事件中心服務發出的請求,請參閱使用 Azure[活動目錄和 Azure 資源的託管標識對 Azure 事件中心資源進行身份驗證存取](authenticate-managed-identity.md)。 

授權步驟要求將一個或多個 RBAC 角色分配給安全主體。 Azure 事件中心提供 RBAC 角色,這些角色包含事件中心資源的許可權集。 分配給安全主體的角色確定主體將具有的許可權。 有關 RBAC 角色的詳細資訊,請參閱[Azure 事件中心的內建 RBAC 角色](#built-in-rbac-roles-for-azure-event-hubs)。 

向事件中心發出請求的本機應用程式和 Web 應用程式也可以使用 Azure AD 進行授權。 要瞭解如何要求存取權杖並使用它授權對事件中心資源的請求,請參閱[從應用程式使用 Azure AD 對 Azure 事件中心進行身份驗證存取](authenticate-application.md)。 

## <a name="assign-rbac-roles-for-access-rights"></a>為存取權限配置 RBAC 角色
Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 事件中心定義一組內建 RBAC 角色,這些角色包含用於訪問事件中心數據的常見許可權集,還可以定義用於訪問數據的自定義角色。

將 RBAC 角色分配給 Azure AD 安全主體時,Azure 會授予對該安全主體的這些資源的訪問許可權。 訪問範圍可限定為訂閱級別、資源組、事件中心命名空間或其下的任何資源。 Azure AD 安全主體可以是使用者、應用程式服務主體或[Azure 資源的託管識別](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Azure 事件中心內建 RBAC 角色
Azure 提供以下內建 RBAC 角色,用於使用 Azure AD 和 Auth 授權存取事件中心資料:

- [Azure 事件中心數據擁有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner):使用此角色可以完全訪問事件中心資源。
- [Azure 事件中心數據發送方](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver):使用此角色授予對事件中心資源的發送訪問許可權。
- [Azure 事件中心數據接收器](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender):使用此角色授予對事件中心資源的使用/接收訪問許可權。

## <a name="resource-scope"></a>資源範圍 
在將 RBAC 角色分配給安全主體之前,請確定安全主體應具有的訪問範圍。 最佳做法規定,最好只授予盡可能窄的範圍。

以下清單描述了對事件中心資源的訪問的範圍,從最窄的範圍開始:

- **消費者組**:在此範圍內,角色分配僅適用於此實體。 目前,Azure 門戶不支援在此級別將 RBAC 角色分配給安全主體。 
- **事件中心**:角色分配應用於事件中心實體及其下的消費者組。
- **命名空間**:角色分配跨越命名空間下事件中心的整個拓撲以及與其關聯的消費者組。
- **資源組**:角色分配應用於資源組下的所有事件中心資源。
- **訂閱**:角色分配應用於訂閱中的所有資源組中的所有事件中心資源。

> [!NOTE]
> - 請記住,RBAC 角色分配可能需要長達五分鐘才能傳播。 
> - 此內容適用於 Apache Kafka 的事件中心和事件中心。 有關 Kafka 支援的事件中心的詳細資訊,請參閱[Kafka - 安全和身份驗證的事件中心](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)。


有關如何定義內建角色的詳細資訊,請參閱[瞭解角色定義](../role-based-access-control/role-definitions.md#management-and-data-operations)。 有關建立自訂 RBAC 角色的資訊,請參考[Azure 的控制程式產生 Azure 的控制程式產生自訂角色](../role-based-access-control/custom-roles.md)。



## <a name="samples"></a>範例
- [微軟.Azure.事件中心範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    這些示例使用舊的**Microsoft.Azure.EventHubs**庫,但可以輕鬆地將其更新為使用最新的**Azure.消息.事件中心**庫。 要將範例從使用舊函式庫移到新函式庫,請參閱[從 Microsoft.Azure.事件中心遷移到 Azure.訊息集的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)。
- [Azure.訊息傳遞.事件中心範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    此示例已更新為使用最新的**Azure.消息.事件中心**庫。
- [卡夫卡 -奧烏特樣品事件中心](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)。 


## <a name="next-steps"></a>後續步驟
- 瞭解如何將內建 RBAC 角色分配給安全主體,請參閱[使用 Azure 活動目錄對事件中心資源的身份驗證存取權限](authenticate-application.md)。
- [使用 RBAC 建立自訂角色](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)。
- [瞭解如何將 Azure 活動目錄與 EH 一起使用](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

請參考以下的文章:

- [使用 Azure 活動目錄對應用程式對 Azure 事件中心的要求進行身份驗證](authenticate-application.md)
- [使用 Azure 活動目錄對託管識別進行身分驗證以存取事件中心資源](authenticate-managed-identity.md)
- [使用分享存取簽署對 Azure 事件中心的要求進行身份驗證](authenticate-shared-access-signature.md)
- [使用分享存取簽署授權存取事件中心資源](authorize-access-shared-access-signature.md)
