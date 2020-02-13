---
title: 驗證和授權
description: 瞭解應用程式或服務可向 Azure 空間錨點驗證的各種方式，以及您必須控制 Azure 空間錨點存取權的層級。
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3de84e2d814acfca67bc722243a90fa41f6536e1
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161676"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure 空間錨點的驗證和授權

在本節中，我們將討論您可以從您的應用程式或 web 服務向 Azure 空間錨點進行驗證的各種方式，以及您可以在 Azure 目錄（Azure AD）中使用角色型存取控制以控制對空間錨點帳戶之存取的方式。

## <a name="overview"></a>概觀

![Azure 空間錨點的驗證總覽](./media/spatial-anchors-authentication-overview.png)

若要存取指定的 Azure 空間錨點帳戶，用戶端必須先從 Azure Mixed Reality 安全性權杖服務（STS）取得存取權杖。 從 STS live 取得24小時的權杖，並包含空間錨點服務的資訊，以對帳戶進行授權決策，並確保只有授權的主體可以存取該帳戶。

您可以從帳戶金鑰或從 Azure AD 發行的權杖，在 exchange 中取得存取權杖。

帳戶金鑰可讓您快速開始使用 Azure 空間錨點服務;不過，在您將應用程式部署至生產環境之前，建議您先更新應用程式，以使用以 Azure AD 為基礎的驗證。

您可以透過兩種方式取得 Azure AD 驗證權杖：

- 如果您要建立企業應用程式，而且您的公司使用 Azure AD 作為其身分識別系統，您可以在應用程式中使用以使用者為基礎的 Azure AD 驗證，並使用現有的 Azure AD 安全性群組來授與空間錨點帳戶的存取權，或直接向組織中的使用者。
- 否則，建議您從支援應用程式的 web 服務取得 Azure AD 權杖。 針對生產應用程式，使用支援的 web 服務是建議的驗證方法，因為它可避免內嵌認證來存取用戶端應用程式中的 Azure 空間錨點。

## <a name="account-keys"></a>帳戶金鑰

使用帳戶金鑰存取您的 Azure 空間錨點帳戶，是最簡單的入門方式。 您會在 Azure 入口網站上找到您的帳戶金鑰。 流覽至您的帳戶，然後選取 [金鑰] 索引標籤。

