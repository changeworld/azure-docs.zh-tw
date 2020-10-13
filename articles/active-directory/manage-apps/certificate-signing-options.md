---
title: Azure AD 應用程式的 Advanced SAML 權杖憑證簽署選項
description: 瞭解如何在 Azure Active Directory 中，針對預先整合之應用程式使用 SAML 權杖中的進階憑證簽署選項
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2625698ae1d76dbae3ed8a8855b88dd6ac7bb17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763681"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>在 Azure Active Directory 中，針對資源庫應用程式使用 SAML 權杖中的進階憑證簽署選項

現今 Azure Active Directory (Azure AD) 在 Azure Active Directory 應用程式庫中支援數千種預先整合的應用程式。 500以上的應用程式支援使用 [安全性聲明標記語言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0 通訊協定（例如 [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) 應用程式）來單一登入。 當客戶使用 SAML 向 Azure AD 驗證應用程式時，Azure AD 會透過 HTTP POST) ，將權杖傳送到應用程式 (。 然後，應用程式會驗證並使用權杖來登入客戶，而不是提示輸入使用者名稱和密碼。 這些 SAML 權杖會使用 Azure AD 中產生的唯一憑證以及特定標準演算法產生的唯一憑證加以簽署。

Azure AD 為資源庫應用程式使用某些預設設定。 預設值是設定根據應用程式需求設定的。

在 Azure AD 中，您可以設定憑證簽署選項和憑證簽署演算法。

## <a name="certificate-signing-options"></a>憑證簽署選項

Azure AD 支援三個憑證簽署選項：

* **簽署 SAML 判斷提示**。 此預設選項是針對大部分的資源庫應用程式而設定的。 如果您選取此選項，Azure AD 作為身分識別提供者 (IdP) 會使用應用程式的 [x.509](https://wikipedia.org/wiki/X.509) 憑證來簽署 SAML 判斷提示和憑證。

* **簽署 SAML 回應**。 如果您選取此選項，Azure AD IdP 會使用應用程式的 x.509 憑證來簽署 SAML 回應。

* **簽署 SAML 回應及判斷提示**。 如果您選取此選項，Azure AD IdP 會使用應用程式的 x.509 憑證來簽署整個 SAML 權杖。

## <a name="certificate-signing-algorithms"></a>憑證簽署演算法

Azure AD 支援兩種簽署演算法，或 (Sha) 的安全雜湊演算法來簽署 SAML 回應：

* **SHA-256**。 Azure AD 使用這個預設演算法簽署 SAML 回應。 這是最新的演算法，而且比 SHA-1 更安全。 大部分的應用程式都支援 SHA-256 演算法。 如果應用程式僅支援 SHA-1 作為簽署演算法，您可以加以變更。 否則，我們建議使用 SHA-256 演算法簽署 SAML 回應。

* **Sha-1**。 這是較舊的演算法，而且被視為比 SHA-256 更不安全。 如果應用程式僅支援此簽署演算法，您可以在 [簽署演算法]**** 下拉式清單中選取此選項。 接著，Azure AD 會使用 SHA-1 演算法簽署 SAML 回應。

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>變更憑證簽署選項和簽署演算法

若要變更應用程式的 SAML 憑證簽署選項和憑證簽署演算法，請選取有問題的應用程式：

1. 在 [Azure Active Directory 入口網站](https://aad.portal.azure.com/)中，登入您的帳戶。 **Azure Active Directory 系統管理中心**] 頁面隨即出現。
1. 在左側窗格中，選取 [企業應用程式]****。 您帳戶中的企業應用程式清單隨即出現。
1. 選取應用程式。 應用程式的 [總覽] 頁面隨即出現。

   ![範例：應用程式總覽頁面](./media/certificate-signing-options/application-overview-page.png)

接下來，變更該應用程式的 SAML 權杖中的憑證簽署選項：

1. 在 [應用程式總覽] 頁面的左窗格中，選取 [ **單一登入**]。
1. 如果出現 [ **使用 SAML 預覽設定單一 Sign-On** ] 頁面，請移至步驟5。
1. 如果未出現 [ **選取單一登入方法** ] 頁面，請選取 [ **變更單一登入模式** ] 以顯示該頁面。
1. 在 [ **選取單一登入方法** ] 頁面中，選取 [ **SAML** （如果有的話）]。  (如果 **saml** 無法使用，則應用程式不支援 saml，您可能會忽略此程式和文章的其餘部分。 ) 
1. 在 [ **以 SAML 預覽設定單一 Sign-On** ] 頁面中，尋找 [ **saml 簽署憑證** ] 標題，然後選取 [ **編輯** ] 圖示 (鉛筆) 。 [ **SAML 簽署憑證** ] 頁面隨即出現。

   ![範例： SAML 簽署憑證頁面](./media/certificate-signing-options/saml-signing-page.png)

1. 在 [簽署選項] 下拉式清單中，選擇 [簽署 SAML回應]、[簽署 SAML 判斷提示] 或 [簽署 SAML 回應和判斷提示]。 這些選項的描述會在本文稍早的 [憑證簽署選項](#certificate-signing-options)中顯示。
1. 在 [簽署演算法] 下拉式清單中，選擇 [SHA-1] 或 [SHA-256]。 這些選項的描述會在本文稍早的 [憑證簽署演算法](#certificate-signing-algorithms) 一節中顯示。
1. 如果您對選擇感到滿意，請選取 [ **儲存** ] 以套用新的 SAML 簽署憑證設定。 否則，請選取 **X** 以捨棄變更。

## <a name="next-steps"></a>接下來的步驟

* [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](configure-federated-single-sign-on-non-gallery-applications.md)
* [針對 SAML 型單一登入進行疑難排解](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
