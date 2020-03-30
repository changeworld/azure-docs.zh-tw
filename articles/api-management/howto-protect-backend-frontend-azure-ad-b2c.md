---
title: 通過使用 Azure 活動目錄 B2C 和 Azure API 管理，使用 OAuth 2.0 保護 SPA 後端。
description: 通過使用 Azure 活動目錄 B2C、Azure API 管理和從 JavaScript SPA 調用的簡易 Auth，使用 OAuth 2.0 保護 API。
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
ms.openlocfilehash: 55acea360de11c5fcc699d65daf92cf24dfd691d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475471"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>使用 OAuth 2.0、Azure 活動目錄 B2C 和 Azure API 管理保護 SPA 後端

本方案演示如何配置 Azure API 管理實例以保護 API。
我們將使用 OAuth 2.0 協定與 Azure AD B2C 以及 API 管理一起使用，使用 EasyAuth 保護 Azure 函數後端。

## <a name="aims"></a>目標
我們將瞭解如何在 Azure 函數和 Azure AD B2C 的簡化方案中使用 API 管理。 您將創建一個調用 API 的 JavaScript （JS） 應用，該應用使用 Azure AD B2C 在使用者中簽名。 然後，您將使用 API 管理的驗證 jwt 策略功能來保護後端 API。

為了深入防禦，我們使用 EasyAuth 在後端 API 內再次驗證權杖。

## <a name="prerequisites"></a>Prerequisites
若要依照本文中的步驟進行，您必須有：
* 用於託管前端 JS 單頁應用的 Azure（存儲V2）通用 V2 存儲帳戶
* Azure API 管理實例 
* 空 Azure 函數應用（在 Windows 消耗計畫中運行 V2 .NET 核心運行時），以承載被調用的 API
* 連結到訂閱的 Azure AD B2C 租戶 

儘管實際上您將在同一區域中使用生產工作負載中的資源，但對於此操作操作文章，部署區域並不重要。

## <a name="overview"></a>總覽
下面是在此過程完成後使用的元件及其之間流動的說明。
![正在使用的元件和流](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "正在使用的元件和流")

以下為步驟概述：

1. 使用作用域創建 Azure AD B2C 調用（前端、API 管理）和 API 應用程式並授予 API 存取權限
1. 創建註冊或登錄策略，以允許使用者使用 Azure AD B2C 登錄 
1. 使用新的 Azure AD B2C 用戶端標識和金鑰配置 API 管理，以便在開發人員主控台中啟用 OAuth2 使用者授權
1. 構建函數 API
1. 配置功能 API 以使用新的 Azure AD B2C 用戶端 ID 和金鑰啟用 EasyAuth，並鎖定到 APIM VIP 
1. 在 API 管理中構建 API 定義
1. 為 API 管理 API 配置設置 Oauth2
1. 設置**CORS**策略並添加**驗證 jwt**策略，以驗證每個傳入請求的 OAuth 權杖
1. 構建調用應用程式以使用 API
1. 上傳 JS SPA 示例
1. 使用新的 Azure AD B2C 用戶端 ID 和金鑰配置示例 JS 用戶端應用 
1. 測試用戶端應用程式

## <a name="configure-azure-ad-b2c"></a>配置 Azure AD B2C 
在門戶中打開 Azure AD B2C 邊欄選項卡，然後執行以下步驟。
1. 選擇 **"應用程式**"選項卡 
1. 按一下"添加"按鈕並創建三個應用程式，用於以下目的
   * 前端用戶端。
   * 後端函數 API。
   * [可選]API 管理開發人員門戶（除非您在消費層中運行 Azure API 管理，稍後將介紹此方案的更多內容）。
1. 為所有 3 個應用程式設定 WebApp / Web API，並將"允許隱式流"設置為"僅為前端用戶端"。
1. 現在設置應用 ID URI，選擇與正在創建的服務相關的唯一內容。
1. 現在對答覆 URL 使用預留位置，例如https://localhost，我們將在稍後更新這些 URL。
1. 按一下"創建"，然後對上述三個應用中的每一個重複步驟 2-5，記錄 AppID URI、名稱和應用程式 ID，以便以後用於所有三個應用。
1. 從應用程式清單中打開 API 管理開發人員門戶應用程式，然後選擇 *"常規"* 選項卡（在"常規"下），然後按一下"生成金鑰"以生成 auth 鍵
1. 按一下"保存"後，將金鑰記錄在安全的地方以供以後使用 - 請注意，這個地方是您查看和複製此金鑰的唯一機會。
1. 現在選擇*已發佈的作用域*選項卡（在 API 訪問下）
1. 為函數 API 創建和命名作用域並記錄範圍和填充的完整範圍值，然後按一下"保存"。
   > [!NOTE]
   > Azure AD B2C 作用域實際上是 API 中的許可權，其他應用程式可以通過 API 訪問邊欄選項卡請求訪問其應用程式的許可權，實際上您剛剛為所謂的 API 創建了應用程式許可權。
