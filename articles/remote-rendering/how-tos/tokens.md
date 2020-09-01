---
title: 取得服務存取權杖
description: 說明如何建立權杖以存取 ARR REST Api
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e8e2f3f9dd49693faa26eaaab309fcad58f6f9f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076152"
---
# <a name="get-service-access-tokens"></a>取得服務存取權杖

對 ARR REST Api 的存取權只會授與授權的使用者。 若要證明您的授權，您必須連同 REST 要求一起傳送 *存取權杖* 。 這些權杖是由 *安全權杖服務* 所發出 (STS) 在 exchange 中的帳戶金鑰。 權杖的 **存留期為24小時** ，因此可發出給使用者，而不需提供服務的完整存取權。

本文說明如何建立這類存取權杖。

## <a name="prerequisites"></a>必要條件

如果您還沒有[ARR 帳戶](create-an-account.md)，請建立一個。

## <a name="token-service-rest-api"></a>權杖服務 REST API

為了建立存取權杖， *安全權杖服務* 會提供單一 REST API。 ARR STS 服務的 URL 為 HTTPs： \/ /sts.mixedreality.azure.com。

### <a name="get-token-request"></a>' 取得權杖 ' 要求

| URI | 方法 |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| 頁首 | 值 |
|--------|:------|
| 授權 | 「持有人 **accountId**：**accountKey**」 |

將 *accountId* 和 *accountKey* 取代為您的個別資料。

### <a name="get-token-response"></a>「取得權杖」回應

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 200 | AccessToken：字串 | 成功 |

| 標頭 | 用途 |
|--------|:------|
| MS-CV | 這個值可以用來追蹤服務內的呼叫 |

## <a name="getting-a-token-using-powershell"></a>使用 PowerShell 取得權杖

下列 PowerShell 程式碼示範如何將必要的 REST 要求傳送至 STS。 然後，它會將權杖列印到 PowerShell 提示字元。

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

腳本只會將權杖列印到輸出中，您可以從此處複製 & 貼上。 針對真實的專案，您應該將此程式自動化。

## <a name="next-steps"></a>後續步驟

* [PowerShell 指令碼範例](../samples/powershell-example-scripts.md)
* [Azure 前端 Api](../how-tos/frontend-apis.md)
* [會話管理 REST API](../how-tos/session-rest-api.md)
