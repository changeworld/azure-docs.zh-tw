---
title: Azure 應用程式組態 REST API-版本控制
description: 使用 Azure 應用程式組態 REST API 進行版本設定的參考頁面
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 90d131cdc7c496853f2520951c95b9903d69f8fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424063"
---
# <a name="versioning"></a>版本控制

api 版本：1。0

每個用戶端要求都必須提供明確的 API 版本作為查詢字串參數。 例如：`https://{myconfig}.azconfig.io/kv?api-version=1.0`

`api-version` 以 SemVer (主要. 次要) 格式表示。 不支援範圍或版本協商。

## <a name="error-response"></a>錯誤回應

以下概述當要求的 API 版本無法相符時，伺服器所傳回的可能錯誤回應摘要。

### <a name="api-version-unspecified"></a>未指定 API 版本

當用戶端提出要求但未提供 API 版本時發生。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

### <a name="unsupported-api-version"></a>不支援的 API 版本

當用戶端要求的 API 版本不符合伺服器所支援的任何 API 版本時，就會發生此問題。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="invalid-api-version"></a>不正確 API 版本

當用戶端使用 API 版本提出要求，但其值格式不正確或伺服器無法剖析時，就會發生這種情況。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="ambiguous-api-version"></a>不明確的 API 版本

當用戶端要求對伺服器不明確的 API 版本時發生。 例如，多個不同的值。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
