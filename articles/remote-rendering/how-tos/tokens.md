---
title: 取得服務存取權杖
description: 描述如何建立權杖以存取 ARR REST Api
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: fd510f90887353d7486908ee076d5308db72c59d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687073"
---
# <a name="get-service-access-tokens"></a>取得服務存取權杖

只有授權的使用者才能存取 ARR REST Api。 若要證明您的授權，您必須連同 REST 要求一起傳送*存取權杖*。 這些權杖是由 exchange 中的*安全權杖服務*（STS）所發行，以取得帳戶金鑰。 權杖的**存留期為24小時**，因此可以發行給使用者，而不需要為他們提供服務的完整存取權。

本文說明如何建立這類存取權杖。

## <a name="prerequisites"></a>必要條件

[建立 ARR 帳戶](create-an-account.md)（如果還沒有的話）。

## <a name="token-service-rest-api"></a>權杖服務 REST API

若要建立存取權杖，*安全權杖服務*會提供單一 REST API。 ARR STS 服務的 URL 為 HTTPs： \/ /sts.mixedreality.azure.com。

### <a name="get-token-request"></a>「取得權杖」要求

| URI | 方法 |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| 頁首 | 值 |
|--------|:------|
| 授權 | 「持有人**accountId**：**accountKey**」 |

將*accountId*和*accountKey*取代為您的個別資料。

### <a name="get-token-response"></a>「取得權杖」回應

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 200 | AccessToken：字串 | 成功 |

| Header | 目的 |
|--------|:------|
| MS-CV | 此值可以用來追蹤服務內的呼叫 |

## <a name="getting-a-token-using-powershell"></a>使用 PowerShell 取得權杖

下列 PowerShell 程式碼示範如何將必要的 REST 要求傳送至 STS。 然後，它會將權杖列印至 PowerShell 提示字元。

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

腳本只會列印輸出的權杖，您可以從中複製 & 貼上。 對於實際的專案，您應該將此程式自動化。

## <a name="next-steps"></a>後續步驟

* [PowerShell 指令碼範例](../samples/powershell-example-scripts.md)
* [Azure 前端 Api](../how-tos/frontend-apis.md)
* [會話管理 REST API](../how-tos/session-rest-api.md)
