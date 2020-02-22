---
title: 使用 Azure Active Directory B2C 和 Azure API 管理來保護具有 OAuth 2.0 的 SPA 後端。
description: 使用 Azure Active Directory B2C、Azure API 管理和簡單的驗證，從 JavaScript SPA 呼叫來保護具有 OAuth 2.0 的 API。
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new
ms.openlocfilehash: daf38baf9daff5fd192091be977a996c9bd5cfc2
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539860"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>使用 OAuth 2.0、Azure Active Directory B2C 和 Azure API 管理來保護 SPA 後端

此案例說明如何設定 Azure API 管理實例來保護 API。
我們會搭配使用 OpenID Connect 通訊協定與 Azure AD B2C，並搭配 API 管理，使用 EasyAuth 來保護 Azure Functions 後端。

## <a name="aims"></a>旨在
我們會瞭解如何在 Azure Functions 和 Azure AD B2C 的簡化案例中使用 API 管理。 您將建立呼叫 API 的 JavaScript （JS）應用程式，以 Azure AD B2C 登入使用者。 接著，您將使用 API 管理的驗證-jwt 原則功能來保護後端 API。

為了深層防禦，我們接著會使用 EasyAuth 在後端 API 內再次驗證權杖。

## <a name="prerequisites"></a>必要條件
若要依照本文中的步驟進行，您必須有：
* Azure （StorageV2）一般用途 V2 儲存體帳戶，用來裝載前端 JS 單一頁面應用程式
* Azure API 管理實例 
* 空的 Azure 函數應用程式（在 Windows 取用方案上執行 V2 .NET Core 執行時間）來裝載呼叫的 API
* 連結至訂用帳戶的 Azure AD B2C 租使用者 

雖然在實務上，您會在生產工作負載中使用相同區域中的資源，但針對此操作說明文章，部署的區域並不重要。

## <a name="overview"></a>概觀
以下是使用中元件的圖例，而在此程式完成之後，會在兩者之間進行流程。
![使用中的元件和流程](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "使用中的元件和流程")

以下為步驟概述：

1. 建立 Azure AD B2C 呼叫（前端、API 管理）和具有範圍的 API 應用程式，並授與 API 存取權
1. 建立註冊或登入原則，以允許使用者使用 Azure AD B2C 登入 
1. 使用新的 Azure AD B2C 用戶端識別碼和金鑰來設定 API 管理，以在開發人員主控台中啟用 OAuth2 使用者授權
1. 建立函數 API
1. 設定函式 API 以使用新的 Azure AD B2C 用戶端識別碼和金鑰來啟用 EasyAuth，並鎖定至 APIM VIP 
1. 在 API 管理中建立 API 定義
1. 設定 API 管理 API 設定的 Oauth2
1. 設定**CORS**原則並新增**驗證-jwt**原則，以驗證每個連入要求的 OAuth 權杖
1. 建立呼叫應用程式以使用 API
1. 上傳 JS SPA 範例
1. 使用新的 Azure AD B2C 用戶端識別碼和金鑰來設定範例 JS 用戶端應用程式 
1. 測試用戶端應用程式

## <a name="configure-azure-ad-b2c"></a>設定 Azure AD B2C 
在入口網站中開啟 [Azure AD B2C] 分頁，然後執行下列步驟。
1. 選取 [**應用程式**] 索引標籤 
1. 按一下 [新增] 按鈕，並針對下列目的建立三個應用程式
   * 前端用戶端。
   * 後端函數 API。
   * 選擇性API 管理開發人員入口網站（除非您是在取用層中執行 Azure API 管理，稍後會詳細說明此案例）。
