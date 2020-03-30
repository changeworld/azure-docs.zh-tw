---
title: Azure AD 應用的高級 SAML 權杖證書簽名選項
description: 瞭解如何在 Azure Active Directory 中，針對預先整合之應用程式使用 SAML 權杖中的進階憑證簽署選項
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc911ff06208b1fd0af7651c8274a45c958bf0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159194"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>在 Azure Active Directory 中，針對資源庫應用程式使用 SAML 權杖中的進階憑證簽署選項

現今 Azure Active Directory (Azure AD) 在 Azure Active Directory 應用程式庫中支援數千種預先整合的應用程式。 超過 500 個應用程式使用[安全斷言標記語言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)（SAML） 2.0 協定（如[NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite)應用程式）支援單一登入。 當客戶使用 SAML 通過 Azure AD 對應用程式進行身份驗證時，Azure AD 會向應用程式發送權杖（通過 HTTP POST）。 然後，應用程式驗證並使用權杖登錄客戶，而不是提示使用者名和密碼。 這些 SAML 權杖會使用 Azure AD 中產生的唯一憑證以及特定標準演算法產生的唯一憑證加以簽署。

Azure AD 為資源庫應用程式使用某些預設設定。 預設值是設定根據應用程式需求設定的。

在 Azure AD 中，可以設置證書簽名選項和證書簽名演算法。

## <a name="certificate-signing-options"></a>憑證簽署選項

Azure AD 支援三個憑證簽署選項：

* **簽署 SAML 判斷提示**。 此預設選項是針對大部分的資源庫應用程式而設定的。 如果選擇此選項，Azure AD 作為標識提供程式 （IdP） 使用應用程式的[X.509](https://wikipedia.org/wiki/X.509)證書對 SAML 斷言和證書進行簽名。

* **簽署 SAML 回應**。 如果選擇此選項，Azure AD 作為 IdP 使用應用程式的 X.509 憑證對 SAML 回應進行簽名。

* **簽署 SAML 回應及判斷提示**。 如果選擇此選項，Azure AD 作為 IdP 使用應用程式的 X.509 憑證對整個 SAML 權杖進行簽名。

## <a name="certificate-signing-algorithms"></a>憑證簽署演算法

Azure AD 支援兩個簽名演算法，或安全雜湊演算法 （SHA） 對 SAML 回應進行簽名：

* **SHA-256**. Azure AD 使用這個預設演算法簽署 SAML 回應。 它是最新的演算法，比SHA-1更安全。 大部分的應用程式都支援 SHA-256 演算法。 如果應用程式僅支援 SHA-1 作為簽署演算法，您可以加以變更。 否則，我們建議使用 SHA-256 演算法簽署 SAML 回應。

* **SHA-1**. 此演算法較舊，並且被視為不如 SHA-256 安全。 如果應用程式僅支援此簽署演算法，您可以在 [簽署演算法]**** 下拉式清單中選取此選項。 接著，Azure AD 會使用 SHA-1 演算法簽署 SAML 回應。

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>更改證書簽名選項和簽名演算法

要更改應用程式的 SAML 證書簽名選項和證書簽名演算法，請選擇有問題的應用程式：

1. 在[Azure 活動目錄門戶](https://aad.portal.azure.com/)中，登錄到您的帳戶。 將顯示**Azure 活動目錄管理中心**頁。
1. 在左側窗格中，選取 [企業應用程式]****。 將顯示帳戶中的企業應用程式清單。
1. 選取應用程式。 將顯示應用程式的概述頁。

   ![示例：應用程式概述頁](./media/certificate-signing-options/application-overview-page.png)

接下來，更改該應用程式的 SAML 權杖中的證書簽名選項：

1. 在應用程式概述頁的左側窗格中，選擇 **"單一登入**"。
1. 如果出現"**設置帶有 SAML 的單個登錄 - 預覽**"頁，請轉到步驟 5。
1. 如果未顯示 **"選擇單個登錄方法**頁"，請選擇 **"更改單一登入模式**"以顯示該頁。
1. 在 **"選擇單個登錄方法**"頁中，選擇**SAML（** 如果可用）。 （如果**SAML**不可用，則應用程式不支援 SAML，並且可能會忽略此過程和文章的其餘部分。
1. 在 **"使用 SAML 設置單個登錄 - 預覽**"頁中，找到**SAML 簽章憑證**標題並選擇 **"編輯"** 圖示（鉛筆）。 將顯示**SAML 簽章憑證**頁。

   ![示例：SAML 簽章憑證頁](./media/certificate-signing-options/saml-signing-page.png)

1. 在 [簽署選項]**** 下拉式清單中，選擇 [簽署 SAML回應]****、[簽署 SAML 判斷提示]**** 或 [簽署 SAML 回應和判斷提示]****。 這些選項的說明出現在本文前面的[證書簽名選項](#certificate-signing-options)中。
1. 在 [簽署演算法]**** 下拉式清單中，選擇 [SHA-1]**** 或 [SHA-256]****。 這些選項的說明在本文前面顯示在[證書簽名演算法](#certificate-signing-algorithms)部分。
1. 如果您對選擇感到滿意，請選擇"**保存"** 以應用新的 SAML 簽章憑證設置。 否則，選擇**X**以丟棄更改。

## <a name="next-steps"></a>後續步驟

* [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](configure-federated-single-sign-on-non-gallery-applications.md)
* [針對 SAML 型單一登入進行疑難排解](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
