---
title: AD FS 支援（JAVA 的 MSAL）
titleSuffix: Microsoft identity platform
description: 瞭解 JAVA （MSAL4j） 的 Microsoft 身份驗證庫中的活動目錄聯合服務 （AD FS） 支援。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696209"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>在 MSAL 中支援 JAVA 的活動目錄聯合服務

Windows 伺服器中的活動目錄聯合服務 （AD FS） 使您能夠向適用于 JAVA （JAVA） 應用的 Microsoft 身份驗證庫 （MSAL） 應用添加 OpenID 連接和 OAuth 2.0 身份驗證和授權。 集成後，你的應用可以通過 Azure AD 聯合對 AD FS 中的使用者進行身份驗證。 有關方案的詳細資訊，請參閱[開發人員的 AD FS 方案](/windows-server/identity/ad-fs/ad-fs-development)。

使用 MSAL 進行 JAVA 的應用將與 Azure 活動目錄 （Azure AD） 進行對話，該目錄隨後與 AD FS 聯合。

JAVA 的 MSAL 連接到 Azure AD，Azure AD 在 Azure AD（託管使用者）中管理的使用者或由 AD FS（聯合使用者）管理的使用者進行簽名。 JAVA 的 MSAL 不知道使用者是聯合的。 它只是與 Azure AD 對話。

在這種情況下，您使用[的許可權](msal-client-application-configuration.md#authority)是通常的許可權（許可權主機名稱 + 租戶、公用或組織）。

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>以對話模式為聯合使用者獲取權杖

當您調用`ConfidentialClientApplication.AcquireToken()`或`PublicClientApplication.AcquireToken()``AuthorizationCodeParameters`使用`DeviceCodeParameters`或 時，使用者體驗通常是：

1. 使用者輸入其帳戶 ID。
2. Azure AD 簡要顯示"將您帶到組織的頁面"，並將使用者重定向到標識提供程式的登錄頁。 登錄頁通常使用組織的徽標進行自訂。

在此聯合方案中支援 AD FS 版本包括：
- 活動目錄聯合服務 FS v2
- 活動目錄聯合服務 v3 （Windows 伺服器 2012 R2）
- 活動目錄聯合服務 v4 （AD FS 2016）

## <a name="acquire-a-token-via-username-and-password"></a>通過使用者名和密碼獲取權杖

當您使用`ConfidentialClientApplication.AcquireToken()`或`PublicClientApplication.AcquireToken()`獲取`IntegratedWindowsAuthenticationParameters``UsernamePasswordParameters`JAVA 的 MSAL 時，會根據使用者名獲取標識提供程式的連絡人。 JAVA 的 MSAL 從標識提供程式獲取[SAML 1.1 權杖](reference-saml-tokens.md)，然後將其提供給返回 JSON Web 權杖 （JWT） 的 Azure AD。

## <a name="next-steps"></a>後續步驟

對於聯合情況，請參閱[使用主域發現策略為應用程式佈建 Azure 活動目錄登錄行為](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)