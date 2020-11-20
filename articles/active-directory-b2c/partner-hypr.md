---
title: 使用 HYPR 設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 針對 true 無密碼強式客戶驗證使用 Hypr 設定 Azure Active Directory B2C 的教學課程
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b80b1a4b3f9bcde6cf01b0e0e59425c6783bd5d9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953758"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 HYPR 的教學課程

在此範例教學課程中，我們會提供如何使用 [HYPR](https://get.hypr.com)設定 Azure AD B2C 的指引。 使用 Azure AD B2C 作為身分識別提供者時，您可以將 HYPR 與任何客戶應用程式整合，為您的使用者提供真正的無密碼驗證。 HYPR 會以公開金鑰加密取代密碼，以避免詐騙、網路釣魚和認證重複使用。

## <a name="prerequisites"></a>Prerequisites

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- [Azure AD B2C 的租](./tutorial-create-tenant.md)使用者。 租使用者已連結至您的 Azure 訂用帳戶。

- HYPR 雲端租使用者，取得免費 [試用帳戶](https://get.hypr.com/free-trial)。

- 使用 HYPR Rest Api 或 HYPR 租使用者中的 HYPR 裝置管理員註冊的使用者行動裝置。 例如，您可以使用 [HYPR JAVA SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) 來完成這項工作。

## <a name="scenario-description"></a>案例描述

HYRP 整合包含下列元件：

- Azure AD B2C –授權伺服器，負責驗證使用者的認證，也稱為身分識別提供者

- Web 和行動應用程式-您選擇使用 HYPR 和 Azure AD B2C 保護的行動或 web 應用程式。 HYPR 提供健全的行動 SDK，也是可讓您在 iOS 和 Android 平臺上用來執行真正無密碼 authentication 的行動應用程式。

- HYPR 行動應用程式-如果不想在您自己的行動應用程式中使用行動 Sdk，可使用 HYPR 行動裝置應用程式來執行此範例。

- HYPR Rest Api-您可以使用 HYPR Api 來進行使用者裝置註冊和驗證。 您可以在 [這裡](https://apidocs.hypr.com)找到這些 api。

下列架構圖顯示執行。

![Hypr 的螢幕擷取畫面-架構-圖表](media/partner-hypr/hypr-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 使用者抵達登入頁面。 使用者選取登入/註冊，然後在頁面中輸入使用者名稱。
| 2. | 應用程式會將使用者屬性傳送給 Azure AD B2C，以識別驗證。
| 3. | Azure AD B2C 會收集使用者屬性，並將屬性傳送至 HYPR，以透過 HYPR 行動裝置應用程式驗證使用者。
| 4. | HYPR 會傳送推播通知給已註冊的使用者行動裝置，以在線上 (FIDO) 認證的驗證進行快速身分識別。 它可以是使用者手指列印、生物特徵辨識或非集中式 pin。  
| 5. | 使用者確認推播通知之後，就會根據驗證結果，授與或拒絕使用者存取客戶應用程式。

## <a name="configure-the-azure-ad-b2c-policy"></a>設定 Azure AD B2C 原則

1. 移至原則資料夾中的 [AZURE AD B2C HYPR 原則](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy) 。

2. 遵循本 [檔](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) 以下載 [LocalAccounts 入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. 設定 Azure AD B2C 租使用者的原則。

>[!NOTE]
>更新提供的原則，以與您特定的租使用者相關聯。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 Azure AD B2C 租使用者，然後在 [原則] 底下選取 [ **Identity Experience Framework**]。

2. 選取您先前建立的 **SignUpSignIn**。

3. 選取 [ **執行使用者流程** ]，然後選取設定：

   a. **應用程式**：選取已註冊的應用程式 (範例為 JWT) 

   b. **回復 url**：選取重新 **導向 url**

   c. 選取 [執行使用者流程]。

4. 進行註冊流程並建立帳戶

5. 建立使用者屬性之後，會在流程期間呼叫 HYPR。 如果流程未完成，請檢查使用者是否未儲存在目錄中。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](./custom-policy-overview.md)

- [Azure AD B2C 中的自訂原則入門](./custom-policy-get-started.md?tabs=applications)