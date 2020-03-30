---
title: 驗證和授權
description: 瞭解應用或服務對 Azure 空間錨點進行身份驗證的各種方式，以及必須對 Azure 空間錨點進行門控級別的控制。
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656962"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>對 Azure 空間錨點的身份驗證和授權

在本節中，我們將介紹從應用或 Web 服務向 Azure 空間錨點進行身份驗證的各種方法，以及使用 Azure 目錄 （Azure AD） 中的基於角色的存取控制來控制對空間錨點帳戶的訪問的方式。

## <a name="overview"></a>總覽

![對 Azure 空間錨點的身份驗證概述](./media/spatial-anchors-authentication-overview.png)

要訪問給定的 Azure 空間錨點帳戶，用戶端需要首先從 Azure 混合現實安全權杖服務 （STS） 獲取訪問權杖。 從 STS 獲得的權杖可生存 24 小時，並包含 Spatial 錨點服務的資訊，以便對帳戶做出授權決策，並確保只有經過授權的委託人才能訪問該帳戶。

訪問權杖可以從任一帳戶金鑰或從 Azure AD 頒發的權杖獲得交換。

帳戶鍵使您能夠快速開始使用 Azure 空間錨點服務;因此，您可以快速開始使用 Azure 空間錨點服務。但是，在將應用程式部署到生產中之前，建議您更新應用以使用基於 Azure AD 的身份驗證。

Azure AD 身份驗證權杖可通過兩種方式獲得：

- 如果要構建企業應用程式，並且公司使用 Azure AD 作為其標識系統，則可以在應用中使用基於使用者的 Azure AD 身份驗證，並使用現有的 Azure AD 安全性群組授予對空間錨點帳戶的存取權限，或者直接給組織中的使用者。
- 否則，建議從支援應用的 Web 服務獲取 Azure AD 權杖。 對於生產應用程式，使用支援 Web 服務是推薦的身份驗證方法，因為它可以避免在用戶端應用程式中嵌入用於訪問 Azure 空間錨點的憑據。

## <a name="account-keys"></a>帳戶金鑰

使用帳戶金鑰訪問 Azure 空間錨點帳戶是入門的最簡單方法。 您可以在 Azure 門戶中找到帳戶金鑰。 導航到您的帳戶，然後選擇"金鑰"選項卡。

