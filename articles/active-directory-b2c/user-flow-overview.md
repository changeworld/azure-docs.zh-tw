---
title: Azure Active Directory B2C 中的使用者流程 | Microsoft Docs
titleSuffix: Azure AD B2C
description: 深入了解 Azure Active Directory B2C 的可延伸原則架構，以及如何建立各種使用者流程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7a7736602fafb740d1d76fa09fd26da25e4ff9f5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87481592"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的使用者流程

為了協助您為應用程式設定最常見的身分識別工作，Azure AD B2C 入口網站包含預先定義且可設定的原則，稱為**使用者流程**。 使用者流程可讓您判斷使用者在執行像是登入、註冊、編輯設定檔或重設密碼等動作時，如何與您的應用程式互動。 透過使用者流程，您可以控制下列功能：

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

Azure AD B2C 包括數種類型的使用者流程：

- **註冊和登入** - 透過單一組態來處理註冊與登入體驗。 系統會視內容而定，將使用者引導到正確的路徑。 也包含個別的**註冊**或登**入**使用者流程。 但我們通常會建議合併的註冊和登入使用者流程。
- **設定檔編輯** - 可讓使用者編輯其設定檔資訊。
- **密碼重設** - 可讓您設定使用者是否及如何重設其密碼。

大部分的使用者流程類型都有**建議**的版本和**標準**版本。 如需詳細資訊，請參閱[使用者流程版本](user-flow-versions.md)。

> [!IMPORTANT]
> 如果您之前在 Azure AD B2C 中使用過使用者流程，您會發現我們已變更我們參考使用者流程版本的方式。 先前，我們提供了 V1 （已準備好用於生產環境）的版本，以及 v1.1 和 V2 （預覽）版本。 現在，我們已將使用者流程合併為兩個版本：
>
>- **建議**的使用者流程是使用者流程的新預覽版本。 它們經過徹底測試，並結合了舊版**V2**和**v1.1**版本的所有功能。 未來，將會維護並更新新的建議使用者流程。 當您移至這些新的建議使用者流程後，就可以在新功能發行時存取它們。
>- **標準**使用者流程，先前稱為**V1**，已正式運作，可供生產環境使用的使用者流程。 如果您的使用者流程是任務關鍵性的，而且取決於高穩定的版本，您可以繼續使用標準使用者流程，並意識到這些版本將不會進行維護和更新。
>
>所有舊版預覽使用者流程（v1.1 和 V2）都是在**2021 年8月1日**淘汰的路徑上。 可能的話，我們強烈建議您儘快[切換至新的**建議**使用者流程](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow)，讓您隨時都能利用最新的功能和更新。

## <a name="linking-user-flows"></a>連結使用者流程

使用本機帳戶的**註冊或登入**使用者流程在體驗的第一個頁面上包含[忘記密碼?]**** 連結。 按一下此連結並不會自動觸發密碼重設使用者流程。

相反地，系統會將錯誤碼 `AADB2C90118` 傳回您的應用程式。 您的應用程式必須藉由執行可重設密碼的特定使用者流程來處理此錯誤碼。 若要查看範例，請看看[簡單的 ASP.NET 範例](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)，該範例示範如何連結使用者流程。

## <a name="email-address-storage"></a>電子郵件地址儲存

電子郵件地址可以是使用者流程的必要部分。 如果使用者向社交識別提供者進行驗證，則電子郵件地址會儲存在 **otherMails** 屬性中。 如果本機帳戶是以使用者名稱為基礎，則電子郵件地址會儲存在強式驗證詳細資料屬性中。 如果本機帳戶是以電子郵件地址為基礎，則電子郵件地址會儲存在 **signInNames** 屬性中。

在上述任何情況下，不保證電子郵件地址都經過驗證。 租用戶系統管理員可以在本機帳戶的基本原則中停用電子郵件驗證。 即使已啟用電子郵件地址驗證，如果地址來自社交識別提供者且尚未變更，則不會加以驗證。

只有**otherMails**和**signInNames**屬性會透過 Microsoft Graph API 公開。 無法使用強式驗證詳細資料屬性中的電子郵件地址。

## <a name="next-steps"></a>後續步驟

若要建立建議的使用者流程，請依照[教學課程：建立使用者流程](tutorial-create-user-flows.md)中的指示進行。
