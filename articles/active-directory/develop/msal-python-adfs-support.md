---
title: Azure AD FS 支援（MSAL Python）
titleSuffix: Microsoft identity platform
description: 瞭解 Python 的 Microsoft 身份驗證庫中的活動目錄聯合服務 （AD FS） 支援
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699541"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>在 MSAL 中支援 Python 的活動目錄聯合服務

Windows 伺服器中的活動目錄聯合服務 （AD FS） 使您能夠通過使用 Python 的 Microsoft 身份驗證庫 （MSAL） 向應用添加 OpenID 連接和 OAuth 2.0 身份驗證和授權。 使用 Python 庫的 MSAL，你的應用可以直接根據 AD FS 對使用者進行身份驗證。 有關方案的詳細資訊，請參閱[開發人員的 AD FS 方案](/windows-server/identity/ad-fs/ad-fs-development)。

通常有兩種針對 AD FS 進行身份驗證的方法：

- MSAL Python 與 Azure 活動目錄對話，該目錄本身與其他標識提供程式聯合。 聯合通過 AD FS 進行。 MSAL Python 連接到 Azure AD，該 AD 在 Azure AD（託管使用者）中管理的使用者或由 AD FS（聯合使用者）管理的使用者中簽名。 MSAL Python 不知道使用者是聯合的。 它只是與 Azure AD 對話。 在這種情況下，您使用[的許可權](msal-client-application-configuration.md#authority)是通常的許可權（許可權主機名稱 + 租戶、公用或組織）。
- MSAL Python 直接與 AD FS 當局對話。 這僅受 AD FS 2019 及更高版本的支援。

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>連接到與 AD FS 聯合的活動目錄

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>以對話模式為聯合使用者獲取權杖

無論您是直接連接到活動目錄聯合服務 （AD FS） 還是通過活動目錄，都適用以下操作。

調用`acquire_token_by_authorization_code`或`acquire_token_by_device_flow`時，使用者體驗通常如下所示：

1. 使用者輸入其帳戶 ID。
2. Azure AD 會簡要顯示消息"將您帶到組織的頁面"，使用者將重定向到標識提供程式的登錄頁面。 登錄頁通常使用組織的徽標進行自訂。

在此聯合方案中支援 AD FS 版本包括：
- 活動目錄聯合服務 FS v2
- 活動目錄聯合服務 v3 （Windows 伺服器 2012 R2）
- 活動目錄聯合服務 v4 （AD FS 2016）

### <a name="acquire-a-token-via-username-and-password"></a>通過使用者名和密碼獲取權杖

無論您是直接連接到活動目錄聯合服務 （AD FS） 還是通過活動目錄，都適用以下操作。

使用 獲取權杖時`acquire_token_by_username_password`，MSAL Python 會根據使用者名獲取標識提供程式的連絡人。 MSAL Python 從標識提供程式獲取[SAML 1.1 權杖](reference-saml-tokens.md)，然後將其提供給返回 JSON Web 權杖 （JWT） 的 Azure AD。

## <a name="connecting-directly-to-ad-fs"></a>直接連接到 AD FS

將目錄連接到 AD FS 時，要用於構建應用程式的許可權將類似于`https://somesite.contoso.com/adfs/`

MSAL Python 支援 ADFS 2019。

它不支援直接連接到 ADFS 2016 或 ADFS v2。 如果您需要支援需要直接連接到 ADFS 2016 的方案，請使用最新版本的 ADAL Python。 將本地系統升級到 ADFS 2019 後，可以使用 MSAL Python。

## <a name="next-steps"></a>後續步驟

- 對於聯合情況，請參閱[使用主域發現策略為應用程式佈建 Azure 活動目錄登錄行為](../manage-apps/configure-authentication-for-federated-users-portal.md)