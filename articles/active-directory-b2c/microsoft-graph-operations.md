---
title: 支援的 Microsoft Graph 作業
titleSuffix: Azure AD B2C
description: 支援 Azure AD B2C 資源管理的 Microsoft Graph 作業索引，包括使用者、使用者流程、身分識別提供者、自訂原則、原則金鑰等等。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 27fe1a41365d96a4179f8c659b63dc22c7b9fc93
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78184243"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>適用于 Azure AD B2C 的 Microsoft Graph 作業

Azure AD B2C 資源的管理支援下列 Microsoft Graph API 作業，包括使用者、身分識別提供者、使用者流程、自訂原則和原則金鑰。

下列各節中的每個連結會將目標設為該作業之 Microsoft Graph API 參考中的對應頁面。

## <a name="user-management"></a>使用者管理

- [列出使用者](https://docs.microsoft.com/graph/api/user-list)
- [建立取用者使用者](https://docs.microsoft.com/graph/api/user-post-users)
- [取得使用者](https://docs.microsoft.com/graph/api/user-get)
- [更新使用者](https://docs.microsoft.com/graph/api/user-update)
- [刪除使用者](https://docs.microsoft.com/graph/api/user-delete)

如需使用 Microsoft Graph API 來管理 Azure AD B2C 使用者帳戶的詳細資訊，請參閱[使用 Microsoft Graph 管理 Azure AD B2C 的使用者帳戶](manage-user-accounts-graph-api.md)。

## <a name="identity-providers-user-flow"></a>識別提供者（使用者流程）

管理您的 Azure AD B2C 租使用者中可供您的使用者流程使用的識別提供者。

- [列出在 Azure AD B2C 租使用者中註冊的識別提供者](https://docs.microsoft.com/graph/api/identityprovider-list)
- [建立身分識別提供者](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [取得身分識別提供者](https://docs.microsoft.com/graph/api/identityprovider-get)
- [更新識別提供者](https://docs.microsoft.com/graph/api/identityprovider-update)
- [刪除識別提供者](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>使用者流程

為註冊、登入、合併的註冊和登入、密碼重設和設定檔更新設定預先建立的原則。

- [列出使用者流程](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [建立使用者流程](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [取得使用者流程](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [刪除使用者流程](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>自訂原則

下列作業可讓您管理 Azure AD B2C 的信任架構原則，稱為[自訂原則](custom-policy-overview.md)。

- [列出租使用者中設定的所有信任架構原則](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [建立信任架構原則](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [讀取現有信任架構原則的屬性](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [更新或建立信任架構原則。](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [刪除現有的信任架構原則](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>原則金鑰

Identity Experience Framework 會儲存自訂原則中所參考的秘密，以建立元件之間的信任。 這些秘密可以是對稱或非對稱金鑰/值。 在 Azure 入口網站中，這些實體會顯示為**原則索引鍵**。

Microsoft Graph API 中原則金鑰的最上層資源是[受信任的架構](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset)索引鍵集。 每個索引**鍵集**至少包含一個索引**鍵**。 若要建立金鑰，請先建立空的索引鍵集，然後在索引鍵集中產生索引鍵。 您可以建立手動密碼、上傳憑證或 PKCS12 金鑰。 金鑰可以是產生的秘密、您定義的字串（例如 Facebook 應用程式密碼）或您上傳的憑證。 如果索引鍵集有多個索引鍵，則只有一個索引鍵為使用中。

### <a name="trust-framework-policy-keyset"></a>信任架構原則索引鍵集

- [列出信任架構索引鍵集](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [建立信任架構索引鍵集](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [取得金鑰集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [更新信任架構索引鍵集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [刪除信任架構索引鍵集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>信任架構原則金鑰

- [取得索引鍵集中的目前作用中金鑰](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [在金鑰集內產生金鑰](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [上傳以字串為基礎的秘密](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [上傳 x.509 憑證](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [上傳 PKCS12 格式憑證](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>應用程式

- [列出應用程式](https://docs.microsoft.com/graph/api/application-list)
- [建立應用程式](https://docs.microsoft.com/graph/api/resources/application)
- [更新應用程式](https://docs.microsoft.com/graph/api/application-update) \(英文\)
- [建立 servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [建立 oauth2Permission 授與](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [刪除應用程式](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>應用程式延伸模組屬性

- [列出擴充功能屬性](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C 提供一個目錄，可為每個使用者保存 100 個自訂屬性。 針對使用者流程，會[使用 Azure 入口網站來管理](custom-policy-custom-attributes.md)這些延伸模組屬性。 若為自訂原則，Azure AD B2C 在第一次將值寫入延伸模組屬性時，為您建立屬性。

## <a name="audit-logs"></a>稽核記錄

- [列出 audit 記錄](https://docs.microsoft.com/graph/api/directoryaudit-list)

如需使用 Microsoft Graph API 來存取 Azure AD B2C audit 記錄的詳細資訊，請參閱[存取 Azure AD B2C 的 audit 記錄](view-audit-logs.md)檔。
