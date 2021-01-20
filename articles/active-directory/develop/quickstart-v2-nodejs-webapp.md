---
title: 快速入門：將「使用者登入」新增至 Node.js Web 應用程式 | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，您會了解如何使用 OpenID Connect 在 Node.js Web 應用程式中實作驗證。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: bd750a05f34a18a1260226fb979a82cc620dfbfb
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178274"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>快速入門：將使用 OpenID Connect 的登入新增至 Node.js Web 應用程式

在本快速入門中，您會下載並執行程式碼範例，該範例會示範如何在使用 Node.js 與 Express 所建置的 Web 應用程式中設定 OpenID Connect 驗證。 此範例已設計為可在任何平台上執行。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>註冊您的應用程式

1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
1. 搜尋並選取 [Azure Active Directory]  。
1. 在 **管理** 下選取 [應用程式註冊] > [新增註冊]。
1. 輸入應用程式的 [名稱]，例如 `MyWebApp`。 您的應用程式使用者可能會看到此名稱，您可以稍後再變更。
1. 在 [支援的帳戶類型] 區段中，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶 (例如 Skype、Xbox、Outlook.com)]。

    如果有多個重新導向 URI，您稍後必須在應用程式建立成功之後，從 [驗證] 索引標籤新增這些 URI。

1. 選取 [註冊] 以建立應用程式。
1. 在應用程式的 [概觀] 頁面上，尋找 [應用程式 (用戶端) 識別碼] 值並將它記下供稍後使用。 稍後在此專案中，您會需要用這個值來設定應用程式。
1. 在 [管理] 底下，選取 [驗證]。
1. 選取 [新增平台] > [Web]。 
1. 在 [重新導向 URI] 區段中，輸入 `http://localhost:3000/auth/openid/return`。
1. 輸入 [登出 URL] `https://localhost:3000`。
1. 在 [隱含授與] 區段中，核取 [識別碼權杖]，因為此範例需要啟用[隱含授與流程](./v2-oauth2-implicit-grant-flow.md)才能將使用者登入。
1. 選取 [設定] 。
1. 在 **管理** 下，選取 [憑證與秘密] > [新增用戶端密碼]。
1. 輸入 (執行個體應用程式祕密的) 金鑰描述。
1. 選取金鑰持續時間，此值可為 [1 年]、[2 年] 或 [永不過期]。
1. 選取 [新增]。 將會顯示金鑰值。 複製金鑰值，並將此值儲存在安全的位置，以便稍後使用。


## <a name="download-the-sample-application-and-modules"></a>下載應用程式範例和模組

接下來，請複製存放庫範例並安裝 NPM 模組。

從殼層或命令列：

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

或

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

從專案根目錄中，執行命令：

`$ npm install`

## <a name="configure-the-application"></a>設定應用程式

依照指示，在 config.js 的 `exports.creds` 中提供參數。

* 將 `exports.identityMetadata` 中的 `<tenant_name>` 更新為 Azure AD 租用戶名稱 (採用 \*.onmicrosoft.com 格式)。
* 將 `exports.clientID` 更新為從應用程式註冊所記下的應用程式識別碼。
* 將 `exports.clientSecret` 更新為從應用程式註冊所記下的應用程式祕密。
* 將 `exports.redirectUrl` 更新為從應用程式註冊所記下的重新導向 URI。

**生產應用程式的選擇性設定：**

* 如果您想要使用不同的 `post_logout_redirect_uri`，請更新 config.js 中的 `exports.destroySessionUrl`。

* 如果您想要使用 [mongoDB](https://www.mongodb.com) 或其他[相容的工作階段存放區](https://github.com/expressjs/session#compatible-session-stores)，請將 config.js 中的 `exports.useMongoDBSessionStore` 設定為 true。
這個範例中的預設工作階段存放區是 `express-session`。 預設工作階段存放區不適合用於生產環境。

* 如果您想要使用 mongoDB 工作階段存放區和不同的資料庫 URI，請更新 `exports.databaseUri`。

* 更新 `exports.mongoDBSessionMaxAge`。 您可以在這裡指定要讓工作階段在 mongoDB 中保留多久。 單位為秒。

## <a name="build-and-run-the-application"></a>建置並執行應用程式

啟動 mongoDB 服務。 如果您要在此應用程式中使用 mongoDB 工作階段存放區，就必須先[安裝 mongoDB](http://www.mongodb.org/) 並啟動服務。 如果您要使用預設的工作階段存放區，則可以略過此步驟。

從命令列使用下列命令來執行應用程式。

```
$ node app.js
```

**伺服器的輸出很難看懂嗎？：** 在此範例中，我們會使用 `bunyan` 來進行記錄。 除非您也安裝 bunyan 並如上執行伺服器，但改為透過 bunyan 二進位檔以管道進行傳輸，否則您大概會看不懂主控台：

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>大功告成！

您將會在 `http://localhost:3000` 上順利執行伺服器。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟
深入了解 Microsoft 身分識別平台所支援的 Web 應用程式案例：
> [!div class="nextstepaction"]
> [登入使用者的 Web 應用程式案例](scenario-web-app-sign-user-overview.md)