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
ms.openlocfilehash: f44be4e1d3d1186f0122bd4669ae800ab42e31d6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521300"
---
# <a name="authorize-access-to-azure-event-hubs"></a>授與 Azure 事件中樞的存取權
每次發佈或使用事件中心的事件/數據時,用戶端都嘗試訪問事件中心資源。 對安全資源的每個請求都必須獲得授權,以便服務能夠確保用戶端具有發佈/使用數據所需的許可權。 

Azure 事件中心提供以下選項,用於授權存取安全資源:

- Azure Active Directory
- 共用存取簽章

> [!NOTE]
> 本文適用於事件中心和[Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)方案。 

## <a name="azure-active-directory"></a>Azure Active Directory
事件中心資源的 Azure 活動目錄 (Azure AD) 整合提供基於角色的存取控制 (RBAC),用於細粒度地控制客戶端對資源的存取。 您可以使用基於角色的存取控制 (RBAC) 向安全主體授予許可權,安全主體可以是使用者、組或應用程式服務主體。 安全主體由 Azure AD 進行身份驗證,以返回 OAuth 2.0 令牌。 該令牌可用於授權訪問事件中心資源的請求。

有關使用 Azure AD 進行身份驗證的詳細資訊,請參閱以下文章:

- [使用 Azure 活動目錄對 Azure 事件中心的要求進行身份驗證](authenticate-application.md)
- [使用 Azure 的目錄 授權存取事件中心資源](authorize-access-azure-active-directory.md)。

## <a name="shared-access-signatures"></a>共用存取簽章 
事件中心資源的共享訪問簽名 (SAS) 提供對事件中心資源的有限委派訪問。 在簽名有效的時間間隔或授予的許可權上添加約束,可以靈活地管理資源。 有關詳細資訊,請參閱[使用共享存取簽章 (SAS) 進行身份認證](authenticate-shared-access-signature.md)。 

使用 Azure AD 傳回的 OAuth 2.0 權杖授權使用者或應用程式比共享存取簽名 (SAS) 更安全且易於使用。 使用 Azure AD,無需將訪問權杖與代碼一起存儲,並存在潛在的安全漏洞風險。 雖然您可以繼續使用共享訪問簽名 (SAS) 來授予對事件中心資源的細粒度訪問,但 Azure AD 提供了類似的功能,而無需管理 SAS 令牌或擔心撤銷已洩露的 SAS。 

默認情況下,所有事件中心資源都是安全的,並且僅對帳戶擁有者可用。 儘管您可以使用上述任何授權策略授予用戶端對事件中心資源的許可權。 Microsoft 建議盡可能使用 Azure AD,以實現最大的安全性和易用性。

關於 SAS 授權的詳細資訊,請參考[分享存取簽署授權的事件中心資源](authorize-access-shared-access-signature.md)。

## <a name="next-steps"></a>後續步驟
- 檢視在我們的 GitHub 儲存函式庫中的[RBAC 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
- 查看下列文章：
    - [使用 Azure 活動目錄對應用程式對 Azure 事件中心的要求進行身份驗證](authenticate-application.md)
    - [使用 Azure 活動目錄對託管識別進行身分驗證以存取事件中心資源](authenticate-managed-identity.md)
    - [使用分享存取簽署對 Azure 事件中心的要求進行身份驗證](authenticate-shared-access-signature.md)
    - [使用 Azure 活動目錄授權存取事件中心資源](authorize-access-azure-active-directory.md)
    - [使用分享存取簽署授權存取事件中心資源](authorize-access-shared-access-signature.md)

