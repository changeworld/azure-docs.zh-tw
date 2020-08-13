---
title: 驗證、要求和回應
description: 瞭解 Azure Key Vault 如何使用 JSON 格式的要求和回應，以及使用金鑰保存庫所需的驗證。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 2b4c8ad666efa32d98e78a0bc2544d0f8851be5e
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191789"
---
# <a name="authentication-requests-and-responses"></a>驗證、要求和回應

Azure Key Vault 支援 JSON 格式化要求和回應。 搭配使用 HTTPS 與一些 URL 參數以及 JSON 編碼要求和回應本文，以將對 Azure Key Vault 的要求導向至有效的 Azure Key Vault URL。

本主題涵蓋 Azure Key Vault 服務的特性。 如需使用 Azure REST 介面的一般資訊 (包括驗證/授權，以及如何取得存取權杖)，請參閱 [Azure REST API 參考](https://docs.microsoft.com/rest/api/azure)。

## <a name="request-url"></a>要求 URL  
 金鑰管理作業使用 HTTP DELETE、GET、PATCH、PUT 和 HTTP POST，而對現有金鑰物件的密碼編譯作業則使用 HTTP POST。 無法支援特定 HTTP 指令動詞的用戶端可能也會使用 HTTP POST，方法是使用 X-HTTP-REQUEST 標頭指定所需的指令動詞；在使用 HTTP POST 時 (例如，使用 POST 而非 DELETE 時)，通常不需要本文的要求應該會包含空本文。  

 若要使用 Azure Key Vault 中的物件，則範例 URL 如下：  

- 若要在 Key Vault 中 CREATE (建立) 稱為 TESTKEY 的金鑰，請使用：`PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- 若要將稱為 IMPORTEDKEY 的金鑰 IMPORT (匯入) 至 Key Vault，請使用：`POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- 若要 GET (取得) Key Vault 中稱為 MYSECRET 的祕密，請使用：`GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- 若要使用 Key Vault 中稱為 TESTKEY 的金鑰來 SIGN (簽署) 摘要，請使用：`POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  對 Key Vault 之要求的授權單位一律如下：`https://{keyvault-name}.vault.azure.net/`  

  金鑰一律儲存在 /keys 路徑下方，祕密則一律儲存在 /secrets 路徑下方。  

## <a name="api-version"></a>API 版本  
 儘管這些用戶端並非都能使用所有功能，但是 Azure Key Vault 服務支援通訊協定版本控制來提供與下層用戶端的相容性。 用戶端必須使用 `api-version` 查詢字串參數，指定其支援且沒有預設值之通訊協定的版本。  

 Azure Key Vault 通訊協定版本遵循使用 {YYYY}.{MM}.{DD} 格式的日期編號方式。  

## <a name="request-body"></a>要求本文  
 根據 HTTP 規格，GET 作業不得有要求本文，而 POST 和 PUT 作業必須有要求本文。 在 HTTP 中，DELETE 作業中的本文是選用項目。  

 除非作業描述中另有說明，否則要求本文內容類型必須是 application/json，而且必須包含符合內容類型的序列化 JSON 物件。  

 除非作業描述中另有說明，否則 Accept 要求標頭必須包含 application/json 媒體類型。  

## <a name="response-body"></a>回應本文  
 除非作業描述中另有說明，否則成功和失敗作業的回應本文內容類型將會是 application/json，並且包含詳細錯誤資訊。  

## <a name="using-http-post"></a>使用 HTTP POST  
 某些用戶端可能無法使用特定 HTTP 指令動詞 (例如 PATCH 或 DELETE)。 Azure Key Vault 支援 HTTP POST 作為這些用戶端的替代方法，但前提是用戶端也包含 "X-HTTP-METHOD" 標頭來指定原始 HTTP 指令動詞。 針對本文件中定義的每個 API，已加註 HTTP POST 支援。  

## <a name="error-responses"></a>錯誤回應  
 錯誤處理將會使用 HTTP 狀態碼。 一般結果為：  

- 2xx – 成功：用於一般作業。 回應本文將會包含預期的結果  

- 3xx – 重新導向：可能會傳回 304「未修改」，以滿足條件式 GET。 未來可能會使用其他 3xx 代碼指出 DNS 和路徑變更。  

- 4xx –用戶端錯誤：用於不正確的要求、遺漏金鑰、語法錯誤、無效參數、驗證錯誤等等。回應主體會包含詳細的錯誤說明。  

- 5xx – 伺服器錯誤：用於內部伺服器錯誤。 回應本文將會包含摘要錯誤資訊。  

  系統設計成受到 Proxy 或防火牆保護。 因此，用戶端可能會收到其他錯誤碼。  

  Azure Key Vault 也會在發生問題時於回應本文中傳回錯誤資訊。 回應本文的格式為 JSON，並採用下列格式：  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>驗證  
 必須驗證所有對 Azure Key Vault 的要求。 Azure Key Vault 支援可使用 OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)] 取得的 Azure Active Directory 存取權杖。 
 
 如需註冊應用程式並使用 Azure Key Vault 驗證的詳細資訊，請參閱[使用 Azure AD 註冊用戶端應用程式](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad)。
 
 存取權杖必須使用 HTTP Authorization 標頭傳送給服務：  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 未提供存取權杖時，或服務不接受權杖時，會將 HTTP 401 錯誤傳回給用戶端，並且其中會包含 WWW-Authenticate 標頭，例如：  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 WWW-Authenticate 標頭上的參數如下：  

-   authorization：可能用來取得要求存取權杖之 OAuth2 授權服務的位址。  

-   資源： `https://vault.azure.net` 要在授權要求中使用的資源 () 名稱。  

