---
title: 為 Azure API for FHIR 註冊 Azure Active Directory 應用程式
description: 本教學課程說明必須為 Azure API for FHIR 和「適用於 Azure 的 FHIR Server」註冊的應用程式。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: aa95dc5cc052fbff6c553de50f4f52dc5df850a5
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398108"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>為 Azure API for FHIR 註冊 Azure Active Directory 應用程式

在設定 Azure API for FHIR 或適用於 Azure 的 FHIR Server (OSS) 時，會有數個設定選項可供選擇。 針對開放原始碼，您必須建立自己的資源應用程式註冊。 針對 Azure API for FHIR，則系統會自動建立此資源應用程式。

## <a name="application-registrations"></a>應用程式註冊

為了讓應用程式與 Azure AD 互動，其必須完成註冊。 在 FHIR 伺服器的內容中，要討論的應用程式註冊有兩種：

1. 資源應用程式註冊。
1. 用戶端應用程式註冊。

**資源應用程式** 是使用 Azure AD (具體來說，這會是 Azure API for FHIR) 所保護的 API 或資源在 Azure AD 中的表示。 當您佈建服務時，系統會自動為 Azure API for FHIR 建立資源應用程式，但如果您使用開放原始碼伺服器，則必須在 Azure AD 中[註冊資源應用程式](register-resource-azure-ad-client-app.md)。 此資源應用程式將會有識別碼 URI。 建議讓此 URI 與 FHIR 伺服器的 URI 相同。 此 URI 應該作為 FHIR 伺服器的 `Audience`。 用戶端應用程式可以在要求權杖時，要求此 FHIR 伺服器的存取權。

*用戶端應用程式* 是將會要求權杖的用戶端註冊。 在 OAuth 2.0 中，我們往往能區分出至少三種不同類型的應用程式：

1. **機密用戶端** ，也稱為 Azure AD 中的 Web 應用程式。 機密用戶端是使用[授權碼流程](../active-directory/azuread-dev/v1-protocols-oauth-code.md)的應用程式，會代表已登入並出示有效認證的使用者來取得權杖。 我們將其稱為機密用戶端是因為其能夠保存秘密，並且會在以驗證碼交換權杖時，向 Azure AD 出示此秘密。 因為機密用戶端能夠使用用戶端密碼來驗證自己，其所受到的信任大過公用用戶端，並且會有存留期較長的權杖，並獲授重新整理權杖。 請閱讀有關如何[註冊機密用戶端](register-confidential-azure-ad-client-app.md)的詳細資訊。 請注意，註冊用戶端將會用來接收授權碼的回復 url 非常重要。
1. **公用用戶端** 。 這些是無法保存祕密的用戶端。 一般來說，這會是行動裝置應用程式或單頁 JavaScript 應用程式，使用者可在其中找到用戶端內的秘密。 公用用戶端也會使用授權碼流程，但不得在取得權杖時出示秘密，其所擁有的權杖存留期可能較短，而且不會有重新整理權杖。 請閱讀有關如何[註冊公用用戶端](register-public-azure-ad-client-app.md)的詳細資訊。
1. 服務用戶端。 這些用戶端會使用[用戶端認證流程](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)，代表本身 (而非代表使用者) 來取得權杖。 其一般代表會以非互動方式存取 FHIR 伺服器的應用程式。 其中一個範例是擷取程序。 使用服務用戶端時，其不需要啟動使用 `/authorize` 端點的呼叫來取得權杖的程序。 服務用戶端可以直接移至 `/token` 端點，並出示用戶端識別碼和用戶端密碼來取得權杖。 請閱讀有關如何[註冊服務用戶端](register-service-azure-ad-client-app.md)的詳細資訊

## <a name="next-steps"></a>後續步驟

在此概觀中，您已了解為了使用 FHIR API 而可能需要的應用程式註冊類型。

根據您的設定，請參閱操作指南來註冊您的應用程式

* [註冊資源應用程式](register-resource-azure-ad-client-app.md)
* [註冊機密用戶端應用程式](register-confidential-azure-ad-client-app.md)
* [註冊公開用戶端應用程式](register-public-azure-ad-client-app.md)
* [註冊服務應用程式](register-service-azure-ad-client-app.md)

註冊應用程式之後，您就可以部署 Azure API for FHIR。

>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-powershell-quickstart.md)