1. 設定 WebApp/Web API 並允許隱含流程為是
1. 現在設定 [應用程式識別碼 URI]，選擇唯一且與所建立之服務相關的內容。
1. 現在請使用回復 url 的預留位置，例如 https://localhost，我們稍後會更新這些 url。
1. 按一下 [建立]，然後針對上述三個應用程式重複步驟2-5，記錄 AppID URI、名稱和應用程式識別碼，以供之後用於所有三個應用程式。
1. 從應用程式清單開啟後端 API，並選取 [*金鑰*] 索引標籤（在 [一般] 底下），然後按一下 [產生金鑰] 以產生驗證金鑰
1. 按一下 [儲存] 後，將金鑰記錄在安全的位置以供稍後使用-請注意，這個位置是您唯一可以查看並複製此金鑰的機會。
1. 現在選取 [*已發佈的範圍*] 索引標籤（在 [API 存取] 底下）
1. 建立並命名函式 API 的範圍，並記錄範圍並填入完整範圍值，然後按一下 [儲存]。
   > [!NOTE]
   > Azure AD B2C 範圍是 API 內的有效許可權，其他應用程式可以從其應用程式要求透過 API 存取分頁來存取，實際上您只是為呼叫的 API 建立應用程式許可權。
1. 開啟其他兩個應用程式，然後查看 [ *API 存取*] 索引標籤底下。
1. 授與他們對後端 API 範圍的存取權，以及已存在的預設值（「存取使用者的設定檔」）。
1. 藉由選取 [一般] 底下的 [*金鑰*] 索引標籤來產生驗證金鑰，並在稍後安全地記錄這些金鑰，每個金鑰產生一個索引鍵。

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>建立「註冊或登入」使用者流程
1. 回到 Azure AD B2C 分頁的根（或「總覽」） 
1. 然後選取 [消費者流程（原則）]，再按一下 [新增使用者流程]
1. 選擇 [註冊和登入] 使用者流程類型
1. 為原則命名並加以記錄，以供稍後使用。
1. 然後在 [識別提供者] 底下，勾選 [使用者識別碼註冊]，然後按一下 [確定]。 
1. 在 [使用者屬性和宣告] 底下，按一下 [顯示更多 ...]然後選擇您要讓使用者輸入並在權杖中傳回的宣告選項。 請至少檢查「顯示名稱」和「電子郵件地址」以收集並返回，然後按一下 [確定]，再按一下 [建立]。
1. 選取您在清單中建立的原則，然後按一下 [執行使用者流程] 按鈕。
1. 此動作會開啟 [執行使用者流程] 分頁，選取 [前端] 應用程式，然後記錄顯示在 [選取網域] 下拉式清單底下的 b2clogin.com 網域位址。
1. 按一下頂端的連結以開啟 [知名的 openid 設定端點]，並將 [authorization_endpoint] 和 [token_endpoint 值] 和 [連結本身] 的值一併記錄為知名的 openid 設定端點。

   > [!NOTE]
   > B2C 原則可讓您公開 Azure AD B2C 登入端點，以便能夠以不同的方式來捕捉不同的資料元件和登入使用者。 在此情況下，我們設定了註冊或登入端點，這會公開知名的設定端點，特別是我們在 URL 中以 p = 參數識別所建立的原則。
   > 
   > 完成後，您現在有一個可運作的企業對取用者身分識別平臺，會將使用者登入多個應用程式。 
   > 如果您想要在這裡按一下 [執行使用者流程] 按鈕（以進行註冊或登入程式），並瞭解其實際作法，但結束的重新導向步驟將會失敗，因為尚未部署應用程式。

## <a name="build-the-function-api"></a>建立函數 API
1. 移至 Azure 入口網站的 [函式應用程式] 分頁，開啟您的空白函式應用程式，然後透過快速入門建立新的入口網站內「Webhook + API」功能。
1. 將下方的範例程式碼貼到 .csx 上的現有程式碼。

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > 您剛貼上的 c # 腳本函式程式碼只會在函式記錄中記錄一行，並以一些動態資料（日期和時間）傳回 "Hello World" 文字。

