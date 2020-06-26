---
title: Azure Active Directory SMART on FHIR Proxy
description: 此教學課程說明如何使用 Proxy，透過 Azure API for FHIR 啟用 SMART on FHIR 應用程式。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: mihansen
author: hansenms
ms.date: 04/02/2019
ms.openlocfilehash: f8cfccdb65258ebb6ab75b96abcb551fbc964f2c
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "84870472"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>教學課程：Azure Active Directory SMART on FHIR Proxy

[SMART on FHIR](https://docs.smarthealthit.org/) 是一組開放式規格，可將合作夥伴應用程式與具有 FHIR 介面的 FHIR 伺服器和電子醫療記錄系統整合在一起。 此規格的主要用途之一，是描述應用程式應該如何探索 FHIR 伺服器的驗證端點，以及開始驗證順序。 

驗證是以 OAuth2 為基礎。 但由於 SMART on FHIR 使用的參數命名慣例未直接與 Azure Active Directory (Azure AD) 相容，因此 Azure API for FHIR 具有內建的 Azure AD SMART on FHIR Proxy，可啟用 SMART on FHIR 啟動序列的子集。 具體而言，Proxy 會啟用 [EHR 啟動順序](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence)。

此教學課程說明如何使用 Proxy，透過 Azure API for FHIR 啟用 SMART on FHIR 應用程式。

## <a name="prerequisites"></a>必要條件

- Azure API for FHIR 的執行個體
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>設定 Azure AD 註冊

SMART on FHIR 會要求 `Audience` 的識別碼 URI 必須等於 FHIR 服務的 URI。 Azure API for FHIR 的標準設定會使用 `Audience` 值 `https://azurehealthcareapis.com`。 不過，您也可以設定符合 FHIR 服務之特定 URL 的值 (例如 `https://MYFHIRAPI.azurehealthcareapis.com`)。 使用 SMART on FHIR Proxy 時這是必要的。

您也將需要用戶端應用程式註冊。 SMART on FHIR 應用程式是單頁 JavaScript 應用程式。 因此，您應該遵循設定[公用 Azure AD 用戶端應用程式](register-public-azure-ad-client-app.md)的指示。

完成這些步驟之後，您應該會有：

- RGE 對象設為 `https://MYFHIRAPI.azurehealthcareapis.com` 的 FHIR 伺服器，其中 `MYFHIRAPI` 是您 Azure API for FHIR 執行個體的名稱。
- 公用用戶端應用程式註冊。 記下此用戶端應用程式的應用程式識別碼。

## <a name="enable-the-smart-on-fhir-proxy"></a>啟用 SMART on FHIR Proxy

藉由選取 [SMART on FHIR Proxy]**** 核取方塊，在 Azure API for FHIR 執行個體的 [驗證]**** 設定中啟用 SMART on FHIR Proxy：

![啟用 SMART on FHIR Proxy 的選項](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>啟用 CORS

由於大部分的 SMART on FHIR 應用程式都是單頁 JavaScript 應用程式，因此您必須為 Azure API for FHIR [啟用跨原始來源資源共用 (CORS)](configure-cross-origin-resource-sharing.md)：

![啟用 CORS 的選項](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>設定回覆 URL

SMART on FHIR Proxy 可做為 SMART on FHIR 應用程式和 Azure AD 之間的中繼。 驗證回覆 (驗證碼) 必須前往 SMART on FHIR Proxy，而非應用程式本身。 然後 Proxy 會將回覆轉送至應用程式。 

由於驗證碼的這兩個步驟的轉送，您需要將 Azure AD 用戶端應用程式的回覆 URL (回呼) 設定為 URL，此 URL 是 SMART on FHIR Proxy 回覆 URL 與 SMART on FHIR 應用程式回覆 URL 的組合。 組合的回覆 URL 會具有以下格式：

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

在該回覆中，`aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` 是 SMART on FHIR 應用程式「回覆 URL」的 URL 安全、以 base64 編碼的版本。 針對 SMART on FHIR 應用程式啟動器，當應用程式在本機執行時，回覆 URL 會是 `https://localhost:5001/sampleapp/index.html`。 

您可以使用像這樣的指令碼來產生組合的回覆 URL：

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

將回覆 URL 新增至您稍早為 Azure AD 建立的公用用戶端應用程式：

![為公用用戶端設定的回覆 URL](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>取得測試病患

若要測試 Azure API for FHIR 和 SMART on FHIR Proxy，您必須在資料庫中至少有一個病患。 如果您還沒有與 API 互動，而且您在資料庫中沒有資料，請遵循 [FHIR API Postman 教學課程](access-fhir-postman-tutorial.md)以載入病患。 記下特定病患的識別碼。

## <a name="download-the-smart-on-fhir-app-launcher"></a>下載 SMART on FHIR 應用程式啟動器

開放原始碼的[適用於 Azure 的 FHIR Server 存放庫](https://github.com/Microsoft/fhir-server)包含簡單的 SMART on FHIR 應用程式啟動器，以及 SMART on FHIR 應用程式範例。 在本教學課程中，請在此本機使用此 SMART on FHIR 啟動器來測試設定。

您可以使用下列命令來複製 GitHub 存放庫並前往應用程式：

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

應用程式需要幾個組態設定，您可以在 `appsettings.json` 中進行設定：

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

我們建議您使用 `dotnet user-secrets` 功能：

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

使用此命令來執行應用程式：

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>測試 SMART on FHIR Proxy

啟動 SMART on FHIR 應用程式啟動器之後，您可以將瀏覽器指向 `https://localhost:5001`，您應該會在其中看到下列畫面：

![SMART on FHIR 應用程式啟動器](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

當您輸入 **病患**、**遭遇**或**從業人員**資訊時，您會發現 [啟動內容]**** 已更新。 當您使用 Azure API for FHIR 時，啟動內容只是一份 JSON 文件，其中包含病患、從業人員等等的相關資訊。 此啟動內容是以 base64 編碼，並傳遞至 SMART on FHIR 應用程式做為 `launch` 查詢參數。 根據 SMART on FHIR 規格，此變數對 SMART on FHIR 應用程式而言是不透明的，並會傳遞給識別提供者。 

SMART on FHIR Proxy 會使用此資訊來填入權杖回應中的欄位。 SMART on FHIR 應用程式*可以*使用這些欄位來控制其要求資料的病患，以及如何在螢幕上呈現應用程式。 SMART on FHIR Proxy 支援下欄欄位：

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

這些欄位旨在提供應用程式的指引，但不會傳達任何安全性資訊。 SMART on FHIR 應用程式可以忽略它們。

請注意，SMART on FHIR 應用程式啟動器會更新頁面底部的 [啟動 URL]**** 資訊。 選取 [啟動]**** 以啟動範例應用程式，您應該會看到類似此範例的內容：

![SMART on FHIR 應用程式](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

檢查權杖回應，以查看啟動內容欄位如何傳遞至應用程式。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定 Azure Active Directory SMART on FHIR Proxy。 若要探索使用 SMART on FHIR 應用程式搭配 Azure API for FHIR 和開放原始碼 FHIR Server for Azure，請移至 GitHub 上的 FHIR 伺服器範例存放庫：

>[!div class="nextstepaction"]
>[FHIR 伺服器範例](https://github.com/Microsoft/fhir-server-samples)
