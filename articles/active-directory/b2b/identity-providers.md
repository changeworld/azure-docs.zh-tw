---
title: 適用於外部身分識別的識別提供者 - Azure AD
description: Azure Active Directory B2B 共同作業支援多重要素驗證 (MFA) 以對您的公司應用程式進行選擇性存取
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595707"
---
# <a name="identity-providers-for-external-identities"></a>適用於外部身分識別的識別提供者

*識別提供者*可建立、維護及管理身分識別資訊，同時對應用程式提供驗證服務。 與外部使用者共用您的應用程式和資源時，Azure AD 是共用的預設身分識別提供者。 這表示當您邀請已經有 Azure AD 或 Microsoft 帳戶的外部使用者時，他們可以自動登入，無需您進一步設定。

不過，您可以讓使用者使用各種身分識別提供者登入。 例如，您可以使用 Azure AD 所支援的社交身分識別提供者 (包括 Google 和 Facebook) 來設定同盟。 您也可以與任何支援 SAML 或 WS-Fed 通訊協定的外部身分識別提供者建立同盟。 透過外部識別提供者同盟，您可讓外部使用者能夠使用其現有的社交或企業帳戶登入您的應用程式。

## <a name="how-it-works"></a>運作方式

Azure AD 外部身分識別已預先設定，可與 Google 和 Facebook 同盟。 若要在 Azure AD 租用戶中設定這些識別提供者，請在每個識別提供者建立應用程式並設定認證。 您將取得用戶端或應用程式識別碼，以及用戶端或應用程式祕密，並將其新增至您的 Azure AD 租用戶。

將身分識別提供者新增至您的 Azure AD 租用戶後：

- 邀請外部使用者到組織中的應用程式或資源時，外部使用者可以使用具有該身分識別提供者的自身帳戶登入。
- 為應用程式啟用[自助式註冊](self-service-sign-up-overview.md)時，外部使用者可以使用自己的帳戶與您新增的身分識別提供者，註冊您的應用程式。 

兌換邀請或註冊應用程式時，外部使用者可以選擇使用社交識別提供者登入和驗證：

![顯示具有 Google 和 Facebook 選項之登入畫面的螢幕擷取畫面](media/identity-providers/sign-in-with-social-identity.png)

若要獲得最佳的登入體驗，請盡可能與身分識別提供者建立同盟，讓受邀的來賓能夠在存取您的應用程式時享有順暢的登入體驗。  

## <a name="next-steps"></a>後續步驟

若要了解如何新增身分識別提供者來登入您的應用程式，請參閱下列文章：

- 將 [Google](google-federation.md) 新增至您的社交識別提供者清單
- 將 [Facebook](facebook-federation.md) 新增至您的社交識別提供者清單
- 針對其識別提供者可支援 SAML 2.0 或 WS-Fed 通訊協定的任何組織來[設定直接同盟](direct-federation.md)。 請注意，直接同盟並不是自助註冊使用者流程的選項。
