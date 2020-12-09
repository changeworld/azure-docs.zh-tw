---
title: Azure Active Directory REST API-使用 Postman 進行測試
description: 使用 Postman 來測試 Azure 應用程式組態 REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 0b4444b049d181c2f66f8b02a5202dd17a3f4b6b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932484"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>使用 Postman 測試 Azure 應用程式組態 REST API

若要使用 [Postman](https://www.getpostman.com/)測試 REST API，您必須在要求中包含 [驗證](./rest-api-authentication-hmac.md) 所需的 HTTP 標頭。 以下說明如何設定 Postman 來測試 REST API，並自動產生驗證標頭：

1. 建立新的 [要求](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/)
1. `signRequest`從[JavaScript 驗證範例](./rest-api-authentication-hmac.md#javascript)將函式新增至要求的要求[前腳本](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/)
1. 將下列程式碼新增至預先要求腳本的結尾。 更新 TODO 批註所指示的存取金鑰

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. 傳送要求
