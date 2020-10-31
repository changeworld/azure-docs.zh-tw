---
title: 驗證和授權
description: 深入瞭解應用程式或服務可向 Azure 空間錨點進行驗證的各種方式，以及您必須對空間錨點進行閘道存取的控制層級。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 12f9a91995eb35fa61a7df5f3ead5255aea0f071
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089027"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure 空間錨點的驗證和授權

在本文中，您將瞭解可從應用程式或 web 服務向 Azure 空間錨點進行驗證的各種方式。 您也會瞭解如何使用 Azure 角色型存取控制 (Azure RBAC) Azure Active Directory (Azure AD) 來控制空間錨點帳戶的存取權。

## <a name="overview"></a>概觀

![此圖顯示 Azure 空間錨點驗證的總覽。](./media/spatial-anchors-authentication-overview.png)

若要存取指定的 Azure 空間錨點帳戶，用戶端必須先從 Azure 混合實境 Security Token Service (STS) 取得存取權杖。 從 STS 取得的權杖有24小時的存留期。 其中包含空間錨點服務用來對帳戶進行授權決策，並確保只有授權的主體可以存取帳戶的資訊。

您可以在 exchange 中取得存取權杖，以取得帳戶金鑰或 Azure AD 所簽發的權杖。

帳戶金鑰可讓您快速開始使用 Azure 空間錨點服務。 但是，在您將應用程式部署至生產環境之前，建議您將應用程式更新為使用 Azure AD 驗證。

您可以透過兩種方式取得 Azure AD 驗證權杖：

- 如果您正在建立企業應用程式，且您的公司使用 Azure AD 作為其身分識別系統，則您可以在應用程式中使用以使用者為基礎的 Azure AD 驗證。 然後，您可以使用現有的 Azure AD 安全性群組，授與空間錨點帳戶的存取權。 您也可以直接將存取權授與您組織中的使用者。
- 否則，建議您從支援您應用程式的 web 服務取得 Azure AD 的權杖。 針對實際執行應用程式，我們建議使用此方法，因為它可讓您避免在用戶端應用程式中內嵌存取 Azure 空間錨點的認證。

## <a name="account-keys"></a>帳戶金鑰

開始使用的最簡單方式是使用帳戶金鑰來存取您的 Azure 空間錨點帳戶。 您可以在 Azure 入口網站取得您的帳戶金鑰。 移至您的帳戶，然後選取 [ **金鑰** ] 索引標籤：