3. 從左側的分頁中選取 [整合]，然後選取窗格右上角的 [進階編輯器]。
4. 將下面的範例程式碼貼到現有的 json 上。

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. 切換回 [HttpTrigger1] 索引標籤，按一下 [取得函數 URL]，然後複製顯示的 URL。

   > [!NOTE]
   > 您剛建立的系結只會告訴函式對您剛才複製的 URL 回應匿名 HTTP GET 要求。 （ https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) 現在我們有可調整的無伺服器 HTTPs API，它能夠傳回非常簡單的裝載。
   > 您現在可以使用上述 URL，從網頁瀏覽器測試呼叫此 API，您也可以去除 URL 的？ code = secret 部分，並證明 Azure Functions 會傳回401錯誤。

## <a name="configure-and-secure-the-function-api"></a>設定和保護函數 API
1. 必須設定函數應用程式中的兩個額外區域（驗證和網路限制）。
1. 首先，我們要設定驗證/授權，因此按一下函式應用程式的名稱（&lt;Z&gt; 函式圖示旁），以顯示 [總覽] 頁面。
1. 接下來，選取 [平臺功能] 索引標籤，然後選取 [驗證/授權]。
1. 開啟 App Service 驗證功能。
1. 在 [驗證提供者] 底下選擇 [Azure Active Directory]，然後從 [管理模式] 交換器選擇 [Advanced]。
1. 貼上後端 API 的應用程式識別碼（從 Azure AD B2C 到 [用戶端識別碼] 方塊）
1. 將知名的開啟識別碼設定端點從註冊或登入原則貼入 [簽發者 URL] 方塊中（我們先前已記錄此設定）。
1. 將您稍早針對 Azure AD B2C 應用程式所記錄的三個（或兩個）應用程式識別碼新增至允許的權杖物件中，這項設定會告訴 EasyAuth 在收到的權杖中允許哪些 AUD 宣告值。
1. 選取 [確定]，然後按一下 [儲存]。

   > [!NOTE]
   > 現在，您的函式 API 已部署，而且應該會針對未經授權的要求擲回401或403錯誤，而且應該會在出示有效要求時傳回資料。
   > 但我們還是沒有 IP 安全性，如果您有有效的金鑰，可以從任何地方呼叫它-最理想的方式是強制所有要求都透過 API 管理來進行。
   > 此外，如果您使用 API 管理取用量層，您將無法透過 VIP 執行此鎖定，因為該層沒有專用的靜態 IP，您必須依賴透過共用密碼功能金鑰鎖定 API 呼叫的方法，因此將無法執行步驟11-14。

1. 關閉 [驗證/授權] 分頁 
1. 選取 [網路]，然後選取 [存取限制]
1. 接下來，將允許的函式應用程式 Ip 鎖定至 API 管理實例 VIP。 此 VIP 會顯示在入口網站的 [API 管理-總覽] 區段中。
1. 如果您想要繼續與函式入口網站互動，並執行下列選擇性步驟，您也應該在此新增自己的公用 IP 位址或 CIDR 範圍。
1. 一旦清單中有允許專案，Azure 就會新增隱含的拒絕規則來封鎖所有其他位址。 

您必須將 CIDR 格式的位址區塊新增至 [IP 限制] 面板。 當您需要新增單一位址（例如 API 管理 VIP）時，您需要以 xx. xx/32 格式來新增它。

   > [!NOTE]
   > 現在，您的函式 API 不應從透過 API 管理或您的位址以外的任何地方進行呼叫。

## <a name="import-the-function-app-definition"></a>匯入函數應用程式定義
1. 開啟 [API 管理] 入口網站分頁，然後選取您的 API 管理實例。
1. 從實例的 [API 管理] 區段中，選取 [Api] 分頁。
1. 從 [新增 API] 窗格中，選擇 [函數應用程式]，然後從快顯視窗頂端選取 [完整]。
1. 按一下 [流覽]，選擇您要在其中裝載 API 的函式應用程式，然後按一下 [選取]。
1. 提供 API 管理內部使用的名稱和描述，並將其新增至「無限制」的產品。
1. 請務必記錄基底 URL 以供稍後使用，然後按一下 [建立]。