1. 打開其他兩個應用程式，然後在 API*訪問*選項卡下查看。
1. 授予他們對後端 API 作用域和已存在的預設 API 作用域（"訪問使用者設定檔"）的存取權限。
1. 通過選擇"常規"下的 *"鍵*"選項卡來生成每個鍵，以生成 auth 鍵，並將這些金鑰記錄在安全的地方，以便以後使用。

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>創建"註冊或登錄"使用者流
1. 返回到 Azure AD B2C 刀片的根（或"概述"） 
1. 然後選擇"使用者流（策略）"，然後按一下"新使用者流"
1. 選擇"註冊並登錄"使用者流類型
1. 為策略指定一個名稱，並記錄它供以後使用。
1. 然後在"身份供應商"下，然後選中"使用者 ID 註冊"（這可能說"電子郵件註冊"），然後按一下"確定"。 
1. 在"使用者屬性和聲明"下，按一下"顯示更多..."。然後選擇您希望使用者輸入並在權杖中返回的聲明選項。 檢查至少"顯示名稱"和"電子郵件地址"以收集和返回，然後按一下"確定"，然後按一下"創建"。
1. 選擇您在清單中創建的策略，然後按一下"運行使用者流"按鈕。
1. 此操作將打開運行的使用者流邊欄選項卡，選擇前端應用程式，然後記錄"選擇域"下拉下欄下顯示的b2clogin.com域的位址。
1. 按一下頂部的連結以打開"眾所周知的 openid 配置終結點"，並將authorization_endpoint和token_endpoint值以及鏈路本身的值記錄為眾所周知的 openid 配置終結點。

   > [!NOTE]
   > B2C 策略允許您公開 Azure AD B2C 登錄終結點，以便能夠捕獲不同的資料元件，並以不同的方式登錄使用者。 在這種情況下，我們配置了註冊或登錄終結點，該終結點公開了已知的配置終結點，特別是我們創建的策略是由 p_ 參數在 URL 中標識的。
   > 
   > 完成此操作後，您現在擁有一個功能化的"企業到消費者"標識平臺，該平臺將使用者登錄到多個應用程式。 
   > 如果需要，可以在此處按一下"運行使用者流"按鈕（通過註冊或登錄過程），並瞭解它將在實踐中執行哪些操作，但結束時的重定向步驟將失敗，因為應用尚未部署。

## <a name="build-the-function-api"></a>構建函數 API
1. 在 Azure 門戶中切換回標準 Azure AD 租戶，以便我們可以再次配置訂閱中的專案 
1. 轉到 Azure 門戶的"功能應用"邊欄選項卡，打開空函數應用，然後通過快速入門創建新的門戶內"Webhook + API"功能。
1. 將下面的示例代碼粘貼到 Run.csx 上，將現有代碼粘貼到顯示的現有代碼上。

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
   > 您剛剛粘貼的 c# 腳本函數代碼只需將一行記錄到函數日誌，即可返回帶有一些動態資料的文本"Hello World"（日期和時間）。

3. 從左側邊欄選項卡中選擇"集成"，然後在窗格的右上角選擇"高級編輯器"。
4. 將下面的示例代碼粘貼到現有 json 上。

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

5. 切換回 HttpTrigger1 選項卡，按一下"獲取函數 URL"，然後複製顯示的 URL。

   > [!NOTE]
   > 您剛剛創建的綁定只需告訴函數以匿名 HTTP GET 請求回應您剛剛複製的 URL。 （https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey)現在我們有一個可擴展的無伺服器 HTTPs API，它能夠返回非常簡單的有效負載。
   > 現在，您可以使用上面的 URL 測試從 Web 瀏覽器調用此 API，還可以剝離 URL 的 ？code_機密部分，並證明 Azure 函數將返回 401 錯誤。

