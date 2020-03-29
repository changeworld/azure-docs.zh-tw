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
ms.openlocfilehash: 1fa8f7a48c03ead7e939185b23834b3049b3e21c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064867"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>使用 Azure 活動目錄授權訪問事件中心資源
Azure 事件中心支援使用 Azure 活動目錄 （Azure AD） 授權對事件中心資源的請求。 使用 Azure AD，可以使用基於角色的存取控制 （RBAC） 向安全主體（可能是使用者）或應用程式服務主體授予許可權。 要瞭解有關角色和角色指派的更多內容，請參閱[瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>總覽
當安全主體（使用者或應用程式）嘗試訪問事件中心資源時，必須授權請求。 使用 Azure AD，訪問資源是一個兩步過程。 

 1. 首先，安全主體的標識經過身份驗證，並返回 OAuth 2.0 權杖。 請求權杖的資源名稱為`https://eventhubs.azure.net/`。 對於 Kafka 用戶端，請求權杖的資源為`https://<namespace>.servicebus.windows.net`。
 1. 接下來，權杖作為請求的一部分傳遞給事件中心服務，以授權對指定資源的訪問。

身份驗證步驟要求應用程式請求在運行時包含 OAuth 2.0 訪問權杖。 如果應用程式在 Azure 實體（如 Azure VM、虛擬機器縮放集或 Azure 函數應用）內運行，則可以使用託管標識訪問資源。 要瞭解如何驗證託管標識對事件中心服務發出的請求，請參閱使用 Azure[活動目錄和 Azure 資源的託管標識對 Azure 事件中心資源進行身份驗證訪問](authenticate-managed-identity.md)。 

授權步驟要求將一個或多個 RBAC 角色指派給安全主體。 Azure 事件中心提供 RBAC 角色，這些角色包含事件中心資源的許可權集。 分配給安全主體的角色確定主體將具有的許可權。 有關 RBAC 角色的詳細資訊，請參閱[Azure 事件中心的內置 RBAC 角色](#built-in-rbac-roles-for-azure-event-hubs)。 

向事件中心發出請求的本機應用程式和 Web 應用程式也可以使用 Azure AD 進行授權。 要瞭解如何請求訪問權杖並使用它授權對事件中心資源的請求，請參閱[從應用程式使用 Azure AD 對 Azure 事件中心進行身份驗證訪問](authenticate-application.md)。 

## <a name="assign-rbac-roles-for-access-rights"></a>為存取權限分配 RBAC 角色
Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 事件中心定義一組內置 RBAC 角色，這些角色包含用於訪問事件中心資料的常見許可權集，還可以定義用於訪問資料的自訂角色。

將 RBAC 角色指派給 Azure AD 安全主體時，Azure 會授予對該安全主體的這些資源的存取權限。 訪問範圍可限定為訂閱級別、資源組、事件中心命名空間或其下的任何資源。 Azure AD 安全主體可以是使用者、應用程式服務主體或[Azure 資源的託管標識](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Azure 事件中心內置 RBAC 角色
Azure 提供了以下內置 RBAC 角色，用於使用 Azure AD 和 Auth 授權訪問事件中心資料：

- [Azure 事件中心資料擁有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)：使用此角色可以完全訪問事件中心資源。
- [Azure 事件中心資料發送方](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)：使用此角色授予對事件中心資源的發送存取權限。
- [Azure 事件中心資料接收器](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)：使用此角色授予對事件中心資源的使用/接收存取權限。

## <a name="resource-scope"></a>資源範圍 
在將 RBAC 角色指派給安全主體之前，請確定安全主體應具有的訪問範圍。 最佳做法規定，最好只授予盡可能窄的範圍。

以下清單描述了對事件中心資源的訪問的範圍，從最窄的範圍開始：

- **消費者組**：在此範圍內，角色指派僅適用于此實體。 目前，Azure 門戶不支援在此級別將 RBAC 角色指派給安全主體。 
- **事件中心**：角色指派應用於事件中心實體及其下的消費者組。
- **命名空間**：角色指派跨越命名空間下事件中心的整個拓撲以及與其關聯的消費者組。
- **資源組**：角色指派應用於資源組下的所有事件中心資源。
- **訂閱**：角色指派應用於訂閱中的所有資源組中的所有事件中心資源。

> [!NOTE]
> - 請記住，RBAC 角色指派可能需要長達五分鐘才能傳播。 
> - 此內容適用于 Apache Kafka 的事件中心和事件中心。 有關 Kafka 支援的事件中心的詳細資訊，請參閱[Kafka - 安全和身份驗證的事件中心](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)。


有關如何定義內置角色的詳細資訊，請參閱[瞭解角色定義](../role-based-access-control/role-definitions.md#management-and-data-operations)。 有關創建自訂 RBAC 角色的資訊，請參閱[為 Azure 基於角色的存取控制創建自訂角色](../role-based-access-control/custom-roles.md)。



## <a name="samples"></a>範例
- [微軟.Azure.事件中心示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    這些示例使用舊的**Microsoft.Azure.EventHubs**庫，但可以輕鬆地將其更新為使用最新的**Azure.消息.事件中心**庫。 要將示例從使用舊庫移動到新庫，請參閱[從 Microsoft.Azure.事件中心遷移到 Azure.消息集的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)。
- [Azure.消息傳遞.事件中心示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    此示例已更新為使用最新的**Azure.消息.事件中心**庫。
- [卡夫卡 -奧烏特樣品事件中心](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)。 


## <a name="next-steps"></a>後續步驟
- 瞭解如何將內置 RBAC 角色指派給安全主體，請參閱[使用 Azure 活動目錄對事件中心資源的身份驗證存取權限](authenticate-application.md)。
- [瞭解如何使用 RBAC 創建自訂角色](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)。
- [瞭解如何將 Azure 活動目錄與 EH 一起使用](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

請參閱以下相關文章：

- [使用 Azure 活動目錄對應用程式對 Azure 事件中心的請求進行身份驗證](authenticate-application.md)
- [使用 Azure 活動目錄對託管標識進行身份驗證以訪問事件中心資源](authenticate-managed-identity.md)
- [使用共用訪問簽名對 Azure 事件中心的請求進行身份驗證](authenticate-shared-access-signature.md)
- [使用共用訪問簽名授權訪問事件中心資源](authorize-access-shared-access-signature.md)
