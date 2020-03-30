---
title: 生成安全權杖以訪問 IoT 隨插即用預覽存儲庫 |微軟文檔
description: 生成共用訪問簽名權杖，以便以程式設計方式訪問 IoT 隨插即用預覽模型存儲庫時使用。
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159195"
---
# <a name="generate-sas-token"></a>產生 SAS Token

此操作指南演示如何以程式設計方式生成共用訪問簽名 （SAS） 權杖，以便與 IoT 隨插即用模型存儲庫 API 一起使用。

## <a name="python"></a>Python

以下程式碼片段演示如何使用 Python 生成 SAS 權杖：

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

以下程式碼片段演示如何使用 C\#生成 SAS 權杖：

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

生成 SAS 權杖後，可以使用它發出 HTTP POST 請求。 例如：

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

如果向用戶端提供 SAS 權杖，則用戶端沒有資源的主金鑰，並且無法反轉雜湊以獲取它。 SAS 權杖允許您控制用戶端可以訪問的內容以及多長時間。 更改策略中的主鍵時，從策略創建的任何 SAS 權杖將失效。

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解了生成安全權杖以用於訪問模型 IoT 隨插即用預覽模型存儲庫，建議的下一步是在[IoT 隨插即用預覽建模開發人員指南](concepts-developer-guide.md)中瞭解更多資訊。