## <a name="configure-oauth2-for-api-management"></a>設定 API 管理的 Oauth2
1. 切換回 Azure 入口網站中的標準 Azure AD 租使用者，讓我們可以再次設定訂用帳戶中的專案，然後開啟 [ *API 管理*] 分頁，再開啟*您的實例*。
1. 接下來，從 [安全性] 索引標籤選取 [Oauth 2.0] 分頁，然後按一下 [新增]
1. 為已新增的 Oauth 端點指定*顯示名稱*和*描述*的值（這些值會在下一個步驟中顯示為 Oauth2 端點）。
1. 您可以在 [用戶端註冊頁面 URL] 中輸入任何值，因為將不會使用此值。
1. 檢查 [*隱含驗證*] 授與類型，並選擇性地取消核取 [授權碼授與類型]。
1. 移至 [*授權*] 和 [*權杖*端點] 欄位，然後輸入您先前從知名設定 xml 檔中捕捉到的值。
1. 使用來自 Azure AD B2C 應用程式註冊的函式 API 用戶端識別碼，向下並填入稱為「資源」的*其他主體參數*
1. 選取 [用戶端認證]，將用戶端識別碼設定為開發人員主控台應用程式的應用程式識別碼-如果使用取用 API 管理模型，請略過此步驟。
1. 將用戶端密碼設定為您稍早記錄的金鑰-如果使用取用 API 管理模型，請略過此步驟。
1. 最後，現在記錄來自 API 管理的驗證碼授與的 redirect_uri，以供稍後使用。

## <a name="set-up-oauth2-for-your-api"></a>為您的 API 設定 Oauth2
1. 您的 API 會出現在入口網站左側的 [所有 Api] 區段底下，按一下即可開啟您的 API。
1. 選取 [設定] 索引標籤。
1. 從 [使用者授權] 選項按鈕選取 [Oauth 2.0]，以更新您的設定。
1. 選取您稍早定義的 Oauth 伺服器。
1. 核取 [覆寫範圍] 核取方塊，並輸入先前針對後端 API 呼叫所記錄的範圍。

   > [!NOTE]
   > 現在，我們有一個 API 管理實例，知道如何從 Azure AD B2C 取得存取權杖來授權要求，並瞭解我們的 Oauth2 Azure Active Directory B2C 設定。

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>設定**CORS**和**驗證 jwt**原則

> 不論使用的 APIM 層為何，都應遵循下列各節。 

