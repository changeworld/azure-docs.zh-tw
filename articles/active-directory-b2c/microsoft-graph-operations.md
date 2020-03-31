---
title: 支援的微軟圖形操作
titleSuffix: Azure AD B2C
description: 支援用於管理 Azure AD B2C 資源的 Microsoft 圖形操作的索引，包括使用者、使用者流、標識提供程式、自訂策略、策略鍵等。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184243"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>適用于 Azure AD B2C 的微軟圖形操作

以下 Microsoft 圖形 API 操作支援用於管理 Azure AD B2C 資源，包括使用者、標識提供程式、使用者流、自訂策略和策略金鑰。

以下各節中的每個連結都針對該操作的 Microsoft 圖形 API 引用中的相應頁面。

## <a name="user-management"></a>使用者管理

- [列出使用者](https://docs.microsoft.com/graph/api/user-list)
- [創建消費者使用者](https://docs.microsoft.com/graph/api/user-post-users)
- [獲取使用者](https://docs.microsoft.com/graph/api/user-get)
- [更新使用者](https://docs.microsoft.com/graph/api/user-update)
- [刪除使用者](https://docs.microsoft.com/graph/api/user-delete)

有關使用 Microsoft 圖形 API 管理 Azure AD B2C 使用者帳戶的詳細資訊，請參閱[使用 Microsoft 圖形管理 Azure AD B2C 使用者帳戶](manage-user-accounts-graph-api.md)。

## <a name="identity-providers-user-flow"></a>標識提供程式（使用者流）

管理 Azure AD B2C 租戶中可供使用者流使用的身份提供程式。

- [列出在 Azure AD B2C 租戶中註冊的標識提供程式](https://docs.microsoft.com/graph/api/identityprovider-list)
- [創建標識提供程式](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [獲取標識提供程式](https://docs.microsoft.com/graph/api/identityprovider-get)
- [更新標識提供程式](https://docs.microsoft.com/graph/api/identityprovider-update)
- [刪除標識提供程式](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>使用者流程

為註冊、登錄、合併註冊和登錄、密碼重設和設定檔更新配置預構建的策略。

- [列出使用者流](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [創建使用者流](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [獲取使用者流](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [刪除使用者流](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>自訂原則

以下操作允許您管理 Azure AD B2C 信任框架策略（稱為[自訂策略](custom-policy-overview.md)）。

- [列出租戶中配置的所有信任框架策略](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [創建信任框架策略](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [讀取現有信任框架策略的屬性](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [更新或創建信任框架策略。](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [刪除現有信任框架策略](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>策略鍵

身份體驗框架存儲自訂策略中引用的機密，以在元件之間建立信任。 這些機密可以是對稱或非對稱鍵/值。 在 Azure 門戶中，這些實體顯示為**策略鍵**。

Microsoft 圖形 API 中策略鍵的頂級資源是[受信任的框架金鑰集](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset)。 每個**金鑰集**至少包含一個**金鑰**。 要創建鍵，請先創建一個空鍵集，然後在金鑰集中生成一個鍵。 您可以創建手動金鑰、上載證書或 PKCS12 金鑰。 金鑰可以是生成的機密、您定義的字串（如 Facebook 應用程式金鑰）或您上傳的證書。 如果金鑰集有多個鍵，則只有一個金鑰處於活動狀態。

### <a name="trust-framework-policy-keyset"></a>信任框架策略金鑰集

- [列出信任框架金鑰集](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [創建信任框架金鑰集](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [獲取金鑰集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [更新信任框架金鑰集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [刪除信任框架金鑰集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>信任框架策略鍵

- [在金鑰集中獲取當前活動金鑰](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [在金鑰集中生成金鑰](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [上傳基於字串的機密](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [上傳 X.509 憑證](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [上傳 PKCS12 格式證書](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>應用程式

- [列出應用程式](https://docs.microsoft.com/graph/api/application-list)
- [建立應用程式](https://docs.microsoft.com/graph/api/resources/application)
- [更新應用程式](https://docs.microsoft.com/graph/api/application-update)
- [創建服務主體](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [創建 auth2 許可權授予](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [刪除應用程式](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>應用程式擴充屬性

- [列出擴充屬性](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C 提供一個目錄，可為每個使用者保存 100 個自訂屬性。 對於使用者流，這些擴充屬性[使用 Azure 門戶進行管理](custom-policy-custom-attributes.md)。 對於自訂策略，Azure AD B2C 會在策略首次向擴充屬性寫入值時為您創建該屬性。

## <a name="audit-logs"></a>稽核記錄

- [列出稽核記錄](https://docs.microsoft.com/graph/api/directoryaudit-list)

有關使用 Microsoft 圖形 API 訪問 Azure AD B2C 稽核記錄的詳細資訊，請參閱[訪問 Azure AD B2C 稽核記錄](view-audit-logs.md)。