![Azure 空間錨點的驗證總覽](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


有兩個金鑰可供使用，這兩者同時也適用于存取空間錨點帳戶。 建議您定期更新用來存取帳戶的金鑰;擁有兩個不同的有效金鑰會啟用這類更新，而不會造成停機;您只需要更新主要金鑰和次要金鑰。

SDK 具有使用帳戶金鑰進行驗證的內建支援;您只需要在 cloudSession 物件上設定 AccountKey 屬性。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

完成後，SDK 會處理存取權杖的帳戶金鑰交換，以及為您的應用程式所需的權杖快取。

> [!WARNING]
> 建議使用帳戶金鑰來快速入門，但僅限開發/原型設計期間。 強烈建議不要將您的應用程式運送到生產環境中使用內嵌的帳戶金鑰，而是改為使用接下來所列的以使用者為基礎或以服務為基礎的 Azure AD 驗證方法。

## <a name="azure-ad-user-authentication"></a>Azure AD 使用者驗證

針對以 Azure Active Directory 使用者為目標的應用程式，建議的方法是使用使用者的 Azure AD token，您可以使用 MSAL 連結[庫](../../active-directory/develop/msal-overview.md)取得該權杖。 您應遵循[註冊應用程式快速入門](../../active-directory/develop/quickstart-register-app.md)中列出的步驟，其中包括：

1. Azure 入口網站中的設定
    1.  在 Azure AD 中，將您的應用程式註冊為**原生應用**程式。 在註冊過程中，您必須判斷您的應用程式是否應為多租使用者，並提供應用程式允許的重新導向 Url。
        1.  切換至 [ **API 許可權**] 索引標籤
        2.  選取 [**新增許可權**]
            1.  在 [**我的組織使用**] 索引標籤下選取 [**混合現實資源提供者**]
            2.  選取**委派的許可權**
            3.  核取 [ **mixedreality** ] 下的 [ **mixedreality** ] 核取方塊
            4.  選取 [**新增許可權**]
        3.  選取 **[授與系統管理員同意**]
    2.  授與您的應用程式或使用者對您資源的存取權：
        1.  流覽至您在 Azure 入口網站中的空間錨點資源
        2.  切換至 [**存取控制（IAM）** ] 索引標籤
        3.  點擊**新增角色指派**
            1.  [選取角色](#role-based-access-control)
            2.  在 [**選取**] 欄位中，輸入您要指派存取權的使用者、群組和/或應用程式的名稱。
            3.  按 [儲存]。
2. 在您的程式碼中：
    1.  請務必使用您自己的 Azure AD 應用程式的**應用程式識別碼**和重新**導向 Uri** ，做為 ADAL 中的**用戶端識別碼**和**RedirectUri**參數
    2.  設定租使用者資訊：
        1.  如果您的應用程式**只支援我的組織**，請將此值取代為您的租使用者**識別碼**或租使用者**名稱**（例如，contoso.microsoft.com）
        2.  如果您的應用程式支援**任何組織目錄中的帳戶**，請將此值取代為**組織**
        3.  如果您的應用程式支援**所有 Microsoft 帳戶使用者**，請將此值取代為**Common**
    3.  在您的權杖要求上，將**資源**設定為「 https://sts.mixedreality.azure.com」。 此「資源」會指出 Azure AD 您的應用程式要求 Azure 空間錨點服務的權杖。

如此一來，您的應用程式應該能夠從 MSAL Azure AD token 取得;您可以將該 Azure AD token 設定為雲端會話設定物件上的**authenticationToken** 。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD 服務驗證

將使用 Azure 空間錨點的應用程式部署至生產環境的建議選項，是利用將會代理驗證要求的後端服務。 一般配置應如下圖所述：

![Azure 空間錨點的驗證總覽](./media/spatial-anchors-aad-authentication.png)

在此，我們假設您的應用程式使用自己的機制（例如： Microsoft 帳戶、PlayFab、Facebook、Google ID、自訂使用者名稱/密碼等）來驗證其後端服務。 當您的使用者通過後端服務的驗證之後，該服務就可以抓取 Azure AD 權杖，將其與 Azure 空間錨點的存取權杖交換，然後傳回給用戶端應用程式。

Azure AD 存取權杖會使用[MSAL 程式庫](../../active-directory/develop/msal-overview.md)來抓取。 您應遵循[註冊應用程式快速入門](../../active-directory/develop/quickstart-register-app.md)中列出的步驟，其中包括：

1.  Azure 入口網站中的設定：
    1.  在 Azure AD 中註冊您的應用程式：
        1.  在 Azure 入口網站中，流覽至 [ **Azure Active Directory**]，然後選取 [**應用程式註冊**]
        2.  選取 [**新增應用程式註冊**]
        3.  輸入應用程式的名稱，選取 [ **Web 應用程式/API** ] 做為 [應用程式類型]，然後輸入服務的驗證 URL。 然後點擊 [**建立**]。
        4.  在該應用程式上，按 [**設定**]，然後選取 [**金鑰**] 索引標籤。輸入金鑰的名稱，選取持續時間，然後點擊 [**儲存**]。 請務必儲存該時間所顯示的金鑰值，因為您必須將它包含在 web 服務的程式碼中。
    2.  將資源的存取權授與您的應用程式和/或使用者：
        1.  流覽至您在 Azure 入口網站中的空間錨點資源
        2.  切換至 [**存取控制（IAM）** ] 索引標籤
        3.  點擊**新增角色指派**
        1.  [選取角色](#role-based-access-control)
        2.  在 [**選取**] 欄位中，輸入您所建立的應用程式名稱，以及要指派存取的目標。 如果您想要讓應用程式的使用者對空間錨點帳戶有不同的角色，您應該在 Azure AD 中註冊多個應用程式，並將個別的角色指派給每個。 然後執行您的授權邏輯，為您的使用者使用正確的角色。
    3.  按 [儲存]。
2.  在您的程式碼中（注意：您可以使用 GitHub 上所包含的服務範例）：
    1.  請務必使用您自己的 Azure AD 應用程式的 [應用程式識別碼]、[應用程式密碼] 和 [重新導向 Uri]，做為 ADAL 中的用戶端識別碼、秘密和 RedirectUri 參數
    2.  在 ADAL 的授權單位參數中，將租使用者識別碼設定為您自己的 AAAzure 新增租使用者識別碼
    3.  在您的權杖要求上，將**資源**設定為 "https://sts.mixedreality.azure.com"

如此一來，您的後端服務就可以取得 Azure AD token。 然後，它會將它交換為會傳回用戶端的 MR token。 使用 Azure AD token 來取得 MR token，是透過 REST 呼叫來完成。 以下是範例呼叫：

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

其中，Authorization 標頭的格式如下： `Bearer <accoundId>:<accountKey>`

而回應會以純文字的方式包含 MR token。

該 MR token 接著會傳回給用戶端。 然後，您的用戶端應用程式可以在雲端會話設定中將它設為其存取權杖。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>角色型存取控制

為了協助控制授與服務的應用程式、服務或 Azure AD 使用者的存取層級，已建立下列角色，以視需要針對您的 Azure 空間錨點帳戶進行指派：

- **空間錨點帳戶擁有**者：具有此角色的應用程式或使用者可以建立空間錨點、查詢它們，並將它們刪除。 當您使用帳戶金鑰向帳戶進行驗證時，會將**空間錨點帳戶擁有**者角色指派給已驗證的主體。
- **空間錨點帳戶參與者**：具有此角色的應用程式或使用者可以建立空間錨點、查詢它們，但無法刪除它們。
- **空間錨點帳戶讀取器**：具有這個角色的應用程式或使用者只能查詢空間錨點，但無法建立新的、刪除現有的，或更新空間錨點上的中繼資料。 這通常用於某些使用者策展環境的應用程式，有些則只能召回先前放在該環境中的錨點。

## <a name="next-steps"></a>後續步驟

使用 Azure 空間錨點建立您的第一個應用程式。

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
