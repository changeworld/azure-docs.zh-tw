---
title: Azure 應用程式組態 REST API 標頭
description: 與 Azure 應用程式組態 REST API 搭配使用之標頭的參考頁面
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932569"
---
# <a name="headers"></a>標題

本文提供與 Azure 應用程式組態 REST API 搭配使用之標頭的參考頁面連結。

## <a name="request-headers"></a>要求標頭

下表說明 Azure 應用程式組態中使用的常見要求標頭。

| 標頭 | 描述 | 範例 |
|--|--|--|
| **授權** | 用來 [驗證](./rest-api-authentication-index.md) 對服務的要求。 請參閱 [14.8 節](https://tools.ietf.org/html/rfc2616#section-14.8) | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **接受** | 通知伺服器用戶端會在 HTTP 回應中接受的媒體類型。 請參閱 [14.1 節](https://tools.ietf.org/html/rfc2616#section-14.1) | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Accept-Datetime** | 要求伺服器將其內容傳回為先前狀態的表示。 此標頭的值是該狀態所要求的日期時間。 請參閱 [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Content-Type** | 包含 HTTP 要求主體中內容的媒體類型。 請參閱 [14.17 節](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **日期** | 發出 HTTP 要求的日期時間。 此標頭會用於 [HMAC 驗證](./rest-api-authentication-hmac.md)。 請參閱 [14.18 節](https://tools.ietf.org/html/rfc2616#section-14.18) | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **主控件** | 指定已發出要求的租使用者。 此標頭會用於 [HMAC 驗證](./rest-api-authentication-hmac.md)。 請參閱 [14.23 節](https://tools.ietf.org/html/rfc2616#section-14.23) | `Host: contoso.azconfig.io` |
| **If-match** | 用來提出 HTTP 要求條件。 只有當目標資源的 ETag 符合此標頭的值時，此要求才會成功。 ' * ' 值符合任何 ETag。 請參閱 [14.24 節](https://tools.ietf.org/html/rfc2616#section-14.24) | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | 用來提出 HTTP 要求條件。 只有當目標資源的 ETag 與此標頭的值不相符時，此要求才會成功。 ' * ' 值符合任何 ETag。 請參閱 [14.26 節](https://tools.ietf.org/html/rfc2616#section-14.26) | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **同步-Token** | 用來在要求順序期間啟用即時一致性。 | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-ms-client-request-id** | 用戶端提供的唯一識別碼，用來追蹤要求的來回行程。 | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-ms-內容-sha256** | HTTP 要求主體的 sha256 摘要。 此標頭會用於 [HMAC 驗證](./rest-api-authentication-hmac.md)。 | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-ms-date** | 如果無法存取日期標頭，則可以設定此標頭，並使用此標頭來取代 `Date` 標頭。 此標頭會用於 [HMAC 驗證](./rest-api-authentication-hmac.md)。 | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-ms-return-client-request-id** | 與標頭一起使用 `x-ms-client-request-id` 。 如果這個標頭的值是 ' true '，則會指示伺服器傳回 `x-ms-client-request-id` 要求標頭的值。 | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>回應標頭

伺服器可能會在其回應中包含下列 HTTP 標頭。

| 標頭 | 描述 | 範例 |
|--|--|--|
| **Content-Type** | 包含 HTTP 回應主體內內容的媒體類型。 請參閱 [14.17 節](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Etag** | 表示指定資源狀態的不透明 token。 可以在條件式作業中使用。 請參閱 [14.19 節](https://tools.ietf.org/html/rfc2616#section-14.19) | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **上次修改時間** | 描述上次修改要求的資源的時間。 格式化為 [HTTP 日期](https://tools.ietf.org/html/rfc2616#section-3.3.1)。 請參閱 [14.29 節](https://tools.ietf.org/html/rfc2616#section-14.29) | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **連結** | 提供與回應相關的資源連結。 此標頭會用於使用 _下一個_ 連結進行分頁。 請參閱 [RFC 5988](https://tools.ietf.org/html/rfc5988) | `Link: </kv?after={token}>; rel="next"` |
| **Memento-Datetime** | 表示回應中包含的內容代表先前的狀態。 此標頭的值是該狀態的日期時間。 請參閱 [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **重試-毫秒** | 提供建議的期間 (（以毫秒為) 單位），讓用戶端在重試失敗的要求之前等候。 | `retry-after-ms: 10` |
| **x-ms-request-id** | 伺服器所產生的唯一識別碼，用來追蹤服務內的要求。 | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-Authenticate** | 用來挑戰用戶端以進行驗證，並提供原因，原因是驗證嘗試失敗的原因。 請參閱 [14.47 節](https://tools.ietf.org/html/rfc2616#section-14.47) | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
