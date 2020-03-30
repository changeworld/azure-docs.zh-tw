---
title: 授與 Azure 事件中樞的存取權
description: 本文提供有關授權訪問 Azure 事件中心資源的不同選項的資訊。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: d4304abf0ca089fbbea86f12cd03dea836db612e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368363"
---
# <a name="authorize-access-to-azure-event-hubs"></a>授與 Azure 事件中樞的存取權
每次發佈或使用事件中心的事件/資料時，用戶端都嘗試訪問事件中心資源。 對安全資源的每個請求都必須獲得授權，以便服務能夠確保用戶端具有發佈/使用資料所需的許可權。 

Azure 事件中心提供了以下選項，用於授權訪問安全資源：

- Azure Active Directory
- 共用存取簽章

> [!NOTE]
> 本文適用于事件中心和[Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)方案。 

## <a name="azure-active-directory"></a>Azure Active Directory
事件中心資源的 Azure 活動目錄 （Azure AD） 集成提供基於角色的存取控制 （RBAC），用於細細微性地控制用戶端對資源的訪問。 您可以使用基於角色的存取控制 （RBAC） 向安全主體授予許可權，安全主體可以是使用者、組或應用程式服務主體。 安全主體由 Azure AD 進行身份驗證，以返回 OAuth 2.0 權杖。 該權杖可用於授權訪問事件中心資源的請求。

有關使用 Azure AD 進行身份驗證的詳細資訊，請參閱以下文章：

- [使用 Azure 活動目錄對 Azure 事件中心的請求進行身份驗證](authenticate-application.md)
- [使用 Azure 活動目錄 授權訪問事件中心資源](authorize-access-azure-active-directory.md)。

## <a name="shared-access-signatures"></a>共用存取簽章 
事件中心資源的共用訪問簽名 （SAS） 提供對事件中心資源的有限委派訪問。 在簽名有效的時間間隔或授予的許可權上添加約束，可以靈活地管理資源。 有關詳細資訊，請參閱[使用共用訪問簽名 （SAS） 進行身份驗證](authenticate-shared-access-signature.md)。 

使用 Azure AD 返回的 OAuth 2.0 權杖授權使用者或應用程式比共用訪問簽名 （SAS） 更安全且便於使用。 使用 Azure AD，無需將訪問權杖與代碼一起存儲，並存在潛在的安全性漏洞風險。 雖然您可以繼續使用共用訪問簽名 （SAS） 來授予對事件中心資源的細細微性訪問，但 Azure AD 提供了類似的功能，而無需管理 SAS 權杖或擔心撤銷已洩露的 SAS。 

預設情況下，所有事件中心資源都是安全的，並且僅對帳戶擁有者可用。 儘管您可以使用上述任何授權策略授予用戶端對事件中心資源的許可權。 只有使用 Azure 資源管理器部署模型創建的事件中心資源支援 Azure AD 授權。 Microsoft 建議盡可能使用 Azure AD，以實現最大的安全性和易用性。

有關使用 SAS 授權的詳細資訊，請參閱[使用共用訪問簽名授權訪問事件中心資源](authorize-access-shared-access-signature.md)。

## <a name="next-steps"></a>後續步驟
- 查看在我們的 GitHub 存儲庫中發佈的[RBAC 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
- 查看下列文章：
    - [使用 Azure 活動目錄對應用程式對 Azure 事件中心的請求進行身份驗證](authenticate-application.md)
    - [使用 Azure 活動目錄對託管標識進行身份驗證以訪問事件中心資源](authenticate-managed-identity.md)
    - [使用共用訪問簽名對 Azure 事件中心的請求進行身份驗證](authenticate-shared-access-signature.md)
    - [使用 Azure 活動目錄授權訪問事件中心資源](authorize-access-azure-active-directory.md)
    - [使用共用訪問簽名授權訪問事件中心資源](authorize-access-shared-access-signature.md)