## <a name="configure-and-secure-the-function-api"></a>配置並保護功能 API
1. 需要配置功能應用中的兩個額外的區域（Auth 和網路限制）。
1. 首先，讓我們配置身份驗證/授權，因此按一下函數應用的名稱&lt;（Z&gt;函數圖示旁邊）以顯示概覽頁面。
1. 下一步選擇"平臺功能"選項卡，然後選擇"身份驗證/授權"。
1. 打開應用服務身份驗證功能。
1. 在"身份檢查器提供者"下選擇"Azure 活動目錄"，並從管理模式開關中選擇"高級"。
1. 將後端函數 API 的應用程式 ID（從 Azure AD B2C 粘貼到"用戶端 ID"框中）
1. 將註冊或登錄策略中的已知打開 ID 配置終結點粘貼到頒發者 URL 框（我們之前記錄了此配置）。
1. 選取 [確定]。
1. 將請求未經過身份驗證下拉清單時執行的操作設置為"使用 Azure 活動目錄登錄"，然後按一下"保存"。

   > [!NOTE]
   > 現在，函數 API 已部署，如果未提供正確的金鑰，則應引發 401 回應，並在顯示有效請求時返回資料。
   > 通過配置"使用 Azure AD 登錄"選項來處理未經身份驗證的請求，在 EasyAuth 中添加了其他深度防禦安全性。 請注意，這將更改後端功能應用和前端 SPA 之間的未經授權的請求行為，因為 EasyAuth 將發出 302 重定向到 AAD 而不是 401 未授權回應，我們將稍後使用 API 管理更正此問題。
   > 我們仍然沒有應用 IP 安全，如果您有有效的金鑰和 OAuth2 權杖，任何人都可以從任何位置調用它 - 理想情況下，我們希望強制所有請求通過 API 管理來。
   > 如果使用 API 管理使用層，則由於該層沒有專用的靜態 IP，因此無法由 VIP 執行此鎖定，則需要依賴通過共用金鑰功能鍵鎖定 API 呼叫的方法，因此步驟 11-13 將是不可能的。

1. 關閉"身份驗證/授權"邊欄選項卡 
1. 選擇"網路"，然後選擇"訪問限制"
1. 接下來，將允許的功能應用 IP 鎖定到 API 管理實例 VIP。 此 VIP 顯示在門戶的 API 管理 - 概述部分。
1. 如果要繼續與函數門戶交互，並執行以下可選步驟，還應在此處添加您自己的公共 IP 位址或 CIDR 範圍。
1. 在清單中有允許條目後，Azure 會添加隱式拒絕規則以阻止所有其他位址。 

您需要將 CIDR 格式化的位址塊添加到 IP 限制面板中。 當您需要添加單個位址（如 API 管理 VIP）時，您需要將其添加到格式 xx.xx.xx.xx 中。

   > [!NOTE]
   > 現在，除了通過 API 管理或您的位址之外，不應從任何其他位置調用函數 API。
   
## <a name="import-the-function-app-definition"></a>導入函數應用定義
1. 打開*API 管理邊欄選項卡*，然後打開*實例*。
1. 從實例的 API 管理部分選擇 API 刀片。
1. 在"添加新 API"窗格中，選擇"功能應用"，然後從快顯視窗頂部選擇"完整"。
1. 按一下"流覽"，選擇您託管 API 的功能應用，然後按一下"選擇"。
1. 為 API 提供 API 管理內部使用的名稱和說明，並將其添加到"無限制"產品中。
1. 請確保記錄基本 URL 供以後使用，然後按一下"創建"。

## <a name="configure-oauth2-for-api-management"></a>為 API 管理配置 Oauth2

1. 接下來，從"安全"選項卡中選擇 Oauth 2.0 邊欄選項卡，然後按一下"添加"
1. 為添加的 Oauth 終結點提供*顯示名稱*和*說明*的值（這些值將作為 Oauth2 終結點顯示在下一步中）。
1. 您可以在用戶端註冊頁 URL 中輸入任何值，因為不會使用此值。
1. 檢查*隱式 Auth*授予類型，並保留"授權代碼授予"類型選中。
1. 移動到*授權*和*權杖*終結點欄位，然後輸入之前從已知配置 xml 文檔中捕獲的值。
1. 使用 Azure AD B2C 應用註冊中的後端函數 API 用戶端 ID 向下滾動並填充稱為"資源"*的其他正文參數*
1. 選擇"用戶端憑據"，將用戶端 ID 設置為開發人員主控台應用的應用 ID - 如果使用消耗 API 管理模型，請跳過此步驟。
1. 將用戶端金鑰設置為前面錄製的金鑰 - 如果使用消耗 API 管理模型跳過此步驟。
1. 最後，現在記錄 API 管理授予的 auth 代碼redirect_uri，以便以後使用。

