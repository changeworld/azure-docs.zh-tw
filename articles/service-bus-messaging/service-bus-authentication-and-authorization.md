---
title: Azure 服務匯流排驗證和授權 | Microsoft Docs
description: 使用共用存取簽章 (SAS) 驗證向服務匯流排驗證應用程式。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7b287b209fbcd5bc2782505095aeae4390107803
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060209"
---
# <a name="service-bus-authentication-and-authorization"></a>服務匯流排驗證和授權
有兩種方式可以驗證和授權 Azure 服務匯流排資源的存取權： Azure 活動目錄 (Azure AD) 和共用存取簽章 (SAS) 。 本文將詳細說明如何使用這兩種類型的安全性機制。 

## <a name="azure-active-directory"></a>Azure Active Directory
服務匯流排資源的 Azure AD 整合可提供 Azure 角色型存取控制 (Azure RBAC) ，以精細控制用戶端對資源的存取。 您可以使用 Azure RBAC 將許可權授與安全性主體，該安全性主體可能是使用者、群組或應用程式服務主體。 安全性主體會由 Azure AD 進行驗證，以傳回 OAuth 2.0 權杖。 權杖可以用來授權要求存取服務匯流排資源 (佇列、主題等 ) 。

如需有關使用 Azure AD 進行驗證的詳細資訊，請參閱下列文章：

- [使用受控識別進行驗證](service-bus-managed-service-identity.md)
- [從應用程式進行驗證](authenticate-application.md)

> [!NOTE]
> [服務匯流排 REST API](/rest/api/servicebus/) 支援 Azure AD 的 OAuth 驗證。

> [!IMPORTANT]
> 使用 Azure AD 所傳回 OAuth 2.0 權杖的授權使用者或應用程式，可提供更高的安全性，並透過 (SAS) 的共用存取簽章更容易使用。 使用 Azure AD 時，不需要在程式碼中儲存權杖，也不需要風險潛在的安全性弱點。 建議您盡可能使用 Azure AD 搭配 Azure 服務匯流排應用程式。 

## <a name="shared-access-signature"></a>共用存取簽章
[SAS 驗證](service-bus-sas.md)可讓您授與使用者具特定權限的服務匯流排資源存取權。 服務匯流排中的 SAS 驗證牽涉到在服務匯流排資源上設定具有相關權限的密碼編譯金鑰。 接著用戶端可以藉由提出 SAS 權杖 (其中包含正在存取的資源 URI 及利用設定金鑰簽署的到期日) 存取該資源。

您可以在服務匯流排命名空間上設定 SAS 的金鑰。 金鑰會套用至該命名空間中的所有訊息實體。 您也可以在服務匯流排佇列和主題上設定金鑰。 [Azure 轉送](../azure-relay/relay-authentication-and-authorization.md)上也支援 SAS。

若要使用 SAS，您可以在命名空間、佇列或主題上設定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 物件。 此規則由下列元素組成：

* *KeyName*：能識別規則。
* *PrimaryKey*：用來簽署/驗證 SAS 權杖的密碼編譯金鑰。
* *SecondaryKey*：用來簽署/驗證 SAS 權杖的密碼編譯金鑰。
* *Rights*：代表所授與之 **接聽**、**傳送** 或 **管理** 權限的集合。

在命名空間層級設定的授權規則可以授與命名空間中所有實體的存取權給具備使用對應金鑰簽署之權杖的用戶端。 您可以在服務匯流排命名空間、佇列或主題上設定最多 12 條這類的授權規則。 根據預設，具備所有權限的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 會在第一次佈建時為每個命名空間設定。

若要存取實體，用戶端需要使用特定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)產生的 SAS 權杖。 SAS 權杖乃是使用資源字串的 HMAC-SHA256 所產生，該字串由宣告存取的資源 URI 以及具備與授權規則相關聯之密碼編譯金鑰的過期所組成。

服務匯流排的 SAS 驗證支援包含在 Azure .NET SDK 2.0 版或更新版本中。 SAS 包括 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)的支援。 所有接受連接字串做為參數的 API 包括 SAS 連接字串的支援。

> [!IMPORTANT]
> 如果您使用 Azure Active Directory 存取控制 (也稱為「存取控制服務」或「ACS) 」服務匯流排，請注意，此方法的支援現在有所限制，您應該將 [應用程式遷移至使用 SAS](service-bus-migrate-acs-sas.md) 或使用 OAuth 2.0 驗證搭配 Azure AD (建議的) 。如需有關淘汰 ACS 的詳細資訊，請參閱 [這篇 blog 文章](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces)。

## <a name="next-steps"></a>下一步
如需有關使用 Azure AD 進行驗證的詳細資訊，請參閱下列文章：

- [使用受控識別執行驗證作業](service-bus-managed-service-identity.md)
- [從應用程式進行驗證](authenticate-application.md)

如需有關使用 SAS 進行驗證的詳細資訊，請參閱下列文章：

- [使用 SAS 進行驗證](service-bus-sas.md)
