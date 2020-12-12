---
title: 適用於外部身分識別的識別提供者 - Azure AD
description: 瞭解如何使用 Azure AD 作為預設身分識別提供者，以便與外部使用者共用。
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
ms.openlocfilehash: 8ead05598c6ca4d096e1a68c8d640938ecd771c2
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355506"
---
# <a name="identity-providers-for-external-identities"></a>適用於外部身分識別的識別提供者

*識別提供者* 可建立、維護及管理身分識別資訊，同時對應用程式提供驗證服務。 與外部使用者共用您的應用程式和資源時，Azure AD 是共用的預設身分識別提供者。 這表示當您邀請已經有 Azure AD 或 Microsoft 帳戶的外部使用者時，他們可以自動登入，無需您進一步設定。

不過，您可以讓使用者使用各種身分識別提供者登入。

- **Google**：Google 同盟允許外部使用者使用其 Gmail 帳戶登入您的應用程式，以兌換來自您的邀請。 Google 同盟也可以用於您的自助式註冊使用者流程。
   > [!IMPORTANT]
   > 自 **2021 年1月4日起**，Google 將 [淘汰 web 服務登入支援](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)。 如果您要使用 Google 同盟或使用 Gmail 的自助式註冊，您應該 [測試企業營運原生應用程式的相容性](google-federation.md#deprecation-of-webview-sign-in-support)。

   > [!NOTE]
   > 在目前的自助式註冊預覽中，如果有使用者流程與應用程式相關聯，且您將該應用程式的邀請傳送給使用者，該使用者將無法使用 Gmail 帳戶來兌換邀請。 因應措施是讓該使用者完成自助式註冊程序。 或者，他們可以透過存取不同的應用程式，或是使用其「我的應用程式」入口網站 (https://myapps.microsoft.com ) 來兌換邀請。

- **Facebook**：建置應用程式時，您可以設定自助式註冊並啟用 Facebook 同盟，讓使用者可以使用其 Facebook 帳戶註冊您的應用程式。 Facebook 只能用於自助式註冊使用者流程，且在使用者兌換來自您的邀請時，並不能作為登入選項使用。

- **直接同盟**：您也可以與任何支援 SAML 或 WS-Fed 通訊協定的外部識別提供者設定直接同盟。 直接同盟允許外部使用者使用其現有的社交或企業帳戶登入您的應用程式，以兌換來自您的邀請。 
   > [!NOTE]
   > 直接同盟識別提供者不能用於您的自助式註冊使用者流程。


## <a name="how-it-works"></a>運作方式

Azure AD [外部身分識別] 自助註冊功能可讓使用者使用其 Azure AD、Google 或 Facebook 帳戶進行註冊。 若要在 Azure AD 租用戶中設定社交識別提供者，請在每個識別提供者建立應用程式並設定認證。 您將取得用戶端或應用程式識別碼，以及用戶端或應用程式祕密，並將其新增至您的 Azure AD 租用戶。

將身分識別提供者新增至您的 Azure AD 租用戶後：

- 邀請外部使用者到組織中的應用程式或資源時，外部使用者可以使用具有該身分識別提供者的自身帳戶登入。
- 為應用程式啟用[自助式註冊](self-service-sign-up-overview.md)時，外部使用者可以使用自己的帳戶與您新增的身分識別提供者，註冊您的應用程式。

> [!NOTE]
> 自助式註冊預設會啟用 Azure AD，因此使用者一律可以使用 Azure AD 帳戶來註冊。

兌換邀請或註冊應用程式時，外部使用者可以選擇使用社交識別提供者登入和驗證：

![顯示具有 Google 和 Facebook 選項之登入畫面的螢幕擷取畫面](media/identity-providers/sign-in-with-social-identity.png)

若要獲得最佳的登入體驗，請盡可能與身分識別提供者建立同盟，讓受邀的來賓能夠在存取您的應用程式時享有順暢的登入體驗。  

## <a name="next-steps"></a>後續步驟

若要了解如何新增身分識別提供者來登入您的應用程式，請參閱下列文章：

- 將 [Google](google-federation.md) 新增至您的社交識別提供者清單
- 將 [Facebook](facebook-federation.md) 新增至您的社交識別提供者清單
- 針對其識別提供者可支援 SAML 2.0 或 WS-Fed 通訊協定的任何組織來[設定直接同盟](direct-federation.md)。 請注意，直接同盟並不是自助註冊使用者流程的選項。