## <a name="set-up-oauth2-for-your-api"></a>為 API 設置 Oauth2
1. API 將顯示在門戶左側的"所有 API"部分下，通過按一下它打開 API。
1. 選擇"設置"選項卡。
1. 通過從使用者授權選項按鈕中選擇"Oauth 2.0"來更新設置。
1. 選擇您之前定義的 Oauth 伺服器。
1. 選中"覆蓋範圍"核取方塊，然後輸入您為後端 API 呼叫記錄的範圍。

   > [!NOTE]
   > 現在，我們有一個 API 管理實例，它知道如何從 Azure AD B2C 獲取訪問權杖以授權請求，並瞭解我們的 Oauth2 Azure 活動目錄 B2C 配置。

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>設置**CORS**和**驗證 jwt**策略

> 無論使用 APIM 層，都應遵循以下各節。 

1. 切換回設計選項卡並選擇"所有 API"，然後按一下代碼視圖按鈕以顯示策略編輯器。
1. 編輯入站部分並粘貼下面的 xml，以便它讀如下。

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
1. 編輯 openid 配置 URL 以匹配眾所周知的 Azure AD B2C 終結點，以便註冊或登錄策略。
1. 編輯聲明值以匹配有效的應用程式 ID（也稱為後端 API 應用程式的用戶端 ID）並保存。

   > [!NOTE]
   > 現在，API 管理能夠回應對 JS SPA 應用的跨源請求，它將執行對 JWT 身份驗證權杖的限制、速率限制和預驗證，然後再將請求轉發到函數 API。

   > [!NOTE]
   > 以下部分是可選的，不適用於不支援開發人員門戶的 **"消耗"** 層。
   > 如果您不打算使用開發人員門戶，或者由於使用消耗層而無法使用它，請跳過此步驟並直接跳轉到["構建 JavaScript SPA 以使用 API"。](#build-the-javascript-spa-to-consume-the-api)

## <a name="optional-configure-the-developer-portal"></a>[可選]配置開發人員門戶

1. 打開 Azure AD B2C 邊欄選項卡，然後導航到開發人員門戶的應用程式註冊
1. 將"回復 URL"條目設置為您在之前在 API 管理中配置 redirect_uri 代碼授予時記下的條目。

   現在，OAuth 2.0 使用者授權在 啟用`Echo API`了 ，開發人員主控台在調用 API 之前為使用者獲取訪問權杖。

1. 流覽到開發人員門戶中的任何`Echo API`操作，然後選擇 **"試用"** 以將您帶到開發人員主控台。
1. 請注意 **"授權"** 部分中的新專案，該項與您剛剛添加的授權伺服器相對應。
1. 從授權下拉清單中選擇**授權代碼**，系統將提示您登錄到 Azure AD 租戶。 如果您已使用該帳戶登錄，則可能不會提示您。
1. 成功登錄後，將標頭`Authorization: Bearer`添加到請求中，並在 Base64 中編碼來自 Azure AD B2C 的訪問權杖。 
1. 選擇 **"發送**"，您可以成功調用 API。

   > [!NOTE]
   > 現在，API 管理能夠為開發人員門戶獲取權杖來測試 API，並能夠理解它的定義並在開發門戶中呈現相應的測試頁。

1. 在 API 監管中心的概述邊欄選項卡中，按一下"開發人員門戶"以 API 管理員身份登錄。
1. 在這裡，您和其他選定的 API 消費者可以從主控台測試和調用它們。
1. 選擇"產品"，然後選擇"無限制"，然後選擇我們之前創建的 API，然後按一下"嘗試 IT"
1. 取消隱藏 API 訂閱金鑰，並將其複製到安全的地方以及以後需要的請求 URL。
1. 此外，還可以從"auth auth"下拉清單中選擇"隱式"，您可能需要在此處使用快顯視窗進行身份驗證。
1. 按一下"發送"，如果一切正常，您的功能應用程式應通過 API 管理回復 hello 消息，並帶有 200 OK 消息和一些 JSON。

   > [!NOTE]
   > 恭喜您，您現在讓 Azure AD B2C、API 管理和 Azure 函數協同工作以發佈、保護和使用 API。 您可能已經注意到，API 實際上使用此方法保護了兩次，一次使用 API 管理 Ocp-訂閱金鑰標頭，一次使用授權：承載 JWT。
   > 您是正確的，因為此示例是 JavaScript 單頁應用程式，我們僅對速率限制和計費調用使用 API 管理金鑰。
   > 實際授權和身份驗證由 Azure AD B2C 處理，並封裝在 JWT 中，JWT 兩次驗證，一次由 API 管理驗證，然後由 Azure 函數驗證。

## <a name="build-the-javascript-spa-to-consume-the-api"></a>構建 JavaScript SPA 以使用 API
1. 在 Azure 門戶中打開存儲帳戶邊欄選項卡 
1. 選擇您創建的帳戶，並從"設置"部分選擇"靜態網站"邊欄選項卡（如果您沒有看到"靜態網站"選項，請檢查您創建了 V2 帳戶）。
1. 將靜態 Web 託管功能設置為"已啟用"，並將索引文檔名稱設置為"index.html"，然後按一下"保存"。
1. 記下主終結點的內容，因為此位置是前端網站的託管位置。 

   > [!NOTE]
   > 您可以使用 Azure Blob 存儲 + CDN 重寫或 Azure 應用服務 - 但 Blob 存儲的靜態網站託管功能為我們提供了一個預設容器，用於從 Azure 存儲中提供靜態 Web 內容 /html / js / css，並將為我們推斷為零工作的預設頁面。

## <a name="upload-the-js-spa-sample"></a>上傳 JS SPA 示例
1. 仍在存儲帳戶邊欄選項卡中，從 Blob 服務部分中選擇"Blobs"邊欄選項卡，然後按一下右側窗格中顯示的$web容器。
1. 將以下代碼作為 index.html 保存到電腦上的本地檔，然後將檔索引.html 上載到$web容器。

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

1. 流覽到上一節中較早之前存儲的靜態網站主終結點。

   > [!NOTE]
   > 恭喜您，您剛剛將 JavaScript 單頁應用部署到 Azure 存儲，因為我們還沒有使用 api 的金鑰配置 JS 應用，或者尚未使用 Azure AD B2C 詳細資訊配置 JS 應用 - 如果打開該頁面，該頁面將不起作用。

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>為 Azure AD B2C 配置 JS SPA
1. 現在我們知道一切在哪裡：我們可以配置SPA與適當的API管理API位址和正確的Azure AD B2C應用程式/用戶端ID
1. 返回 Azure 門戶存儲邊欄選項卡並按一下 index.html，然後選擇"編輯 Blob" 
1. 更新 auth 詳細資訊以匹配之前在 B2C 中註冊的前端應用程式，並注意到"b2cScopes"值適用于 API 後端。
1. 可以在 API 管理測試窗格中找到 WEBApi 金鑰和 api URL。
1. 通過返回 API 管理回 API 管理邊欄選項卡，選擇"訂閱"，然後按一下"添加訂閱"然後保存記錄，創建 APIM 訂閱金鑰。 按一下創建行旁邊的橢圓 （...） 將允許您顯示鍵，以便可以複製主鍵。
1. 它應該類似于下面的代碼：-  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. 按一下 [Save] \(儲存)。

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>為 Azure AD B2C 前端應用設置重定向 URI
1. 打開 Azure AD B2C 邊欄選項卡，然後導航到 JavaScript 前端應用程式的應用程式註冊
1. 將重定向 URL 設置為之前設置上面靜態網站主終結點時記下的 URL

   > [!NOTE] 
   > 此配置將導致前端應用程式的用戶端接收具有 Azure AD B2C 適當聲明的訪問權杖。
   > SPA 將能夠將其添加為後端 API 呼叫中的 HTTPs 標頭中的承載權杖。
   > API 管理將在將請求傳遞到接收的 Azure 函數 API 之前，通過訂閱者金鑰預先驗證對終結點的權杖、速率限制調用。
   > SPA 將在瀏覽器中呈現回應。

   > *恭喜您，您已配置 Azure AD B2C、Azure API 管理、Azure 功能、Azure 應用服務授權以完美和諧地工作！*

   > [!NOTE]
   > 現在，我們有一個簡單的應用程式與一個簡單的安全API，讓我們測試它。

## <a name="test-the-client-application"></a>測試用戶端應用程式
1. 打開您從之前創建的存儲帳戶中記下的示例應用 URL
1. 按一下右上角的"登錄"，此按一下將彈出 Azure AD B2C 註冊或登錄設定檔。
1. 螢幕"登錄為"部分的後登錄將從 JWT 填充。
1. 現在按一下"調用 Web Api"，頁面應使用從安全 API 發送回來的值進行更新。

## <a name="and-were-done"></a>我們完成了
可以調整和編輯上述步驟，以便使用 API 管理對 Azure AD B2C 進行許多不同的使用。

## <a name="next-steps"></a>後續步驟
* 深入了解 [Azure Active Directory 和 OAuth2.0](../active-directory/develop/authentication-scenarios.md)。
* 查看更多有關 API 管理的 [視訊](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 。
* 如需其他保護後端服務的方式，請參閱[相互憑證驗證](api-management-howto-mutual-certificates.md)。
* [創建 API 管理服務實例](get-started-create-service-instance.md)。
* [管理第一個 API](import-and-publish.md)。
