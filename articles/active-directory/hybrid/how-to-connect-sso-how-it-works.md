---
title: Azure AD Connect：無縫單一登入 - 運作方式 | Microsoft Docs
description: 本文描述 Azure Active Directory 無縫單一登入功能的運作方式。
services: active-directory
keywords: 何謂 Azure AD Connect、安裝 Active Directory、Azure AD、SSO、單一登入的必要元件
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4743bc38c3b2b4b9495b33535b4b73f48d1372
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "71176682"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory 無縫單一登入：技術性深入探討

本文提供 Azure Active Directory 無縫單一登入 (無縫 SSO) 功能運作方式的技術詳細資料。

## <a name="how-does-seamless-sso-work"></a>順暢 SSO 如何運作？

本節包含三個相關部分：

1. 無縫 SSO 功能的設定。
2. 網頁瀏覽器上的單一使用者登入交易如何與「無縫 SSO」搭配運作。
3. 原生用戶端上的單一使用者登入交易如何與「無縫 SSO」搭配運作。

### <a name="how-does-set-up-work"></a>設定如何運作？

無縫 SSO 是使用 Azure AD Connect 所啟用，如[這裏](how-to-connect-sso-quick-start.md)所示。 啟用此功能時，會執行下列步驟：

- 在您同步處理`AZUREADSSOACC`至 Azure AD 的每個 AD 樹系（使用 Azure AD Connect）中，會在您的內部部署 ACTIVE DIRECTORY （ad）中建立電腦帳戶（）。
- 此外，系統會建立一些 Kerberos 服務主體名稱（Spn），以在 Azure AD 登入程式期間使用。
- 電腦帳戶的 Kerberos 解密金鑰可安全地與 Azure AD 共用。 如果有多個 AD 樹系，每個電腦帳戶都會有自己唯一的 Kerberos 解密金鑰。

>[!IMPORTANT]
> 基於`AZUREADSSOACC`安全考慮，電腦帳戶必須受到嚴格的保護。 只有網域系統管理員才能夠管理電腦帳戶。 請確定已停用電腦帳戶上的 Kerberos 委派，而且 Active Directory 中的其他帳戶都具有電腦帳戶的`AZUREADSSOACC`委派許可權。 將電腦帳戶儲存在組織單位（OU）中，以防止意外刪除，而且只有網域系統管理員才有存取權。 電腦帳戶上的 Kerberos 解密金鑰也應該被視為機密。 強烈建議您至少每隔 30 天變換一次 `AZUREADSSOACC` 電腦帳戶的 [Kerberos 解密金鑰](how-to-connect-sso-faq.md)。

設定完成之後，無縫 SSO 登入的運作方式與其他任何使用整合式 Windows 驗證 (IWA) 的登入相同。

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>網頁瀏覽器上的登入如何與無縫 SSO 搭配運作？

網頁瀏覽器上的登入流程如下：

1. 使用者嘗試從公司網路內已加入網域的公司裝置存取 Web 應用程式 (例如 Outlook Web App，https://outlook.office365.com/owa/))。
2. 如果使用者尚未登入，則會將使用者重新導向至 Azure AD 登入頁面。
3. 使用者將他們的使用者名稱鍵入 Azure AD 登入頁面中。

   >[!NOTE]
   >針對[某些應用程式](./how-to-connect-sso-faq.md)，會略過步驟 2 和 3。

4. 在背景中使用 JavaScript 時，Azure AD 會透過 401 未授權回應來挑戰瀏覽器，以提供 Kerberos 票證。
5. 瀏覽器接著會從 Active Directory 要求 `AZUREADSSOACC` 電腦帳戶 (代表 Azure AD) 的票證。
6. Active Directory 會找出該電腦帳戶，並將 Kerberos 票證傳回給瀏覽器 (使用電腦帳戶的祕密加密)。
7. 瀏覽器會將它從 Active Directory 取得的 Kerberos 票證轉送給 Azure AD。
8. Azure AD 會解密 Kerberos 票證，其中包含使用先前共用的金鑰登入公司裝置之使用者的身分識別。
9. 評估之後，Azure AD 會將權杖傳回給應用程式，或要求使用者執行其他證明，例如 Multi-Factor Authentication。
10. 如果使用者登入成功，則使用者可以存取應用程式。

下圖說明所有元件和相關步驟。

![無縫單一登入 - Web 應用程式流程](./media/how-to-connect-sso-how-it-works/sso2.png)

無縫 SSO 是靈活變換的功能，這表示如果失敗，登入體驗會改回其一般行為； 也就是，使用者必須輸入密碼才能登入。

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>原生用戶端上的登入如何與無縫 SSO 搭配運作？

原生用戶端上的登入流程如下：

1. 使用者嘗試從公司網路內已加入網域的公司裝置存取原生應用程式 (例如 Outlook 用戶端)。
2. 如果使用者尚未登入，原生應用程式就會從裝置的 Windows 工作階段擷取使用者的使用者名稱。
3. 應用程式會將使用者名稱傳送給 Azure AD，然後擷取您租用戶的 WS-Trust MEX 端點。 「無縫 SSO」功能會以獨佔方式使用此 WS-Trust 端點，而且不是 WS-Trust 通訊協定在 Azure AD 上的一般實作。
4. 應用程式會接著查詢 WS-Trust MEX 端點，以了解是否有可用的整合式驗證端點。 「無縫 SSO」功能會以獨佔方式使用整合式驗證端點。
5. 如果步驟 4 成功，就會發出 Kerberos 查問。
6. 如果應用程式能夠擷取 Kerberos 票證，它就會將票證向上轉送至 Azure AD 的整合式驗證端點。
7. Azure AD 會將 Kerberos 票證解密並加以驗證。
8. Azure AD 會將使用者登入，然後向應用程式發出 SAML 權杖。
9. 應用程式會接著向 Azure AD 的 OAuth2 權杖端點提交該 SAML 權杖。
10. Azure AD 會驗證該 SAML 權杖，然後向應用程式發出所指定資源的存取權杖和重新整理權杖，以及一個識別碼權杖。
11. 使用者會取得應用程式資源的存取權。

下圖說明所有元件和相關步驟。

![無縫單一登入 - 原生應用程式流程](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>後續步驟

- [**快速入門**](how-to-connect-sso-quick-start.md) - 開始使用 Azure AD 無縫 SSO。
- [**常見問題**](how-to-connect-sso-faq.md)-常見問題的解答。
- [**疑難排解**](tshoot-connect-sso.md)-瞭解如何解決此功能的常見問題。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -用於提出新的功能要求。
