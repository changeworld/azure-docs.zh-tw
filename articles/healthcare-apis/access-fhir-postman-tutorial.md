---
title: Azure 中的 Postman FHIR 伺服器 – Azure API for FHIR
description: 在本教學課程中，我們將逐步解說使用 Postman 存取 FHIR 伺服器所需的步驟。 Postman 有助於對存取 API 的應用程式進行偵錯。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 02/07/2019
ms.openlocfilehash: b0acfbf9636c2ad1df7df3197a0a1a83ec91b5c3
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747335"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>使用 Postman 存取 Azure API for FHIR

用戶端應用程式會透過 [REST API](https://www.hl7.org/fhir/http.html) 來存取 FHIR API。 當您建置應用程式時，您也可以直接與 FHIR 伺服器互動，例如，進行偵錯。 在本教學課程中，我們將逐步解說使用 [Postman](https://www.getpostman.com/) 存取 FHIR 伺服器所需的步驟。 Postman 是在建置可存取 API 的應用程式時，通常用於偵錯的工具。

## <a name="prerequisites"></a>必要條件

- Azure 中的 FHIR 端點。 您可以使用受控 Azure API for FHIR 或適用於 Azure 的開放原始碼 FHIR 伺服器進行設定。 使用 [Azure 入口網站](fhir-paas-portal-quickstart.md)、[PowerShell](fhir-paas-powershell-quickstart.md)或 [Azure CLI](fhir-paas-cli-quickstart.md) 來設定受控 Azure API for FHIR。
- 您將用來存取 FHIR 服務的[用戶端應用程式](register-confidential-azure-ad-client-app.md)
- 已安裝 Postman。 您可以從 [https://www.getpostman.com](https://www.getpostman.com) 取得。

## <a name="fhir-server-and-authentication-details"></a>FHIR 伺服器和驗證詳細資料

為了使用 Postman，需要下列詳細資料：

- 您的 FHIR 伺服器 URL，例如 `https://MYACCOUNT.azurehealthcareapis.com`
- FHIR 伺服器的識別提供者 `Authority`，例如 `https://login.microsoftonline.com/{TENANT-ID}`
- 已設定的 `audience`。 這通常是 FHIR 伺服器的 URL，例如 `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` 或只是 `https://azurehealthcareapis.com` 。
- 您將用來存取 FHIR 服務的[用戶端應用程式](register-confidential-azure-ad-client-app.md) 的 `client_id` (或應用程式識別碼)。
- 用戶端應用程式的 `client_secret` (或應用程式密碼)。

最後，您應該檢查 `https://www.getpostman.com/oauth2/callback` 是否為用戶端應用程式的已註冊回覆 URL。

## <a name="connect-to-fhir-server"></a>連線到 FHIR 伺服器

使用 Postman，對 `https://fhir-server-url/metadata` 提出 `GET` 要求：

![Postman 中繼資料功能陳述式](media/tutorial-postman/postman-metadata.png)

Azure API for FHIR 的中繼資料 URL 為 `https://MYACCOUNT.azurehealthcareapis.com/metadata`。 在此範例中，FHIR 伺服器 URL 為 `https://fhirdocsmsft.azurewebsites.net`，而且可在 `https://fhirdocsmsft.azurewebsites.net/metadata` 取得伺服器的功能陳述式。 該端點不需驗證即可存取。

如果您嘗試存取受限制的資源，您應該會收到「驗證失敗」回應：

![驗證失敗](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>取得存取權杖

若要取得有效的存取權杖，請選取 [授權]，然後挑選類型 "OAuth 2.0"：

![設定 OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

點擊 [取得新的存取權杖]，隨即出現一個對話方塊：

![要求新的存取權杖](media/tutorial-postman/postman-request-token.png)

您需要一些詳細資料：

| 欄位                 | 範例值                                                                                                   | 註解                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| 權杖名稱            | MYTOKEN                                                                                                         | 您選擇的名稱          |
| 授與類型            | 授權碼                                                                                              |                            |
| 回呼 URL          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| 驗證 URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` 是 Azure API for FHIR 的 `https://MYACCOUNT.azurehealthcareapis.com` |
| 存取權杖 URL      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| 用戶端識別碼             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | 應用程式識別碼             |
| 用戶端密碼         | `XXXXXXXX`                                                                                                        | 秘密用戶端金鑰          |
| 影響範圍 | `<Leave Blank>` |
| State                 | `1234`                                                                                                            |                            |
| 用戶端驗證 | 在本文中傳送用戶端認證                                                                                 |                 

點擊 [要求權杖]，系統會引導您完成 Azure Active Directory 驗證流程並將權杖傳回給 Postman。 如果您遇到問題，請開啟 Postman 主控台 (從 [檢視] -> [顯示 Postman 主控台] 功能表項目)。

在傳回的權杖畫面上向下滾動，然後點擊 [使用權杖]：

![使用權杖](media/tutorial-postman/postman-use-token.png)

權杖現在應該會填入 [存取權杖] 欄位中，而且您可從 [可用權杖] 中選取權杖。 如果您再次「傳送」以重複 `Patient` 資源搜尋，您應會取得狀態 `200 OK`：

![200 確定](media/tutorial-postman/postman-200-OK.png)

在此情況下，資料庫中沒有病患，而且搜尋是空的。

如果您使用 [https://jwt.ms](https://jwt.ms) 之類的工具檢查存取權杖，應該會看到如下的內容：

```jsonc
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

在疑難排解的情況下，驗證您是否有正確的對象 (`aud` 宣告) 是不錯的開端。 如果您的權杖來自正確的簽發者 (`iss` 宣告) 且具有正確的對象 (`aud` 宣告)，但您仍無法存取 FHIR API，則使用者或服務主體 (`oid` 宣告) 可能沒有 FHIR 資料平面的存取權。 建議您[使用 Azure 角色型存取控制 (Azure RBAC)](configure-azure-rbac.md)，將資料平面角色指派給使用者。 如果您針對資料平面使用外部次要 Azure Active Directory 租用戶，則需要[設定本機 RBAC 指派](configure-local-rbac.md)。

您也可以[使用 Azure CLI 取得 Azure API for FHIR 的權杖](get-healthcare-apis-access-token-cli.md)。 如果您使用透過 Azure CLI 取得的權杖，則應該使用授權類型「持有人權杖」並直接貼入權杖。

## <a name="inserting-a-patient"></a>插入病患

您現在具有有效的存取權杖。 您可以插入新的病患。 切換至 "POST" 方法並在要求的本文中新增下列 JSON 文件：

[!code-json[](samples/sample-patient.json)]

點擊 [傳送]，您應該會看到已成功建立病患：

![顯示已成功建立病患的螢幕擷取畫面。](media/tutorial-postman/postman-patient-created.png)

如果您重複執行病患搜尋，現在應該會看到病患記錄：

![已建立病患](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Postman 存取 FHIR API。 深入了解我們支援的功能一節中支援的 API 功能。
 
>[!div class="nextstepaction"]
>[支援的功能](fhir-features-supported.md)
