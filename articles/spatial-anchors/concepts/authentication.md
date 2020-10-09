---
title: 驗證和授權
description: 了解應用程式或服務可向 Azure 空間錨點進行驗證的各種方式，以及存取 Azure 空間錨點所需的控制層級。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 133b565bc54feaf49a2fec9dd0056ca8e7ef43f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91857719"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure 空間錨點的驗證和授權

在本節中，我們將討論可以從您應用程式或 Web 服務向 Azure 空間錨點進行驗證的各種方式，以及如何在 Azure Directory (Azure AD) 中使用角色型存取控制來控制空間錨點帳戶的存取。

## <a name="overview"></a>概觀

![此圖顯示 Azure 空間錨點驗證的總覽。](./media/spatial-anchors-authentication-overview.png)

若要存取指定的 Azure 空間錨點帳戶，用戶端必須先從 Azure 混合實境 Security Token Service (STS) 取得存取權杖。 從 STS 取得的權杖可存留 24 小時，其中會包含讓空間錨點服務對帳戶進行授權決策的資訊，並確保只有授權的主體可以存取該帳戶。

您可以取得從帳戶金鑰或從 Azure AD 所發行權杖交換而來的存取權杖。

帳戶金鑰可讓您快速開始使用 Azure 空間錨點服務；不過，在您將應用程式部署至生產環境之前，建議您先將應用程式更新為使用以 Azure AD 為基礎的驗證。

您可以透過兩種方式取得 Azure AD 驗證權杖：

- 如果您要建立企業應用程式，且您的公司使用 Azure AD 作為其身分識別系統，您可以在應用程式中使用以使用者為基礎的 Azure AD 驗證，並使用現有的 Azure AD 安全性群組或直接對您組織中的使用者授與空間錨點帳戶的存取權。
- 否則，建議您從支援應用程式的 Web 服務取得 Azure AD 權杖。 建議您使用支援的 Web 服務來驗證生產應用程式，因為此方式可避免將用來存取 Azure 空間錨點的認證內嵌到用戶端應用程式。

## <a name="account-keys"></a>帳戶金鑰

使用帳戶金鑰存取您的 Azure 空間錨點帳戶，是最簡單的入門方式。 您會在 Azure 入口網站上找到帳戶金鑰。 流覽至您的帳戶，然後選取 [金鑰] 索引標籤。

