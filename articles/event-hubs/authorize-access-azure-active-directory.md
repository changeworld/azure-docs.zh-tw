---
title: 透過 Azure Active Directory 授與存取權
description: 本文提供使用 Azure Active Directory 授權存取事件中樞資源的相關資訊。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: c6d1be2825ea716c72d70cd38babbb2d95083198
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87413386"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>使用 Azure Active Directory 授權事件中樞資源的存取權
Azure 事件中樞支援使用 Azure Active Directory （Azure AD）來授權事件中樞資源的要求。 使用 Azure AD，您可以使用角色型存取控制（RBAC），將許可權授與安全性主體，這可能是使用者或應用程式服務主體。 若要深入瞭解角色和角色指派，請參閱[瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概觀
當安全性主體（使用者或應用程式）嘗試存取事件中樞資源時，要求必須獲得授權。 使用 Azure AD，對資源的存取是兩個步驟的程序。 

 1. 首先，安全性主體的身分識別已通過驗證，並傳回 OAuth 2.0 權杖。 要求權杖的資源名稱是 `https://eventhubs.azure.net/` 。 針對 Kafka 用戶端，用來要求權杖的資源是 `https://<namespace>.servicebus.windows.net` 。
 1. 接下來，權杖會當做要求的一部分傳遞給事件中樞服務，以授權存取指定的資源。

驗證步驟要求應用程式要求在執行階段包含 OAuth 2.0 存取權杖。 如果應用程式是在 azure 實體（例如 Azure VM、虛擬機器擴展集或 Azure 函式應用程式）內執行，它可以使用受控識別來存取資源。 若要瞭解如何驗證受控識別對事件中樞服務所提出的要求，請參閱[使用 Azure 資源的 Azure Active Directory 和受控識別來驗證 Azure 事件中樞資源的存取權](authenticate-managed-identity.md)。 

授權步驟需要將一或多個 RBAC 角色指派給安全性主體。 Azure 事件中樞提供 RBAC 角色，其中包含事件中樞資源的許可權集。 指派給安全性主體的角色會決定主體將擁有的許可權。 如需 RBAC 角色的詳細資訊，請參閱[適用于 Azure 事件中樞的 Azure 內建角色](#azure-built-in-roles-for-azure-event-hubs)。 

向事件中樞提出要求的原生應用程式和 web 應用程式也可以使用 Azure AD 進行授權。 若要瞭解如何要求存取權杖，並使用它來授權事件中樞資源的要求，請參閱[使用應用程式的 Azure AD 來驗證對 Azure 事件中樞的存取權](authenticate-application.md)。 

## <a name="assign-rbac-roles-for-access-rights"></a>指派存取權限的 RBAC 角色
Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 事件中樞定義一組 Azure 內建角色，其中包含用來存取事件中樞資料的常用許可權集，而且您也可以定義自訂角色來存取資料。

當 RBAC 角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取權的範圍可以是訂用帳戶層級、資源群組、事件中樞命名空間，或其底下的任何資源。 Azure AD 安全性主體可以是使用者或應用程式服務主體，或是 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>適用于 Azure 事件中樞的 Azure 內建角色
Azure 提供下列 Azure 內建角色，以使用 Azure AD 和 OAuth 來授權事件中樞資料的存取：

- [Azure 事件中樞資料擁有](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)者：使用此角色可提供完整的事件中樞資源存取權。
- [Azure 事件中樞資料](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)傳送者：使用此角色可授與事件中樞資源的存取權。
- [Azure 事件中樞資料接收器](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)：使用此角色可提供事件中樞資源的取用/接收存取權。

## <a name="resource-scope"></a>資源範圍 
將 RBAC 角色指派給安全性主體之前，請先決定安全性主體應該具備的存取範圍。 最佳做法規定，最好只授與最少的可能範圍。

下列清單說明您可以將存取範圍限定為事件中樞資源的層級，從最窄的範圍開始：

- 取用**者群組**：在此範圍中，角色指派只會套用至此實體。 目前，Azure 入口網站不支援將 RBAC 角色指派給此層級的安全性主體。 
- **事件中樞**：角色指派會套用到事件中樞實體和其下的取用者群組。
- **命名空間**：角色指派會跨越命名空間下的整個事件中樞拓撲，以及與其相關聯的取用者群組。
- **資源群組**：角色指派會套用至資源群組下的所有事件中樞資源。
- **訂**用帳戶：角色指派會套用至訂用帳戶中所有資源群組內的所有事件中樞資源。

> [!NOTE]
> - 請記住，Azure 角色指派最多可能需要五分鐘的時間來傳播。 
> - 此內容適用于 Apache Kafka 的事件中樞和事件中樞。 如需 Kafka 支援之事件中樞的詳細資訊，請參閱[Kafka 的事件中樞-安全性和驗證](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)。


如需如何定義內建角色的詳細資訊，請參閱[瞭解角色定義](../role-based-access-control/role-definitions.md#management-and-data-operations)。 如需建立 Azure 自訂角色的詳細資訊，請參閱[azure 自訂角色](../role-based-access-control/custom-roles.md)。



## <a name="samples"></a>範例
- [EventHubs 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    這些範例會使用舊的**EventHubs**程式庫，但您可以使用最新的**EventHubs**程式庫輕鬆地將其更新為。 若要將範例從使用舊的程式庫移至新的程式庫，請參閱[從 EventHubs 遷移至 EventHubs 的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)。
- [EventHubs 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    此範例已更新為使用最新的**EventHubs**程式庫。
- [Kafka-OAuth 範例的事件中樞](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)。 


## <a name="next-steps"></a>後續步驟
- 瞭解如何將內建 RBAC 角色指派給安全性主體，請參閱[使用 Azure Active Directory 來驗證事件中樞資源的存取權](authenticate-application.md)。
- 瞭解[如何使用 RBAC 建立自訂角色](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)。
- 瞭解[如何搭配 EH 使用 Azure Active Directory](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

請參閱下列相關文章：

- [使用 Azure Active Directory 驗證從應用程式 Azure 事件中樞的要求](authenticate-application.md)
- [使用 Azure Active Directory 來驗證受控識別，以存取事件中樞資源](authenticate-managed-identity.md)
- [使用共用存取簽章驗證 Azure 事件中樞的要求](authenticate-shared-access-signature.md)
- [使用共用存取簽章授權事件中樞資源的存取權](authorize-access-shared-access-signature.md)