![顯示 [金鑰] 索引標籤的螢幕擷取畫面，其中已反白顯示主鍵的 [複製] 按鈕。](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

有兩個金鑰可供使用。 兩者同時適用于空間錨點帳戶的存取權。 建議您定期更新用來存取帳戶的金鑰。 有兩個不同的有效金鑰可啟用這些更新而不需要停機。 您只需要更新主要金鑰和次要金鑰。

SDK 具有透過帳戶金鑰進行驗證的內建支援。 您只需要 `AccountKey` 在物件上設定屬性即可 `cloudSession` ：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

設定該屬性之後，SDK 會處理存取權杖的帳戶金鑰交換，以及您應用程式所需的權杖快取。

> [!WARNING]
> 我們建議您只在開發/原型設計期間使用帳戶金鑰進行快速入門。 我們不建議您使用內嵌的帳戶金鑰，將您的應用程式寄送給生產環境。 請改為使用以使用者為基礎或以服務為基礎的 Azure AD 驗證方法（如下所述）。

## <a name="azure-ad-user-authentication"></a>Azure AD 使用者驗證

針對以 Azure Active Directory 使用者為目標的應用程式，我們建議您為使用者使用 Azure AD 權杖。 您可以使用 [MSAL](../../active-directory/develop/msal-overview.md)取得此權杖。 遵循 [註冊應用程式快速入門](../../active-directory/develop/quickstart-register-app.md)中的步驟，其中包括：

**在 Azure 入口網站中**
1.    在 Azure AD 中將應用程式註冊為原生應用程式。 在註冊過程中，您必須判斷您的應用程式是否應該是多租使用者。 您也必須提供應用程式所允許的重新導向 Url。
1.  移至 [ **API 許可權** ] 索引標籤。
2.  選取 [新增權限]。
    1.  在 [ **我的組織使用的 api** ] 索引標籤上選取 [ **混合現實資源提供者** ]
    2.  選取 [委派的權限]。
    3.  選取 [ **mixedreality** ] 下的 [ **mixedreality** ]。
    4.  選取 [新增權限]。
3.  選取 **[授與系統管理員同意** ]。
    
2. 對您的應用程式或使用者授與資源的存取權：
   1.    移至 Azure 入口網站中的空間錨點資源。
   2.    移至 [ **存取控制] (IAM)** 索引標籤。
   3.    選取 [新增角色指派]。
   1.    [選取角色](#azure-role-based-access-control)。
   2.    在 [ **選取** ] 方塊中，輸入您要指派存取權的使用者、群組和/或應用程式的名稱。
   3.    選取 [儲存]  。

**在您的程式碼中**
1.    請務必針對 MSAL 中的 **用戶端識別碼** 和 **RedirectUri** 參數使用您自己的 Azure AD 應用程式的應用程式識別碼和重新導向 URI。
2.    設定租用戶資訊：
        1.    如果您的應用程式 **僅支援我的組織** ，請將此值取代為您的租使用者 **識別碼** 或租使用者 **名稱** 。 例如，contoso.microsoft.com。
        2.    如果您的應用程式支援 **任何組織目錄中的帳戶** ，請將此值取代為 **組織** 。
        3.    如果您的應用程式支援 **所有 Microsoft 帳戶使用者** ，請將此值取代為 **Common** 。
3.    在您的權杖要求中，將 **範圍** 設定為 **" https://sts.mixedreality.azure.com//.default "** 。 此範圍會向 Azure AD 表示您的應用程式正在要求用於混合實境 Security Token Service (STS) 的權杖。

完成這些步驟之後，您的應用程式應該能夠從 MSAL Azure AD token 取得。 您可以將該 Azure AD token 設定為 `authenticationToken` 雲端會話設定物件上的：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD 服務驗證

若要將使用 Azure 空間錨點的應用程式部署至生產環境，建議您使用將會 broker 驗證要求的後端服務。 以下是流程的總覽：

![提供 Azure 空間錨點驗證的摘要圖表。](./media/spatial-anchors-aad-authentication.png)

在此，我們假設您的應用程式使用自己的機制來向後端服務進行驗證。  (例如，Microsoft 帳戶、PlayFab、Facebook、Google ID 或自訂使用者名稱和密碼 ) 。在使用者向後端服務驗證之後，該服務就能取得 Azure AD 權杖、將它交換給 Azure 空間錨點的存取權杖，然後傳回用戶端應用程式。

Azure AD 存取權杖是透過 [MSAL](../../active-directory/develop/msal-overview.md)抓取。 遵循 [註冊應用程式快速入門](../../active-directory/develop/quickstart-register-app.md)中的步驟，其中包括：

**在 Azure 入口網站中**
1.    在 Azure AD 中註冊您的應用程式：
        1.    在 [Azure 入口網站中，選取 [ **Azure Active Directory** ]，然後選取 [ **應用程式註冊** ]。
        2.    選取 [新增註冊]。
        3.    輸入應用程式的名稱，選取 [Web 應用程式/API] 作為 [應用程式類型]，然後輸入服務的驗證 URL。 選取 [建立]。
4.    在應用程式上，選取 [ **設定** ]，然後選取 [ **憑證和密碼** ] 索引標籤。建立新的用戶端密碼，選取持續時間，然後選取 [ **新增** ]。 務必儲存秘密值。 您必須將它包含在 web 服務的程式碼中。
2.    對您的應用程式及 (或) 使用者授與資源的存取權：
        1.    移至 Azure 入口網站中的空間錨點資源。
        2.    移至 [ **存取控制] (IAM)** 索引標籤。
        3.    選取 [新增角色指派]。
        1.    [選取角色](#azure-role-based-access-control)。
        2.    在 [ **選取** ] 方塊中，輸入您要指派存取權的應用程式名稱或名稱。 如果您想要讓應用程式的使用者針對空間錨點帳戶使用不同的角色，請在 Azure AD 中註冊多個應用程式，並為每個應用程式指派個別的角色。 然後執行您的授權邏輯，為您的使用者使用正確的角色。
        
              > [!NOTE] 
              > 在 [ **新增角色指派** ] 窗格的 [ **將存取權指派給** ] 中，選取 [ **Azure AD 使用者、群組或服務主體** ]。
    
      3.    選取 [儲存]  。
    
**在您的程式碼中** 

>[!NOTE] 
> 您可以使用 GitHub 上提供的服務範例。

1.    請務必使用您自己的 Azure AD 應用程式的應用程式識別碼、應用程式秘密和重新導向 URI，作為 MSAL 中的 **用戶端識別碼** 、 **秘密** 和 **RedirectUri** 參數。
2.    在 MSAL 的 **授權** 參數中，將租使用者識別碼設定為您自己的 Azure AD 租使用者識別碼。
3.    在您的權杖要求中，將 **範圍** 設定為 **" https://sts.mixedreality.azure.com//.default "** 。

完成這些步驟之後，您的後端服務就可以取出 Azure AD 的權杖。 然後將其交換為 MR 權杖，並傳回用戶端。 使用 Azure AD 權杖來擷取 MR 權杖是透過 REST 呼叫來完成的。 以下是範例呼叫：

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

授權標頭的格式如下： `Bearer <Azure_AD_token>`

回應會以純文字形式包含 MR token。

該 MR 權杖接著會傳回給用戶端。 然後，您的用戶端應用程式可以在雲端會話設定中將其設定為其存取權杖：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Azure 角色型存取控制

為了協助您控制授與服務的應用程式、服務或 Azure AD 使用者的存取層級，您可以視需要為您的 Azure 空間錨點帳戶指派這些預先存在的角色：

- **空間錨點帳戶擁有** 者。 具有此角色的應用程式或使用者可以建立空間錨點、查詢它們，以及刪除它們。 當您使用帳戶金鑰組帳戶進行驗證時，會將空間錨點帳戶擁有者角色指派給已驗證的主體。
- **空間錨點帳戶參與者** 。 具有此角色的應用程式或使用者可以建立空間錨點並查詢它們，但無法將其刪除。
- **空間錨點帳戶讀取器** 。 具有此角色的應用程式或使用者只能查詢空間錨點。 他們無法建立新的專案、刪除現有的專案，或更新其上的中繼資料。 此角色通常用於某些使用者策展環境的應用程式，但其他使用者只能重新叫用先前放置於環境中的錨點。

## <a name="next-steps"></a>下一步

使用 Azure 空間錨點來建立您的第一個應用程式：

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
