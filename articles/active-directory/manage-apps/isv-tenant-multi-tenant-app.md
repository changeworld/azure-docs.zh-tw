---
title: 建立多租使用者應用程式的 Azure 租使用者
description: 獨立軟體廠商與 Azure Active Directory 整合的指引
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a0b63c130d7d1e72bd3320e40213ae3cb1069a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763239"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>建立多租使用者應用程式的 Azure 租使用者  

若要提供多租使用者應用程式的存取權，您必須建立 Azure Active Directory 租使用者來註冊應用程式，並啟用您客戶身分識別的同盟。 請參閱[為您的多租使用者應用程式選擇正確的同盟通訊協定](isv-choose-multi-tenant-federation.md)。 此租使用者可讓您在與客戶 Azure AD 環境類似的環境中測試您的應用程式和同盟。

## <a name="costs-of-hosting-a-multi-tenant-application"></a>裝載多租使用者應用程式的成本

Azure Active Directory 在多個版本中都有提供。 [請參閱詳細的功能比較](https://azure.microsoft.com/pricing/details/active-directory/)。

您可以免費建立 Azure 訂用帳戶和 Azure active directory，並使用基本功能。

## <a name="create-your-tenant"></a>建立您的租使用者

1. 建立您的租使用者。 請參閱[設定開發環境](../develop/quickstart-create-new-tenant.md)。

2. 啟用並測試對您應用程式的單一登入存取，

   a. **針對 OIDC 或 Oath 應用**程式，請將[您的應用程式註冊](../develop/quickstart-register-app.md)為多租使用者應用程式。 在 [支援的帳戶類型] 中，選取 [任何組織目錄中的帳戶] 和 [個人 Microsoft 帳戶] 選項

   b. **針對 saml 和以 WS 為基礎的應用程式**，您可以使用 Azure AD 中的一般 SAML 範本來[設定 saml 型單一登入](configure-single-sign-on-non-gallery-applications.md)應用程式。

您也可以視需要[將單一租使用者應用程式轉換成多租](../develop/howto-convert-app-to-be-multi-tenant.md)使用者。

## <a name="next-steps"></a>後續步驟

[在您的應用程式中整合 SSO](isv-sso-content.md)
