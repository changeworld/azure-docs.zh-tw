---
title: Azure Front Door 的 Web 應用程式防火牆自訂規則
description: 瞭解如何使用 Web 應用程式防火牆 (WAF) 自訂規則，以保護您的 Web 應用程式免于遭受惡意攻擊。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "79475819"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>具有 Azure Front Door 的 Web 應用程式防火牆自訂規則

使用 Front Door 的 Azure Web 應用程式防火牆 (WAF) 可讓您根據您定義的條件來控制 Web 應用程式的存取權。 自訂 WAF 規則是由優先順序數位、規則類型、比對條件和動作所組成。 有兩種類型的自訂規則：符合規則和速率限制規則。 比對規則會根據一組比對條件來控制存取，而速率限制規則則會根據比對條件和連入要求的速率來控制存取權。 您可以停用自訂規則，以防止它進行評估，但仍繼續進行設定。 

## <a name="priority-match-conditions-and-action-types"></a>優先權、比對條件和動作類型

您可以使用自訂 WAf 規則來控制存取，以定義優先順序數位、規則類型、符合條件的陣列，以及動作。 

- **Priority：** 是唯一的整數，描述 WAF 規則的評估順序。 具有較低優先順序值的規則會在值較高的規則之前進行評估。 所有自訂規則中的優先順序數位都必須是唯一的。

- **動作：** 定義在 WAF 規則相符時如何路由傳送要求。 當要求符合自訂規則時，您可以選擇下列其中一個要套用的動作。

    - *Allow* WAF 會將嘗試轉送到後端，並在 WAF 記錄中記錄專案並結束。
    - *封鎖要求* 遭到封鎖，WAF 會將回應傳送給用戶端，而不會將要求轉送至後端。 WAF 會在 WAF 記錄中記錄專案。
    - *記錄* WAF 會記錄 WAF 記錄中的專案，並繼續評估下一個規則。
    - 重新*導向*-WAF 會將要求重新導向至指定的 URI、記錄 WAF 記錄中的專案，然後結束。

- 比對**條件：** 定義比對變數、運算子和符合值。 每個規則可能包含多個比對條件。 比對條件可根據地理位置、用戶端 IP 位址 (CIDR) 、大小或字串相符。 字串比對可以與相符變數清單相符。
  - **符合變數：**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody
    - Cookie
  - **運算元：**
    - 任何：通常用來定義沒有符合規則時的預設動作。 Any 是 match all 運算子。
    - 等於
    - 包含
    - LessThan： size 條件約束
    - GreaterThan： size 條件約束
    - LessThanOrEqual： size 條件約束
    - GreaterThanOrEqual： size 條件約束
    - BeginsWith
    - EndsWith
    - Regex
  
  - **Regex** 不支援下列作業： 
    - 反向參考和捕獲子運算式
    - 任意零寬度的判斷提示
    - 副程式參考和遞迴模式
    - 條件式模式
    - 回溯控制項動詞
    - \C 單一位元組指示詞
    - \R 分行符號 match 指示詞
    - 相符重設指示詞的 \K 開始
    - 標注和內嵌程式碼
    - 不可部分完成的群組和所有格數量詞

  - **否定 [選用]：** 如果條件的結果應該是否定的，您可以將 [ *否定* ] 條件設定為 [true]。
      
  - **轉換 [選用]：** 在嘗試比對之前，具有要執行之轉換名稱的字串清單。 這些可以是下列轉換：
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
     - REQUESTS
     - 複製
     - MOVE

## <a name="examples"></a>範例

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>以 HTTP 參數為基礎的 WAF 自訂規則範例

以下範例顯示具有兩個相符條件的自訂規則設定。 要求是來自指定的網站，如訪客所定義，而查詢字串不包含「密碼」。

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
封鎖「PUT」方法的範例設定如下所示：

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

您可以建立自訂規則，以指定傳入要求的一部分的大小條件約束。 例如，下列規則會封鎖長度超過100個字元的 Url。

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
- [使用 Azure PowerShell 來設定 Web 應用程式防火牆原則](waf-front-door-custom-rules-powershell.md) 
- 瞭解 [Front Door 的 Web 應用程式防火牆](afds-overview.md)
- 了解如何[建立 Front Door](../../frontdoor/quickstart-create-front-door.md)。

