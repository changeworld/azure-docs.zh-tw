---
title: 產生安全性權杖以存取 IoT 隨插即用預覽存放庫 |Microsoft Docs
description: 當您以程式設計方式存取 IoT 隨插即用預覽模型存放庫時，產生要使用的共用存取簽章權杖。
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159195"
---
# <a name="generate-sas-token"></a>產生 SAS Token

本操作指南會示範如何以程式設計方式產生共用存取簽章（SAS）權杖，以搭配 IoT 隨插即用預覽模型存放庫 Api 使用。

## <a name="python"></a>Python

下列程式碼片段說明如何使用 Python 產生 SAS 權杖：

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

下列程式碼片段說明如何使用 C 來產生 SAS 權杖 \# ：

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>使用 SAS 權杖

產生 SAS 權杖之後，您可以使用它來提出 HTTP POST 要求。 例如：

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

如果您為用戶端提供 SAS 權杖，用戶端就不會擁有該資源的主要金鑰，而且無法反轉雜湊來取得它。 SAS 權杖可讓您控制用戶端可以存取的內容，以及時間長度。 當您變更原則中的主鍵時，任何從該金鑰建立的 SAS 權杖都會失效。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何產生用來存取模型 IoT 隨插即用預覽模型存放庫的安全性權杖，建議的下一個步驟是深入瞭解[iot 隨插即用預覽模型開發人員指南](concepts-developer-guide.md)。
