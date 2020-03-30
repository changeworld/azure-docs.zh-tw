---
title: 如何委派使用者註冊和產品訂閱
description: 了解如何在 Azure API Management 中將使用者註冊和產品訂閱委派給第三方。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454350"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>如何委派使用者註冊和產品訂閱

委派允許您使用現有網站來處理開發人員登錄/註冊和訂閱產品，而不是在開發人員門戶中使用內置功能。 它使您的網站能夠擁有使用者資料，並自訂執行這些步驟的驗證。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>委派開發人員登入和註冊

要委派開發人員、登錄並註冊現有網站，您需要在網站上創建一個特殊的委派終結點。 它需要充當從 API 管理開發人員門戶啟動的任何此類請求的進入點。

最終工作流程如下所示：

1. 開發人員按一下 API 管理開發人員門戶的登錄或註冊連結
2. 瀏覽器重新導向至委派端點
3. 委派終結點，返回重定向到或顯示 UI，要求使用者登錄或註冊
4. 成功時，將使用者重新導向回到他們所來自的 API 管理開發人員入口網站頁面

首先，請設定 API 管理透過委派端點來傳遞要求。 在 Azure 門戶中，在 API 管理資源中搜索**安全性**，然後按一下**委派**項。 按一下該核取方塊可啟用"代理登錄&註冊"。

![Delegation page][api-management-delegation-signin-up]

* 決定特殊委派端點的 URL，並在 [ **Delegation endpoint URL** ] 欄位中輸入。 
* 在 [委派驗證金鑰] 欄位中輸入密碼，用來計算提供給您驗證的簽章，以確定要求確實來自 Azure API 管理。 您可以按一下 [產生]**** 按鈕，讓 API 管理為您隨機產生金鑰。

現在您需要建立「 **委派端點**」。 必須執行一些動作：

1. 接收下列形式的要求：
   
   > *HTTP：\//www.yourwebsite.com/apimdelegation?operation=SignIn&返回Url_源頁 URL[&salt_字串[&sig_string]*
   > 
   > 
   
    登錄/註冊案例的查詢參數：
   
   * **operation**：識別委派要求的類型 - 在此案例中只能是 **SignIn**
   * **returnUrl**：使用者按一下登錄或註冊連結的頁面的 URL
   * **salt**：特殊 salt 字串，用於計算安全性雜湊
   * **sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較
2. 確認要求來自 Azure API 管理 (選擇性，但基於安全性理由，強烈建議這麼做)
   
   * 根據 **returnUrl** 和 **salt** 查詢參數，計算字串的 HMAC-SHA512 雜湊 ([以下提供範例程式碼])：
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * 比較以上計算的雜湊和 **sig** 查詢參數的值。 如果兩個雜湊相符，則繼續下一步，否則拒絕要求。
3. 驗證您是否收到登錄/註冊請求：**操作**查詢參數將設置為"**登錄**"。
4. 向使用者出示要登錄或註冊的 UI
5. 如果使用者要註冊，您必須在 API 管理中為他們建立對應的帳戶。 使用 API Management REST API [建立使用者]。 執行此操作時，請確保將使用者 ID 設置為與使用者存儲中的值或可以跟蹤的 ID 相同的值。
6. 成功驗證使用者之後：
   
   * [要求單一登入 (SSO) 權杖]
   * 將 returnUrl 查詢參數附加至您從上述 API 呼叫收到的 SSO URL：
     
     > 例如，https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * 將使用者重新導向至以上產生的 URL

除了 **SignIn** 作業之外，您也可以遵循上述步驟來執行帳戶管理，並使用以下其中一項作業：

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

您必須傳遞下列查詢參數，供帳戶管理作業使用。

* **operation**：識別委派要求的類型 (ChangePassword、ChangeProfile 或 CloseAccount)
* **使用者 ID**：要管理的帳戶的使用者 ID
* **salt**：特殊 salt 字串，用於計算安全性雜湊
* **sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>委派產品訂閱
委派產品訂閱的工作方式類似于委派使用者登錄/上線。 最終工作流程如下所示：

1. 開發人員在 API 管理開發人員門戶中選擇產品，然後按一下"訂閱"按鈕。
2. 瀏覽器被重定向到委派終結點。
3. 委派終結點執行所需的產品訂閱步驟。 由您設計這些步驟。 它們可能包括重定向到其他頁面以請求計費資訊、提出其他問題或僅存儲資訊，而無需任何使用者操作。

若要啟用此功能，請在 [委派]**** 頁面按一下 [委派產品訂用帳戶]****。

接下來，確保委派終結點執行以下操作：

1. 接收下列形式的要求：
   
   > *HTTP：\//www.yourweb.com/apim 委派？操作{操作{操作{&產品 Id_產品訂閱[&使用者 Id_使用者 Id_&salt_&s_ssig_string}*
   >
   
    產品訂閱案例的查詢參數：
   
   * **operation**：識別委派要求的類型。 對於產品訂閱要求，有效的選項包括：
     * "Subscribe"：為使用者訂閱具有提供之識別碼的 (請參閱下面) 指定產品的要求
     * "Unsubscribe"：將為使用者取消訂閱產品的要求
     * "Renew"：訂閱續訂要求 (例如，可能過期)
   * **productId**：使用者要求訂閱之產品的識別碼
   * **訂閱 Id**：取消*訂閱*和*續訂*- 產品訂閱的 ID
   * **使用者 ID**：為請求發出請求的使用者的 ID
   * **salt**：特殊 salt 字串，用於計算安全性雜湊
   * **sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較

2. 確認要求來自 Azure API 管理 (選擇性，但基於安全性理由，強烈建議這麼做)
   
   * 根據產品**Id、userId**和**salt**查詢參數計算**productId**字串的 HMAC-SHA512：
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * 比較以上計算的雜湊和 **sig** 查詢參數的值。 如果兩個雜湊相符，則繼續下一步，否則拒絕要求。
3. 根據**操作**中請求的操作類型（例如，計費、其他問題等）處理產品訂閱。
4. 在成功將使用者訂閱到您身邊的產品時，請通過[調用 REST API 進行訂閱]，將使用者訂閱到 API 管理產品。

## <a name="example-code"></a><a name="delegate-example-code"> </a>示例代碼

這些代碼示例演示如何：

* 獲取在發行者門戶的委派螢幕中設置的*委派驗證金鑰*
* 創建一個 HMAC，然後用於驗證簽名，以證明傳遞的 returnUrl 的有效性。

相同的程式碼稍微修改一下後，也適用於 productId 和 userId。

**產生 returnUrl 雜湊的 C# 程式碼**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**產生 returnUrl 雜湊的 NodeJS 程式碼**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> 您需要[重新發佈開發人員門戶](api-management-howto-developer-portal-customize.md#publish)，以便委派更改生效。

## <a name="next-steps"></a>後續步驟
如需委派的詳細資訊，請觀看以下影片：

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[要求單一登入 (SSO) 權杖]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[建立使用者]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[調用訂閱的 REST API]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[以下提供範例程式碼]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
