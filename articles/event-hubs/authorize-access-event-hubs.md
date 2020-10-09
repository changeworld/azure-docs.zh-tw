---
title: 授與 Azure 事件中樞的存取權
description: 本文提供授權存取 Azure 事件中樞資源之不同選項的相關資訊。
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 18b8bd80eaec316fbaefadad0dd7a19418bfa838
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85323170"
---
# <a name="authorize-access-to-azure-event-hubs"></a>授與 Azure 事件中樞的存取權
每次您發佈或取用事件中樞的事件/資料時，您的用戶端都會嘗試存取事件中樞資源。 每個對安全資源的要求都必須獲得授權，服務才能確保用戶端具有發行/取用資料的必要許可權。 

Azure 事件中樞提供下列選項來授權存取安全資源：

- Azure Active Directory
- 共用存取簽章

> [!NOTE]
> 本文適用于事件中樞和 [Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) 案例。 

## <a name="azure-active-directory"></a>Azure Active Directory
事件中樞資源的 Azure Active Directory (Azure AD) 整合提供了角色型存取控制 (RBAC) ，以針對用戶端對資源的存取進行細微的控制。 您可以使用角色型存取控制 (RBAC) 將許可權授與安全性主體，該許可權可能是使用者、群組或應用程式服務主體。 安全性主體會由 Azure AD 進行驗證，以傳回 OAuth 2.0 權杖。 權杖可以用來授權存取事件中樞資源的要求。

如需有關使用 Azure AD 進行驗證的詳細資訊，請參閱下列文章：

- [使用 Azure Active Directory 驗證 Azure 事件中樞的要求](authenticate-application.md)
- [使用 Azure Active Directory 授權存取事件中樞資源](authorize-access-azure-active-directory.md)。

## <a name="shared-access-signatures"></a>共用存取簽章 
事件中樞資源 (SAS) 的共用存取簽章，可提供事件中樞資源的有限委派存取權。 針對簽章有效或其授與許可權的時間間隔加入條件約束，可提供管理資源的彈性。 如需詳細資訊，請參閱 [使用共用存取簽章 (SAS) 進行驗證 ](authenticate-shared-access-signature.md)。 

使用 Azure AD 所傳回的 OAuth 2.0 權杖授權使用者或應用程式，可提供更高的安全性，並讓您輕鬆地透過 (SAS) 的共用存取簽章使用。 使用 Azure AD，就不需要在程式碼中儲存存取權杖，也不需要風險潛在的安全性弱點。 雖然您可以繼續使用共用存取簽章 (SAS) 將更細緻的存取權授與事件中樞資源，Azure AD 提供類似的功能，而不需要管理 SAS 權杖或擔心會撤銷遭盜用的 SAS。 

根據預設，所有事件中樞資源都會受到保護，而且只有帳戶擁有者可以使用。 雖然您可以使用上面所述的任何授權策略來授與用戶端事件中樞資源的存取權。 Microsoft 建議盡可能使用 Azure AD，以獲得最高的安全性和使用便利性。

如需使用 SAS 進行授權的詳細資訊，請參閱 [使用共用存取簽章授權存取事件中樞資源](authorize-access-shared-access-signature.md)。

## <a name="next-steps"></a>後續步驟
- 請參閱 GitHub 存放庫中發佈的 [RBAC 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 。 
- 請參閱下列文章：
    - [使用 Azure Active Directory 驗證從應用程式 Azure 事件中樞的要求](authenticate-application.md)
    - [使用 Azure Active Directory 來驗證受控識別，以存取事件中樞資源](authenticate-managed-identity.md)
    - [使用共用存取簽章驗證 Azure 事件中樞的要求](authenticate-shared-access-signature.md)
    - [使用 Azure Active Directory 授權存取事件中樞資源](authorize-access-azure-active-directory.md)
    - [使用共用存取簽章授權存取事件中樞資源](authorize-access-shared-access-signature.md)

