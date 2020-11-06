---
title: Azure Active Directory REST API-使用 Fiddler 進行測試
description: 使用 Fiddler 來測試 Azure 應用程式組態 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3766567fe58e8d2eb86556d3defa7a85efd9b2fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423927"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>使用 Fiddler 測試 Azure 應用程式組態 REST API

若要使用 [Fiddler](https://www.telerik.com/fiddler)測試 REST API，您必須在要求中包含 [驗證](./rest-api-authentication-hmac.md) 所需的 HTTP 標頭。 以下說明如何設定 Fiddler 來測試 REST API，並自動產生驗證標頭：

1. 確定 TLS 1.2 是允許的通訊協定：
    1. 移至 [ **工具**  >  **選項**  >  **HTTPS** ) ]。
    1. 確定已核取 [ **解密 HTTPS 流量** ]。
    1. 在通訊協定清單中，新增 **tls 1.2** （如果不存在）。
1. 開啟 **Fiddler 腳本編輯器** ，或在 Fiddler 中按下 **Ctrl + R**
1. 在函式之前的類別內新增下列程式碼 `Handlers` `OnBeforeRequest`

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. 在函式的結尾新增下列程式碼 `OnBeforeRequest` 。 更新 TODO 批註所指示的存取金鑰。

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. 使用 [Fiddler 的編輯器](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) 來產生和傳送要求
