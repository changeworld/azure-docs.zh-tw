---
title: Azure Web 應用程式防火牆 （WAF） v2 應用程式閘道上的自訂規則
description: 本文概述了 Azure 應用程式閘道上的 Web 應用程式防火牆 （WAF） v2 自訂規則。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 072c7bd5b5b292ca4f0e53c59fcb7e9771331a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031726"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Azure 應用程式閘道上的 Web 應用程式防火牆 v2 的自訂規則

Azure 應用程式閘道 Web 應用程式防火牆 （WAF） v2 附帶一個預配置的平臺管理規則集，提供抵禦許多不同類型的攻擊的保護。 這些攻擊包括跨網站腳本、SQL 注入等。 如果您是 WAF 管理員，則可能需要編寫自己的規則來增強核心規則集 （CRS） 規則。 您的規則可以基於匹配條件阻止或允許請求的流量。

自訂規則允許您創建自己的規則，這些規則會針對通過 WAF 的每個請求進行評估。 這些規則的優先順序會高於受控規則集中的其餘規則。 自訂規則包含規則名稱、規則優先順序和匹配條件陣列。 如果滿足這些條件，則執行操作（以允許或阻止）。

例如，您可以阻止來自 192.168.5.4/24 範圍內 IP 位址的所有請求。 在此規則中，運算子是*IPMatch，* 匹配值是 IP 位址範圍 （192.168.5.4/24），操作是阻止流量。 您還可以設置規則的名稱和優先順序。

自訂規則支援使用複合邏輯來制定更高級的規則，以滿足您的安全需求。 例如，（條件 1**和**條件 2）**或**條件 3）。 這意味著，如果滿足條件 1**和**條件 2，**或者**滿足條件 3，WAF 應執行自訂規則中指定的操作。

同一規則中的不同匹配條件始終使用**和**進行複合使用。 例如，阻止來自特定 IP 位址的流量，並且僅當流量使用特定瀏覽器時才阻止流量。

如果要**或**兩個不同的條件，這兩個條件必須位於不同的規則中。 例如，阻止來自特定 IP 位址的流量，或者如果流量使用特定的瀏覽器，則阻止流量。

> [!NOTE]
> WAF 自訂規則的最大數量為 100。 有關應用程式閘道限制的詳細資訊，請參閱[Azure 訂閱和服務限制、配額和約束](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)。

自訂規則中也支援正則運算式，就像在 CRS 規則集中一樣。 有關示例，請參閱[創建和使用自訂 Web 應用程式防火牆規則](create-custom-waf-rules.md)中的示例 3 和 5。

## <a name="allowing-vs-blocking"></a>允許與阻塞

允許和阻止流量與自訂規則很簡單。 例如，您可以阻止來自一系列 IP 位址的所有流量。 如果請求來自特定瀏覽器，則可以制定另一條規則以允許流量。

要允許某事，請確保參數`-Action`設置為 **"允許**"。 要阻止某些內容，請確保參數`-Action`設置為 **"阻止**"。

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

前面的`$BlockRule`映射到 Azure 資源管理器中的以下自訂規則：

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

此自訂規則包含名稱、優先順序、操作和要執行操作必須滿足的匹配條件陣列。 有關這些欄位的進一步說明，請參閱以下欄位說明。 例如，請參閱[創建和使用自訂 Web 應用程式防火牆規則](create-custom-waf-rules.md)。

## <a name="fields-for-custom-rules"></a>自訂規則的欄位

### <a name="name-optional"></a>名稱 [可選]

規則的名稱。  它出現在日誌中。

### <a name="priority-required"></a>優先順序[必需]

- 確定規則評估順序。 值越低，規則評估越早。 允許的範圍為 1-100。 
- 在所有自訂規則中必須是唯一的。 優先順序為 40 的規則在優先順序為 80 的規則之前進行評估。

### <a name="rule-type-required"></a>規則類型 [必需]

目前，必須匹配**規則**。

### <a name="match-variable-required"></a>匹配變數 [必需]

必須是變數之一：

- 遠端添加器 = 遠端電腦連接的 IP 位址/主機名稱
- 請求方法 = HTTP 要求方法（獲取、POST、PUT、刪除等）。
- 查詢字串 = URI 中的變數
- PostArgs = 在 POST 正文中發送的參數。 僅當"內容類型"標頭設置為"應用程式/x-www-表單 url 編碼"和"多部分/表單資料"時，才應用使用此匹配變數的自訂規則。
- 請求庫 = 請求的 URI
- 請求標題 = 請求的標題
- 請求正文 – 它包含整個請求正文。 僅當"內容類型"標頭設置為"應用程式/x-www-表單 url 編碼"時，才應用使用此匹配變數的自訂規則。 
- 請求 Cookie = 請求的 Cookie

### <a name="selector-optional"></a>選擇器 [可選]

描述匹配變數集合的欄位。 例如，如果匹配變數為"請求標題"，則選擇器可以位於*使用者代理*標頭上。

### <a name="operator-required"></a>操作員 [必需]

必須是以下運算子之一：

- IPMatch - 僅在匹配變數為*遠端添加器*時使用
- 等於 = 輸入與匹配值相同
- 包含
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- 地理匹配（預覽）

### <a name="negate-condition-optional"></a>否定條件 [可選]

否定當前條件。

### <a name="transform-optional"></a>轉換 [可選]

在嘗試匹配之前要執行的字串清單。 這些轉換可以是以下轉換：

- 小寫
- Trim
- UrlDecode
- UrlEncode 
- 刪除 Nulls
- HtmlEntity解碼

### <a name="match-values-required"></a>匹配值 [必需]

要匹配的值清單，可以視為*OR'ed。* 例如，它可以是 IP 位址或其他字串。 值格式取決於前面的運算子。

### <a name="action-required"></a>行動[要求]

- 允許 = 授權事務，跳過所有其他規則。 指定的請求將添加到允許清單中，一旦匹配，請求將停止進一步評估併發送到後端池。 不允許清單中的規則不會為任何其他自訂規則或託管規則進行評估。
- 塊 = 基於*SecDefaultAction（* 檢測/預防模式）阻止事務。 與"允許"操作一樣，一旦評估請求並將其添加到阻止清單，評估將停止，請求被阻止。 之後滿足相同條件的任何請求將不會進行評估，只會被阻止。 
- 日誌 = 允許規則寫入日誌，但允許規則的其餘部分運行以進行評估。 其他自訂規則按優先順序順序計算，後跟託管規則。

## <a name="geomatch-custom-rules-preview"></a>地理匹配自訂規則（預覽）

自訂規則允許您創建量身定制的規則，以滿足應用程式和安全性原則的確切需求。 您可以按國家/地區限制對 Web 應用程式的訪問。 有關詳細資訊，請參閱[地理匹配自訂規則（預覽）。](geomatch-custom-rules.md)

## <a name="next-steps"></a>後續步驟

瞭解自訂規則後，[請創建自己的自訂規則](create-custom-waf-rules.md)。
