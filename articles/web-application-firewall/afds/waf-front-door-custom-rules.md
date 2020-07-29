---
title: 適用于 Azure Front 的 Web 應用程式防火牆自訂規則
description: 瞭解如何使用 Web 應用程式防火牆（WAF）的自訂規則，保護 web 應用程式免于遭受惡意攻擊。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79475819"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>使用 Azure Front 的 Web 應用程式防火牆自訂規則

具有 Front 的 Azure Web 應用程式防火牆（WAF）可讓您根據所定義的條件，控制對 Web 應用程式的存取。 自訂 WAF 規則包含優先順序號碼、規則類型、比對條件和動作。 有兩種類型的自訂規則：符合規則和速率限制規則。 比對規則會根據一組比對條件來控制存取，而速率限制規則會根據比對條件和連入要求的速率來控制存取。 您可以停用自訂規則，以防止它被評估，但仍保留設定。 

## <a name="priority-match-conditions-and-action-types"></a>優先順序、相符條件和動作類型

您可以使用自訂 WAf 規則來控制存取，以定義優先順序編號、規則類型、比對條件陣列和動作。 

- **優先順序：** 是唯一的整數，描述 WAF 規則的評估順序。 具有較低優先順序值的規則會在具有較高值的規則之前進行評估。 優先順序數位在所有自訂規則中都必須是唯一的。

- **動作：** 定義若符合 WAF 規則，如何路由傳送要求。 當要求符合自訂規則時，您可以選擇下列其中一個要套用的動作。

    - *Allow* WAF 會將其轉送至後端，並在 WAF 記錄中記錄專案並結束。
    - *封鎖要求*會遭到封鎖，WAF 會將回應傳送至用戶端，而不會將要求轉送至後端。 WAF 會在 WAF 記錄中記錄一個專案。
    - *記錄*檔 WAF 會在 WAF 記錄中記錄一個專案，並繼續評估下一個規則。
    - WAF 會將要求*重新導向至*指定的 URI，並在 WAF 記錄中記錄一個專案，然後結束。

- 比對**條件：** 定義符合變數、運算子和比對值。 每個規則可能包含多個符合條件。 比對條件可以根據地理位置、用戶端 IP 位址（CIDR）、大小或字串比對。 字串比對可用於符合的變數清單。
  - **Match 變數：**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody
    - Cookie
  - **操作**
    - Any：如果沒有符合規則，通常會用來定義預設動作。 Any 是 match all 運算子。
    - 等於
    - 包含
    - LessThan： size 條件約束
    - GreaterThan： size 條件約束
    - LessThanOrEqual： size 條件約束
    - GreaterThanOrEqual： size 條件約束
    - BeginsWith
    - EndsWith
    - Regex
  
  - **Regex**不支援下列作業： 
    - 反向參考和捕獲子運算式
    - 任意零寬度的判斷提示
    - 副程式參考和遞迴模式
    - 條件式模式
    - 回溯控制項動詞
    - \C 單一位元組指示詞
    - \R 分行符號 match 指示詞
    - \K 開頭的 match 重設指示詞
    - 標注和內嵌程式碼
    - 不可部分完成群組和所有格數量詞

  - **否定 [選擇性]：** 如果條件的結果應該是否定的，您可以將 [*否定*條件] 設定為 [true]。
      
  - **轉換 [選用]：** 在嘗試進行比對之前，具有轉換名稱的字串清單。 這些可以是下列轉換：
     - 大寫 
     - 小寫
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **符合值：** 支援的 HTTP 要求方法值包括：
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

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>根據 HTTP 參數 WAF 自訂規則範例

以下範例顯示具有兩個比對條件之自訂規則的設定。 要求是來自指定的網站（如查閱者所定義），而查詢字串不包含 "password"。

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
封鎖 "PUT" 方法的範例設定如下所示：

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

### <a name="size-constraint"></a>大小條件約束

您可以建立自訂規則，針對傳入要求的一部分指定大小條件約束。 例如，下列規則會封鎖長度超過100個字元的 Url。

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
- [使用 Azure PowerShell 設定 Web 應用程式防火牆原則](waf-front-door-custom-rules-powershell.md) 
- 瞭解[具有 Front 的 Web 應用程式防火牆](afds-overview.md)
- 了解如何[建立 Front Door](../../frontdoor/quickstart-create-front-door.md)。