![對 Azure 空間錨點的身份驗證概述](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


兩個金鑰可用，這兩個鍵同時對訪問空間錨點帳戶有效。 建議您定期更新用於存取帳戶的金鑰;擁有兩個單獨的有效金鑰可啟用此類更新，而不會停機;您只需要更新主鍵和輔助鍵。

SDK 具有內置支援使用帳戶金鑰進行身份驗證;您只需在雲會話物件上設置 AccountKey 屬性即可。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[JAVA](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++溫RT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

完成後，SDK 將處理訪問權杖的帳戶金鑰的交換，以及應用所需的權杖緩存。

> [!WARNING]
> 建議使用帳戶金鑰快速載入，但僅在開發/原型設計期間使用。 強烈建議不要使用其中的嵌入式帳戶金鑰將應用程式運送到生產部門，而是使用下面列出的基於使用者或基於服務的 Azure AD 身份驗證方法。

## <a name="azure-ad-user-authentication"></a>Azure AD 使用者身份驗證

對於面向 Azure 活動目錄使用者的應用程式，建議的方法是為使用者使用 Azure AD 權杖，您可以使用[MSAL 庫](../../active-directory/develop/msal-overview.md)獲取該權杖。 您應該按照列出的步驟列出[註冊應用快速入門](../../active-directory/develop/quickstart-register-app.md)，其中包括：

1. Azure 門戶中的配置
    1.  在 Azure AD 中註冊應用程式作為**本機應用程式**。 作為註冊的一部分，您需要確定應用程式是否應是多租戶，並提供應用程式允許的重定向 URL。
        1.  切換到**API 許可權**選項卡
        2.  選擇 **"添加許可權**"
            1.  選擇 **"我的組織使用"選項卡**下的 API 下**的混合現實資來源提供者**
            2.  選擇**委派許可權**
            3.  選中混合**現實.在****混合現實**下登錄的框
            4.  選擇 **"添加許可權**"
        3.  選擇 **"授予管理員同意"**
    2.  授予應用程式或使用者對資源的許可權：
        1.  導航到 Azure 門戶中的空間錨點資源
        2.  切換到**存取控制 （IAM）** 選項卡
        3.  點擊**添加角色指派**
            1.  [選取角色](#role-based-access-control)
            2.  在 **"選擇"** 欄位中，輸入要向其分配存取權限的使用者、組和/或應用程式的名稱。
            3.  按 [儲存]****。
2. 在代碼中：
    1.  請確保使用自己的 Azure AD**應用程式的應用程式 ID**和**重定向 Uri**作為 ADAL 中的**用戶端 ID**和**重定向 Uri**參數
    2.  設置租戶資訊：
        1.  如果應用程式僅支援**我的組織**，請將此值替換為**租戶 ID**或**租戶名稱**（例如，contoso.microsoft.com）
        2.  如果應用程式支援**任何組織目錄中的帳戶**，請將此值替換為**組織**
        3.  如果應用程式支援**所有 Microsoft 帳戶使用者**，請將此值替換為 **"通用"**
    3.  在權杖請求時，將**資源**設置為""。https://sts.mixedreality.azure.com 此"資源"將指示 Azure AD 應用程式正在請求 Azure 空間錨點服務的權杖。

這樣，您的應用程式應該能夠從 MSAL 獲取 Azure AD 權杖;因此，應用程式應該能夠從 MSAL 獲取 Azure AD 權杖。您可以將該 Azure AD 權杖設置為雲會話設定物件的**身份驗證權杖**。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[JAVA](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++溫RT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure 廣告服務身份驗證

將利用 Azure 空間錨點部署應用以進行生產的建議選項是利用將代理身份驗證請求的後端服務。 一般方案應如下圖所述：

![對 Azure 空間錨點的身份驗證概述](./media/spatial-anchors-aad-authentication.png)

在這裡，假定你的應用使用自己的機制（例如：微軟帳戶，PlayFab，Facebook，谷歌ID，自訂使用者名/密碼等）來驗證其後端服務。 向後端服務驗證使用者後，該服務可以檢索 Azure AD 權杖，將其交換為 Azure 空間錨點的訪問權杖，並將其返回到用戶端應用程式。

使用[MSAL 庫](../../active-directory/develop/msal-overview.md)檢索 Azure AD 訪問權杖。 您應該按照列出的步驟列出[註冊應用快速入門](../../active-directory/develop/quickstart-register-app.md)，其中包括：

1.  Azure 門戶中的配置：
    1.  在 Azure AD 中註冊應用程式：
        1.  在 Azure 門戶中，導航到**Azure 活動目錄**，並選擇**應用註冊**
        2.  **選擇新的申請註冊**
        3.  輸入應用程式的名稱，選擇 Web**應用/API**作為應用程式類型，然後輸入服務 auth URL。 然後點擊 **"創建**"。
        4.  在該應用程式上，點擊 **"設置**"，然後選擇 **"鍵**"選項卡。輸入金鑰的名稱，選擇持續時間，然後點擊 **"保存**"。 請確保保存當時顯示的鍵值，因為您需要將其包含在 Web 服務的代碼中。
    2.  授予應用程式和/或使用者對資源的許可權：
        1.  導航到 Azure 門戶中的空間錨點資源
        2.  切換到**存取控制 （IAM）** 選項卡
        3.  點擊**添加角色指派**
        1.  [選取角色](#role-based-access-control)
        2.  在**選擇**欄位中，輸入您創建的應用程式的名稱，以及要為其分配存取權限的名稱。 如果希望應用的使用者對空間錨點帳戶具有不同的角色，則應在 Azure AD 中註冊多個應用程式，並為每個單獨的角色指派。 然後實現授權邏輯，以便為使用者使用正確的角色。
    3.  按 [儲存]****。
2.  在代碼中（注意：您可以使用 GitHub 中包含的服務示例）：
    1.  請確保使用自己的 Azure AD 應用程式的應用程式 ID、應用程式機密和重定向 Uri 作為 ADAL 中的用戶端 ID、機密和重定向 Uri 參數
    2.  在 ADAL 中的許可權參數中將租戶 ID 設置為您自己的 AAAzure ADD 租戶 ID
    3.  在權杖請求中，將**資源**設置為""https://sts.mixedreality.azure.com

這樣，後端服務就可以檢索 Azure AD 權杖。 然後，它可以將其交換為 MR 權杖，以便返回用戶端。 使用 Azure AD 權杖檢索 MR 權杖是通過 REST 調用完成的。 下面是一個示例調用：

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

其中授權標頭的格式如下：`Bearer <accoundId>:<accountKey>`

回應以純文字形式包含 MR 權杖。

然後，該 MR 權杖將返回到用戶端。 然後，用戶端應用可以在雲會話配置中將其設置為其訪問權杖。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[JAVA](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++溫RT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>角色型存取控制

為了説明控制授予服務的應用程式、服務或 Azure AD 使用者的存取層級，已創建以下角色，以便您根據需要根據 Azure 空間錨點帳戶進行分配：

- **空間錨點帳戶擁有者**：具有此角色的應用程式或使用者能夠創建空間錨點、查詢它們並刪除它們。 當您使用帳戶金鑰向帳戶進行身份驗證時，**空間錨點帳戶擁有者**角色將分配給經過身份驗證的委託人。
- **空間錨點帳戶參與者**：具有此角色的應用程式或使用者能夠創建空間錨點，查詢它們，但不能刪除它們。
- **空間錨點帳戶讀取器**：具有此角色的應用程式或使用者只能查詢空間錨點，但不能創建新錨點、刪除現有錨點或更新空間錨點上的中繼資料。 這通常用於某些使用者策劃環境的應用程式，而其他使用者只能調用以前放置在該環境中的錨點。

## <a name="next-steps"></a>後續步驟

使用 Azure 空間錨點創建第一個應用。

> [!div class="nextstepaction"]
> [統一（霍洛倫斯）](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [統一 （iOS）](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [統一（安卓）](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [霍洛倫斯](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [夏馬林（安卓）](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [夏馬林 （iOS）](../quickstarts/get-started-xamarin-ios.md)