![顯示 [金鑰] 頁面的螢幕擷取畫面，其中反白顯示 [主要索引鍵] 的 [複製] 按鈕。](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

有兩個金鑰可供使用，這兩個金鑰可同時用於存取空間錨點帳戶。 建議您定期更新用來存取帳戶的金鑰 (擁有兩個不同的有效金鑰，可在不停機的情況下啟用這類更新)，您只需要更新主要金鑰和次要金鑰。

SDK 具有使用帳戶金鑰進行驗證的內建支援；您只需要在 cloudSession 物件上設定 AccountKey 屬性。

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

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

完成後，SDK 會處理帳戶金鑰與存取權杖的交換作業，以及您應用程式所需的權杖快取作業。

> [!WARNING]
> 建議您使用帳戶金鑰來快速上手，但僅限於在開發/原型設計期間這麼做。 強烈建議您不要將使用內嵌帳戶金鑰的應用程式傳送到生產環境中，而是改為使用以使用者為基礎或以服務為基礎的 Azure AD 驗證方法，如下所述。

## <a name="azure-ad-user-authentication"></a>Azure AD 使用者驗證

針對以 Azure Active Directory 使用者為目標的應用程式，建議的方法是對使用者使用 Azure AD 權杖，您可以使用 [MSAL 程式庫](../../active-directory/develop/msal-overview.md)來取得該權杖。 您應遵循[註冊應用程式快速入門](../../active-directory/develop/quickstart-register-app.md)中列出的步驟，其中包括：

1. Azure 入口網站中的組態
    1.    在 Azure AD 中，將您的應用程式註冊為**原生應用程式**。 在註冊過程中，您必須判斷您的應用程式是否應為多租用戶，並提供應用程式允許的重新導向 URL。
        1.  切換至 [API 權限] 索引標籤
        2.  選取 [新增權限]
            1.  在 [**我的組織使用的 api** ] 索引標籤下，選取**混合的現實資源**
            2.  選取 [委派的權限]
            3.  核取 [mixedreality] 底下的 [mixedreality.signin] 核取方塊
            4.  選取 [新增權限]
        3.  選取 [授與管理員同意]。
    2.    對您的應用程式或使用者授與資源的存取權：
        1.    在 Azure 入口網站中瀏覽至您的空間錨點資源
        2.    切換至 [存取控制 (IAM)] 索引標籤
        3.    點擊 [新增角色指派]
            1.    [選取角色](#role-based-access-control)
            2.    在 [選取] 欄位中，輸入您要對其指派存取權的使用者、群組和/或應用程式名稱。
            3.    按 [儲存]。
2. 在您的程式碼中：
    1.    請務必使用您 Azure AD 應用程式的**應用程式識別碼**和**重新導向 URI** 作為 MSAL 中的 **client ID** 和 **RedirectUri** 參數
    2.    設定租用戶資訊：
        1.    如果您的應用程式支援 [僅限我的組織]，請將此值取代為 [租用戶識別碼] 或 [租用戶名稱] (例如 contoso.microsoft.com)
        2.    如果您的應用程式支援「任何組織目錄中的帳戶」，請將此值取代為 [組織]
        3.    如果您的應用程式支援 [所有 Microsoft 帳戶使用者]，請將此值取代為 [通用]
    3.    在您的權杖要求上，將**範圍**設定為 "https://sts.mixedreality.azure.com//.default"。 此範圍會向 Azure AD 表示您的應用程式正在要求用於混合實境 Security Token Service (STS) 的權杖。

如此一來，您的應用程式應該能夠從 MSAL 取得 Azure AD 權杖；您可以在雲端工作階段設定物件上，將 Azure AD 權杖設為 **authenticationToken**。

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

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD 服務驗證

若要將使用 Azure 空間錨點的應用程式部署至生產環境，建議您利用可居中處理驗證要求的後端服務。 一般配置應如下圖所述：

![Azure 空間錨點的驗證概觀](./media/spatial-anchors-aad-authentication.png)

在此，我們假設您的應用程式使用自己的機制 (例如：Microsoft 帳戶、PlayFab、Facebook、Google ID、自訂使用者名稱/密碼等) 來驗證其後端服務。 當您的使用者通過後端服務的驗證之後，該服務就可以擷取 Azure AD 權杖，將其與 Azure 空間錨點的存取權杖交換，然後傳回給用戶端應用程式。

Azure AD 存取權杖的擷取方式是使用 [MSAL 程式庫](../../active-directory/develop/msal-overview.md)。 您應遵循[註冊應用程式快速入門](../../active-directory/develop/quickstart-register-app.md)中列出的步驟，其中包括：

1.    Azure 入口網站中的組態：
    1.    在 Azure AD 中註冊您的應用程式：
        1.    在 Azure 入口網站中，瀏覽至 **Azure Active Directory**，然後選取 [應用程式註冊]
        2.    選取 [新增應用程式註冊]
        3.    輸入應用程式的名稱，選取 [Web 應用程式/API] 作為 [應用程式類型]，然後輸入服務的驗證 URL。 然後點擊 [建立]。
        4.    在該應用程式上，按一下 [ **設定**]，然後選取 [ **憑證和密碼** ] 索引標籤。建立新的用戶端密碼、選取持續時間，然後按 [ **新增**]。 請務必儲存秘密值，因為您必須將它包含在 web 服務的程式碼中。
    2.    對您的應用程式及 (或) 使用者授與資源的存取權：
        1.    在 Azure 入口網站中瀏覽至您的空間錨點資源
        2.    切換至 [存取控制 (IAM)] 索引標籤
        3.    點擊 [新增角色指派]
        1.    [選取角色](#role-based-access-control)
        2.    在 [選取] 欄位中，輸入您所建立，並且要對其指派存取權的應用程式名稱。 如果您想要讓應用程式的使用者針對空間錨點帳戶使用不同的角色，您應該在 Azure AD 中註冊多個應用程式，並將每個應用程式指派給個別的角色。 然後執行您的授權邏輯，為您的使用者使用正確的角色。
        3.    注意-在 [ **新增角色指派** ] 選項中，您要將 [ **指派存取** 權] 設定為 [Azure AD 使用者、群組或服務主體]。
    3.    按 [儲存]。
2.    在您的程式碼中 (注意：您可以使用 GitHub 上所包含的服務範例)：
    1.    請務必使用您 Azure AD 應用程式的應用程式識別碼、應用程式祕密和重新導向 URI 作為 MSAL 中的 client ID、secret 和 RedirectUri 參數
    2.    在 MSAL 的授權單位參數中，將租用戶識別碼設定為您自己的 Azure ADD 租用戶識別碼。
    3.    在您的權杖要求上，將**範圍**設定為 "https://sts.mixedreality.azure.com//.default"

如此一來，您的後端服務就可以擷取 Azure AD 權杖。 然後將其交換為 MR 權杖，並傳回用戶端。 使用 Azure AD 權杖來擷取 MR 權杖是透過 REST 呼叫來完成的。 以下是呼叫範例：

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

其中，授權標頭的格式如下：`Bearer <Azure_AD_token>`

而回應會以純文字的形式包含 MR 權杖。

該 MR 權杖接著會傳回給用戶端。 然後，您的用戶端應用程式就可以在雲端工作階段設定中將其設為存取權杖。

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

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>角色型存取控制

為了協助控制對應用程式、服務或服務的 Azure AD 使用者授與的存取層級，您可以在需要時對您的 Azure 空間錨點帳戶指派下列已建立的角色：

- **空間錨點帳戶擁有者**：具有此角色的應用程式或使用者可以建立空間錨點，也可以查詢及刪除這些錨點。 當您使用帳戶金鑰向帳戶進行驗證時，**空間錨點帳戶擁有者**角色會指派給已驗證的主體。
- **空間錨點帳戶參與者**：具有此角色的應用程式或使用者可以建立空間錨點並對其進行查詢，但不能刪除這些錨點。
- **空間錨點帳戶讀者**：具有此角色的應用程式或使用只能查詢空間錨點，但無法建立新的錨點、刪除現有的錨點，或更新空間錨點上的中繼資料。 使用此角色的應用程式通常會由某些使用者策畫環境，而其他使用者只能重新叫用先前放在該環境中的錨點。

## <a name="next-steps"></a>後續步驟

使用 Azure Spatial Anchors 建立您的第一個應用程式。

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
