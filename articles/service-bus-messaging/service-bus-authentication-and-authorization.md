---
title: Azure 服務匯流排驗證和授權 | Microsoft Docs
description: 使用共用存取簽章 (SAS) 驗證向服務匯流排驗證應用程式。
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70013235"
---
# <a name="service-bus-authentication-and-authorization"></a>服務匯流排驗證和授權

應用程式會使用共用存取簽章 (SAS) 權杖驗證來取得 Azure 服務匯流排資源的存取權。 應用程式會使用 SAS 向服務匯流排呈現權杖，該權杖已使用權杖簽發者和服務匯流排 (因此稱為「共用」) 已知的對稱金鑰進行簽署，而該金鑰會直接與授與特定存取權限的規則相關聯，例如接收/接聽或傳送訊息等權限。 SAS 規則可在命名空間加以設定，或直接在諸如佇列或或主題等實體上加以設定，以允許進行更精細的存取控制。

SAS 權杖可由服務匯流排用戶端直接產生，或可由與用戶端有互動的一些中繼權杖發行端點產生。 例如，系統可能會要求用戶端呼叫 Active Directory 授權所保護的 Web 服務端點，以證明其身分識別和系統存取權限，然後 Web 服務就會傳回適當的服務匯流排權杖。 使用 Azure SDK 隨附的服務匯流排權杖提供者，就可以輕鬆地產生此 SAS 權杖。 

> [!IMPORTANT]
> 如果您是使用 Azure Active Directory 存取控制 (也稱為存取控制服務或 ACS) 搭配服務匯流排，請注意，這個方法的支援目前有所限制，且您應該移轉應用程式以使用 SAS。 如需詳細資訊，請參閱[此部落格文章](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) \(英文\) 和[此文章](service-bus-migrate-acs-sas.md)。

## <a name="azure-active-directory"></a>Azure Active Directory
服務匯流排資源的 Azure 活動目錄 （Azure AD） 集成提供基於角色的存取控制 （RBAC），用於細細微性地控制用戶端對資源的訪問。 您可以使用基於角色的存取控制 （RBAC） 向安全主體授予許可權，安全主體可以是使用者、組或應用程式服務主體。 安全主體由 Azure AD 進行身份驗證，以返回 OAuth 2.0 權杖。 該權杖可用於授權訪問服務匯流排資源（佇列、主題等）的請求。

有關使用 Azure AD 進行身份驗證的詳細資訊，請參閱以下文章：

- [使用受控識別進行驗證](service-bus-managed-service-identity.md)
- [從應用程式進行驗證](authenticate-application.md)

> [!IMPORTANT]
> 使用 Azure AD 返回的 OAuth 2.0 權杖授權使用者或應用程式比共用訪問簽名 （SAS） 更安全且便於使用。 使用 Azure AD，無需將權杖存儲在代碼中，並存在潛在的安全性漏洞風險。 我們建議盡可能將 Azure AD 與 Azure 服務匯流排應用程式一起使用。 


## <a name="shared-access-signature"></a>共用存取簽章
[SAS 驗證](service-bus-sas.md)可讓您授與使用者具特定權限的服務匯流排資源存取權。 服務匯流排中的 SAS 驗證牽涉到在服務匯流排資源上設定具有相關權限的密碼編譯金鑰。 接著用戶端可以藉由提出 SAS 權杖 (其中包含正在存取的資源 URI 及利用設定金鑰簽署的到期日) 存取該資源。

您可以在服務匯流排命名空間上設定 SAS 的金鑰。 金鑰會套用至該命名空間中的所有訊息實體。 您也可以在服務匯流排佇列和主題上設定金鑰。 [Azure 轉送](../service-bus-relay/relay-authentication-and-authorization.md)上也支援 SAS。

若要使用 SAS，您可以在命名空間、佇列或主題上設定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 物件。 此規則由下列元素組成：

* *KeyName*：能識別規則。
* *PrimaryKey*：用來簽署/驗證 SAS 權杖的密碼編譯金鑰。
* *SecondaryKey*：用來簽署/驗證 SAS 權杖的密碼編譯金鑰。
* *Rights*：代表所授與之**接聽**、**傳送**或**管理**權限的集合。

在命名空間層級設定的授權規則可以授與命名空間中所有實體的存取權給具備使用對應金鑰簽署之權杖的用戶端。 您可以在服務匯流排命名空間、佇列或主題上設定最多 12 條這類的授權規則。 根據預設，具備所有權限的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 會在第一次佈建時為每個命名空間設定。

若要存取實體，用戶端需要使用特定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)產生的 SAS 權杖。 SAS 權杖乃是使用資源字串的 HMAC-SHA256 所產生，該字串由宣告存取的資源 URI 以及具備與授權規則相關聯之密碼編譯金鑰的過期所組成。

服務匯流排的 SAS 驗證支援包含在 Azure .NET SDK 2.0 版或更新版本中。 SAS 包括 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)的支援。 所有接受連接字串做為參數的 API 包括 SAS 連接字串的支援。

## <a name="next-steps"></a>後續步驟

- 如需 SAS 的詳細資料，請繼續閱讀[使用共用存取簽章的服務匯流排驗證](service-bus-sas.md)。
- 如何[從 Azure Active Directory 存取控制服務 (ACS) 移轉至共用存取簽章授權](service-bus-migrate-acs-sas.md)。
- [變更為已啟用 ACS 的命名空間](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) \(英文\)。
- 如需 Azure 轉送驗證和授權的相關對應資訊，請參閱 [Azure 轉送驗證和授權](../service-bus-relay/relay-authentication-and-authorization.md)。 

