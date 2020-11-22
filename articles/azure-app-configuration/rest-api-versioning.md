---
title: Azure 應用程式組態 REST API-版本控制
description: 使用 Azure 應用程式組態的版本設定參考頁面 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3a7f50b26d59501d2be3a0147fe89919819b50e6
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2020
ms.locfileid: "95246362"
---
# <a name="versioning"></a>版本控制

每個用戶端要求都必須提供明確的 API 版本作為查詢字串參數。 例如：`https://{myconfig}.azconfig.io/kv?api-version=1.0`。

`api-version` 以 SemVer (主要. 次要) 格式表示。 不支援範圍或版本協商。

本文適用于 API 版本1.0。

以下概述當要求的 API 版本無法相符時，伺服器所傳回的可能錯誤回應摘要。

## <a name="api-version-unspecified"></a>未指定 API 版本

當用戶端提出要求但未提供 API 版本時，就會發生此錯誤。

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

## <a name="unsupported-api-version"></a>不支援的 API 版本

當用戶端要求的 API 版本與伺服器所支援的任何 API 版本不相符時，就會發生此錯誤。

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

## <a name="invalid-api-version"></a>不正確 API 版本

當用戶端使用 API 版本提出要求，但值格式不正確或無法由伺服器剖析時，就會發生此錯誤。

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

## <a name="ambiguous-api-version"></a>不明確的 API 版本

當用戶端要求對 (伺服器不明確的 API 版本（例如，多個不同的值) ）時，就會發生此錯誤。

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
