---
title: Azure 前門的 Web 應用程式防火牆自訂規則
description: 瞭解如何使用 Web 應用程式防火牆 （WAF） 自訂規則來保護 Web 應用程式免受惡意攻擊。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475819"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>使用 Azure 前門的 Web 應用程式防火牆的自訂規則

使用前門的 Azure Web 應用程式防火牆 （WAF） 允許您根據定義的條件控制對 Web 應用程式的訪問。 自訂 WAF 規則由優先順序編號、規則類型、匹配條件和操作組成。 有兩種類型的自訂規則：匹配規則和速率限制規則。 匹配規則基於一組匹配條件控制訪問，而速率限制規則根據匹配條件和傳入請求的速率控制訪問。 您可以禁用自訂規則以防止對其進行計算，但仍保留配置。 

## <a name="priority-match-conditions-and-action-types"></a>優先順序、匹配條件和操作類型

您可以使用自訂 WAf 規則來控制訪問，該規則定義優先順序編號、規則類型、匹配條件陣列和操作。 

- **優先順序：** 是描述 WAF 規則評估順序的唯一整數。 優先順序較低的規則在具有較高值的規則之前進行評估。 優先順序編號在所有自訂規則中必須是唯一的。

- **操作：** 定義在 WAF 規則匹配時如何路由請求。 您可以選擇以下操作之一，在請求與自訂規則匹配時應用。

    - *允許*- WAF 將任務轉發到後端，在 WAF 日誌和退出中記錄條目。
    - *塊*- 請求被阻止，WAF 向用戶端發送回應，而不將請求轉發到後端。 WAF 在 WAF 日誌中記錄條目。
    - *日誌*- WAF 在 WAF 日誌中記錄條目，並繼續評估下一個規則。
    - *重定向*- WAF 將請求重定向到指定的 URI，在 WAF 日誌中記錄條目，然後退出。

- **匹配條件：** 定義匹配變數、運算子和匹配值。 每個規則可能包含多個匹配條件。 匹配條件可能基於地理位置、用戶端 IP 位址 （CIDR）、大小或字串匹配。 字串匹配可以針對匹配變數的清單。
  - **匹配變數：**
    - 請求方法
    - QueryString
    - PostArgs
    - 請求庫裡
    - RequestHeader
    - RequestBody
    - Cookie
  - **運算子：**
    - 任何：通常用於定義預設操作（如果沒有匹配規則）。 任何是所有運算子的匹配項。
    - 等於
    - 包含
    - 小於：大小約束
    - 大於：大小約束
    - 小於等於：大小約束
    - 大於ThanOr相等：大小約束
    - BeginsWith
    - EndsWith
    - Regex
  
  - **Regex**不支援以下操作： 
    - 回引用和捕獲子運算式
    - 任意零寬度斷言
    - 副程式引用和遞迴模式
    - 條件模式
    - 回溯控制動詞
    - \C 單位元組指令
    - \R newline 匹配指令
    - 匹配重置指令的 #K 開始
    - 標注和嵌入式代碼
    - 原子分組和佔有限定詞

  - **否定 [可選]：** 如果條件的結果應否定，則可以將*否定*條件設置為 true。
      
  - **轉換 [可選]：** 在嘗試匹配之前要執行的字串清單。 這些轉換可以是以下轉換：
     - 大寫 
     - 小寫
     - Trim
     - 刪除 Nulls
     - UrlDecode
     - UrlEncode
     
   - **匹配值：** 支援的 HTTP 要求方法值包括：
     - GET
     - POST
     - PUT
     - HEAD
     - 刪除
     - LOCK
     - UNLOCK
     - 設定檔
     - OPTIONS
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - 複製
     - MOVE

## <a name="examples"></a>範例

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>基於 HTTP 參數的 WAF 自訂規則示例

下面是一個示例，該示例顯示了具有兩個匹配條件的自訂規則的配置。 請求來自引用者定義的指定網站，查詢字串不包含"密碼"。

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
阻止"PUT"方法的示例配置如下所示：

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>大小約束

可以生成自訂規則，該規則指定傳入請求的一部分的大小約束。 例如，下面的規則阻止超過 100 個字元的 Url。

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>後續步驟
- [使用 Azure PowerShell 配置 Web 應用程式防火牆策略](waf-front-door-custom-rules-powershell.md) 
- 瞭解[前門的 Web 應用程式防火牆](afds-overview.md)
- 了解如何[建立 Front Door](../../frontdoor/quickstart-create-front-door.md)。

