---
title: 取得服務存取權杖
description: 描述如何建立存取 ARR REST API 的權杖
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: fd510f90887353d7486908ee076d5308db72c59d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687073"
---
# <a name="get-service-access-tokens"></a>取得服務存取權杖

僅授權使用者才能存取 ARR REST API。 要證明您的授權,您必須發送*訪問權杖*以及 REST 請求。 這些權杖由*安全權杖服務*(STS) 頒發,以換取帳戶密鑰。 令牌**的存留期為 24 小時**,因此可以頒發給使用者,而無需授予他們對服務的完全訪問許可權。

本文介紹如何創建此類訪問權杖。

## <a name="prerequisites"></a>Prerequisites

[建立 ARR 帳戶](create-an-account.md),如果您還沒有。

## <a name="token-service-rest-api"></a>權杖服務 REST API

要建立存取權杖,*安全權碼服務*提供單個 REST API。 ARR STS 服務的 URL\/是 HRSTS 服務的網址是 HR: /sts.mixedreality.azure.com。

### <a name="get-token-request"></a>「取得權杖」要求

| URI | 方法 |
|-----------|:-----------|
| /帳號/**帳戶 Id**/權杖 | GET |

| 頁首 | 值 |
|--------|:------|
| 授權 | "持有人**帳號代碼**:**帳戶金鑰**" |

將*帳戶Id*和*帳戶金鑰*替換為相應的資料。

### <a name="get-token-response"></a>"取得權杖"回應

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 200 | 存取權杖:字串 | Success |

| 頁首 | 目的 |
|--------|:------|
| MS-CV | 此值可用於追蹤服務中的呼叫 |

## <a name="getting-a-token-using-powershell"></a>使用 PowerShell 取得權杖

下面的 PowerShell 程式碼展示如何向 STS 發送必要的 REST 請求。 然後,它將令牌列印到 PowerShell 提示符。

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

腳本只需將令牌列印到輸出,即可複製&粘貼它。 對於實際專案,您應該自動執行此過程。

## <a name="next-steps"></a>後續步驟

* [PowerShell 指令碼範例](../samples/powershell-example-scripts.md)
* [Azure 前端 API](../how-tos/frontend-apis.md)
* [工作階段管理 REST API](../how-tos/session-rest-api.md)