1. 切換回 [設計] 索引標籤並選擇 [所有 Api]，然後按一下 [程式碼視圖] 按鈕以顯示 [原則編輯器]。
1. 編輯輸入區段並貼上下列 xml，使其看起來如下所示。

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. 編輯 openid-config url，以符合您的知名 Azure AD B2C 端點，以進行註冊或登入原則。
1. 編輯宣告值，使其符合有效的應用程式識別碼（也稱為後端 API 應用程式的用戶端識別碼）並儲存。
1. 選取 [所有 Api] 下方的 api 作業

   > [!NOTE]
   > 現在，API 管理能夠回應 JS SPA 應用程式的跨原始來源要求，而且它會執行節流、速率限制和預先驗證所傳遞的 JWT 驗證權杖，然後再將要求轉送至函數 API。

   > [!NOTE]
   > 下列區段是選擇性的，不適用於不支援開發人員入口網站的**耗用量**層。
   > 如果您不想要使用開發人員入口網站，或因為您使用的是取用層而無法使用它，請略過此步驟，並直接跳到「[建立 JAVASCRIPT SPA 以取用 API](##build-the-javascript-spa-to-consume-the-api)」。

## <a name="optional-configure-the-developer-portal"></a>選擇性設定開發人員入口網站

1. 開啟 [Azure AD B2C] 分頁，然後流覽至開發人員入口網站的應用程式註冊
1. 當您稍早在 API 管理中設定驗證碼授與的 redirect_uri 時，請將 [回復 URL] 輸入設為您記下的專案。

   既然已在 `Echo API`上啟用 OAuth 2.0 使用者授權，開發人員主控台會先取得使用者的存取權杖，然後再呼叫 API。

1. 在開發人員入口網站中，流覽至 `Echo API` 下的任何作業，然後選取 [**試試看**] 以帶您前往開發人員主控台。
1. 請注意，在 [授權] 區段中有一個新項目對應到您剛才新增的授權伺服器。
1. 從 [授權] 下拉式清單中選取 [**授權碼**]，系統就會提示您登入 Azure AD 租使用者。 如果您已經使用帳戶登入，則可能不會提示您。
1. 成功登入之後，會將 `Authorization: Bearer` 標頭加入至要求中，並以 Base64 編碼 Azure AD B2C 的存取權杖。 
1. 選取 [**傳送**]，您就可以成功呼叫 API。

   > [!NOTE]
   > 現在，API 管理能夠取得開發人員入口網站的權杖來測試您的 API，並能夠瞭解其定義，並在開發人員入口網站中呈現適當的測試頁面。

1. 從 API 管理入口網站的 [總覽] 分頁中，按一下 [開發人員入口網站] 以 API 的系統管理員身分登入。
1. 在這裡，您和您的 API 的其他選取取用者可以從主控台進行測試和呼叫。
1. 選取 [產品]，然後選擇 [無限制]，再選擇我們稍早建立的 API，然後按一下 [試試看]
1. 取消隱藏 API 訂用帳戶金鑰，並將它複製到安全的位置，以及稍後需要的要求 url。
1. 此外，從 [oauth 驗證] 下拉式清單中選取 [隱含]，您可能必須使用快顯在此進行驗證。
1. 按一下 [傳送]，如果一切順利，您的函數應用程式應該透過 API 管理以「200確定」訊息和一些 JSON 來回應 hello 訊息。

   > [!NOTE]
   > 恭喜，您現在已有 Azure AD B2C、API 管理和 Azure Functions 共同合作，以發佈、保護和使用 API。 您可能已經注意到，使用此方法時，API 的安全兩次，一次是使用 API 管理 Ocp-訂用帳戶金鑰標頭，而一次使用授權：持有人 JWT。
   > 您會正確，因為此範例是 JavaScript 單一頁面應用程式，我們只會使用 API 管理金鑰進行速率限制和計費呼叫。
   > 實際的授權和驗證會由 Azure AD B2C 來處理，並封裝在 JWT 中，這會透過 API 管理來進行驗證兩次，然後再 Azure Functions。

## <a name="build-the-javascript-spa-to-consume-the-api"></a>建立 JavaScript SPA 以使用 API
1. 在 Azure 入口網站中開啟 儲存體帳戶 分頁 
1. 選取您建立的帳戶，然後從 [設定] 區段中選取 [靜態網站] 分頁（如果您沒有看到 [靜態網站] 選項，請檢查您是否已建立 V2 帳戶）。
1. 將 [靜態 web 主控] 功能設定為 [已啟用]，並將 [索引檔案名稱] 設定為 [.html]，然後按一下 [儲存]。
1. 記下主要端點的內容，因為此位置是前端網站的主控位置。 

   > [!NOTE]
   > 您可以使用 Azure Blob 儲存體 + CDN 重寫，或 Azure App Service 但 Blob 儲存體的靜態網站裝載功能提供預設容器，以從 Azure 儲存體提供靜態 web 內容/html/js/css，並會為我們推斷預設頁面，讓我們進行零工作。

## <a name="upload-the-js-spa-sample"></a>上傳 JS SPA 範例
1. 仍在 [儲存體帳戶] 分頁中，從 [Blob 服務] 區段選取 [Blob] 分頁，然後按一下右側窗格中的 [$web] 容器。
1. 將下列程式碼儲存到您電腦本機上的檔案做為 index .html，然後將檔案索引 .html 上傳至 $web 容器。

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. 流覽至您稍早在最後一節中儲存的靜態網站主要端點。

   > [!NOTE]
   > 恭喜您，您剛將 JavaScript 單頁應用程式部署到 Azure 儲存體，因為我們尚未使用您的 api 金鑰來設定 JS 應用程式，或已使用您的 Azure AD B2C 詳細資料設定 JS 應用程式–如果您開啟該頁面，此網頁將無法使用。

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>設定適用于 Azure AD B2C 的 JS SPA
1. 現在我們知道所有專案的所在位置：我們可以使用適當的 API 管理 API 位址和正確的 Azure AD B2C 應用程式/用戶端識別碼來設定 SPA
1. 回到 [Azure 入口網站儲存體] 分頁，然後按一下 [.html]，再選擇 [編輯 Blob] 
1. 更新驗證詳細資料，以符合您稍早在 B2C 中註冊的前端應用程式，請注意，' b2cScopes ' 值適用于 API 後端。
1. 在 API 作業的 [API 管理] 測試窗格中，可以找到 [webApi 金鑰] 和 [api url]。
1. 建立 APIM 訂用帳戶金鑰，方法是將 API 管理的標題改回 [API 管理] 分頁，選取 [訂用帳戶]，然後按一下 [新增訂閱]，然後儲存記錄。 按一下所建立資料列旁的省略號（...），即可顯示金鑰，讓您可以複製主要金鑰。
1. 看起來應該像下面這段程式碼：-  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. 按一下 [儲存]。

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>設定 Azure AD B2C 前端應用程式的重新導向 Uri
1. 開啟 [Azure AD B2C] 分頁，然後流覽至 JavaScript 前端應用程式的應用程式註冊
1. 將重新導向 URL 設定為您先前設定上述靜態網站主要端點時所記下的 URL

   > [!NOTE] 
   > 此設定會導致前端應用程式的用戶端接收具有來自 Azure AD B2C 之適當宣告的存取權杖。
   > SPA 可以在對後端 API 的呼叫中，將此新增為 HTTPs 標頭中的持有人權杖。
   > API 管理會先依照訂閱者金鑰來預先驗證權杖、速率限制呼叫端點，然後再將要求傳遞至接收的 Azure Function API。
   > SPA 會在瀏覽器中呈現回應。

   > *恭喜，您已設定 Azure AD B2C、Azure API 管理、Azure Functions Azure App Service 授權以完美的協調方式工作！*

   > [!NOTE]
   > 現在我們有一個簡單的應用程式，其中包含簡單的安全 API，讓我們進行測試。

## <a name="test-the-client-application"></a>測試用戶端應用程式
1. 從您稍早建立的儲存體帳戶中，開啟您記下的範例應用程式 URL
1. 按一下右上角的 [登入]，這會按一下以快顯您的 Azure AD B2C 註冊或登入設定檔。
1. 在畫面的 [登入身分] 區段中，將會從您的 JWT 填入 Post。
1. 現在，按一下 [呼叫 Web Api]，您應該會使用從受保護的 API 傳回的值來更新頁面。

## <a name="and-were-done"></a>我們已經完成了
您可以調整和編輯上述步驟，以允許使用多個不同的 Azure AD B2C 搭配 API 管理。

## <a name="next-steps"></a>後續步驟
* 深入了解 [Azure Active Directory 和 OAuth2.0](../active-directory/develop/authentication-scenarios.md)。
* 查看更多有關 API 管理的 [視訊](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 。
* 如需其他保護後端服務的方式，請參閱[相互憑證驗證](api-management-howto-mutual-certificates.md)。
* 請考慮使用 Azure AD 圖形 API 來指派自訂宣告，並使用 API 管理原則來驗證它們是否存在於權杖中。

* [建立 API 管理服務執行個體](get-started-create-service-instance.md)。

* [管理第一個 API](import-and-publish.md)。
