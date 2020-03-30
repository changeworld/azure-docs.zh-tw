---
title: Azure Active Directory B2C 中的使用者流程 | Microsoft Docs
description: 深入了解 Azure Active Directory B2C 的可延伸原則架構，以及如何建立各種使用者流程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c11bc48742c398d2048a236c7d00af044971f845
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185602"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的使用者流程

Azure 活動目錄 B2C（Azure AD B2C） 的可擴展策略框架是服務的核心強度。 原則可完整描述身分識別體驗，例如註冊、登入或設定檔編輯。 為了協助您設定最常見的身分識別工作，Azure AD B2C 入口網站包含預先定義且可設定的原則，稱為**使用者流程**。

## <a name="what-are-user-flows"></a>使用者流程是什麼？

使用者流程可讓您藉由進行下列設定來控制您應用程式的行為：

- 用於登入的帳戶類型，例如社交帳戶，像是 Facebook 或本機帳戶
- 要向取用者收集的屬性，例如名字、郵遞區號和鞋子尺寸
- Azure Multi-Factor Authentication
- 使用者介面的自訂
- 應用程式所接收的資訊 (以權杖中的宣告表示)

您可以在租用戶中建立許多不同類型的使用者流程，並視需要在您的應用程式中使用。 使用者流程可以跨應用程式重複使用。 此彈性可讓您在稍微變更或完全不變更程式碼的情況下，定義及修改身分識別體驗。 您的應用程式會使用包含使用者流程參數的標準 HTTP 驗證要求來觸發使用者流程。 自訂的[權杖](tokens-overview.md)當以回應形式收到。

下列範例顯示 "p" 查詢字串參數，該參數可指定要使用的使用者流程：

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>使用者流程版本

在 Azure 入口網站中，一直會新增[新版的使用者流程](user-flow-versions.md)。 當您開始使用 Azure AD B2C 時，建議使用經過測試的使用者流程。 當您建立新的使用者流程時，可從 [建議]**** 索引標籤選擇您所需的使用者流程。

目前建議使用下列使用者流程：

- **註冊和登入** - 透過單一組態來處理註冊與登入體驗。 系統會視內容而定，將使用者引導到正確的路徑。 建議您使用這個使用者流程，而不要使用**註冊**使用者流程或**登入**使用者流程。
- **設定檔編輯** - 可讓使用者編輯其設定檔資訊。
- **密碼重設** - 可讓您設定使用者是否及如何重設其密碼。

## <a name="linking-user-flows"></a>連結使用者流程

使用本機帳戶的**註冊或登入**使用者流程在體驗的第一個頁面上包含[忘記密碼?]**** 連結。 按一下此連結並不會自動觸發密碼重設使用者流程。

相反地，系統會將錯誤碼 `AADB2C90118` 傳回您的應用程式。 您的應用程式必須藉由執行可重設密碼的特定使用者流程來處理此錯誤碼。 若要查看範例，請看看[簡單的 ASP.NET 範例](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)，該範例示範如何連結使用者流程。

## <a name="email-address-storage"></a>電子郵件地址儲存

電子郵件地址可以是使用者流程的必要部分。 如果使用者向社交識別提供者進行驗證，則電子郵件地址會儲存在 **otherMails** 屬性中。 如果本機帳戶是以使用者名稱為基礎，則電子郵件地址會儲存在強式驗證詳細資料屬性中。 如果本機帳戶是以電子郵件地址為基礎，則電子郵件地址會儲存在 **signInNames** 屬性中。

在上述任何情況下，不保證電子郵件地址都經過驗證。 租用戶系統管理員可以在本機帳戶的基本原則中停用電子郵件驗證。 即使已啟用電子郵件地址驗證，如果地址來自社交識別提供者且尚未變更，則不會加以驗證。

只有**其他郵件**和**簽名InNames**屬性通過 Microsoft 圖形 API 公開。 無法使用強式驗證詳細資料屬性中的電子郵件地址。

## <a name="next-steps"></a>後續步驟

要創建建議的使用者流，請按照教程中的說明[：創建使用者流](tutorial-create-user-flows.md)。
