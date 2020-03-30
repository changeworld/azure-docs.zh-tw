---
title: Azure 應用程式閘道中的 Web 應用程式防火牆要求大小限制與排除清單 - Azure 入口網站
description: 本文提供有關 Web 應用程式防火牆請求大小限制和排除清單配置的資訊，這些配置與 Azure 門戶一起位於應用程式閘道中。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7244788bbc7431c7f26363b2852babb72d5697e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526785"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Web 應用程式防火牆請求大小限制和排除清單

Azure 應用程式閘道 Web 應用程式防火牆 （WAF） 為 Web 應用程式提供保護。 此文章說明 WAF 要求大小限制與排除清單設定。 這些設置位於與您的應用程式閘道關聯的 WAF 策略中。 要瞭解有關 WAF 策略的資訊，請參閱[Azure 應用程式閘道上的 Azure Web 應用程式防火牆](ag-overview.md)，並為[應用程式閘道創建 Web 應用程式防火牆策略](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>WAF 排除清單

![要求大小限制](../media/application-gateway-waf-configuration/waf-policy.png)

WAF 排除清單可讓您略過 WAF 評估的特定要求屬性。 常見範例是用於驗證或密碼欄位的 Active Directory 插入式權杖。 這類屬性較可能包含特殊字元，而會觸發 WAF 規則的誤判。 一旦屬性新增至 WAF 排除清單，任何已設定和作用中 WAF 規則就不會考慮該屬性。 排除清單的範圍是全域的。

以下屬性可以按名稱添加到排除清單中。 所選欄位的值不會根據 WAF 規則計算，但它們的名稱仍為（請參閱下面的示例 1，使用者代理標頭的值從 WAF 評估中排除）。 排除清單將刪除對欄位值的檢查。

* 要求標頭
* 要求 Cookie
* 請求屬性名稱 （args） 可以添加為排除元素，例如：

   * 表單欄位名稱
   * XML 實體
   * JSON 實體
   * URL 查詢字串 args

您可以指定要精確比對要求標頭、本文、Cookie 或查詢字串屬性。  或者，您也可以選擇指定部分相符即可。 排除規則屬於全域範圍，可套用至所有頁面和所有規則。

以下是支援的比對條件運算子：

- **等於**：此運算子適用於精確比對。 例如，若要選取名為 **bearerToken** 的標頭，請使用等於運算子搭配設定為 **bearerToken** 的選取器。
- **開頭為**：此運算子會比對開頭與指定之選取器值相符的所有欄位。
- **結尾為**：此運算子會比對結尾與指定之選取器值相符的所有欄位。
- **包含**：此運算子會比對包含與指定之選取器值相符的所有欄位。
- **等於任何**：此運算子匹配所有請求欄位。 * 將是選擇器值。

在所有情況下，比對都不會區分大小寫，且不允許使用規則運算式作為選取器。

> [!NOTE]
> 有關詳細資訊和故障排除説明，請參閱 WAF[故障排除](web-application-firewall-troubleshoot.md)。

### <a name="examples"></a>範例

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

以下示例演示了排除項的使用。

#### <a name="example-1"></a>範例 1

在此示例中，要排除使用者代理標頭。 使用者代理請求標頭包含一個特徵字串，允許網路通訊協定對等方標識請求軟體使用者代理的應用程式類型、作業系統、軟體供應商或軟體版本。 有關詳細資訊，請參閱[使用者代理](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)。

禁用評估此標頭的原因可能有很多。 WAF 可能會看到一個字串，並假定它是惡意的。 例如，字串中的經典 SQL 攻擊"x_x"。 在某些情況下，這可能是合法的流量。 因此，您可能需要從 WAF 評估中排除此標頭。

以下 Azure PowerShell Cmdlet 將使用者代理標頭排除在評估之外：

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>範例 2

此示例排除通過 URL 在請求中傳遞*的使用者*參數中的值。 例如，假設使用者欄位包含 WAF 視為惡意內容的字串，因此它阻止它，這在你的環境中很常見。  在這種情況下，您可以排除使用者參數，以便 WAF 不會評估欄位中的任何內容。

以下 Azure PowerShell Cmdlet 將使用者參數排除在評估之外：

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
因此，如果`http://www.contoso.com/?user%281%29=fdafdasfda`URL 傳遞給 WAF，它不會評估字串**fdafdasfda，** 但它仍然會評估參數名稱**使用者%281%29**。 

## <a name="waf-request-size-limits"></a>WAF 要求大小限制



Web 應用程式可讓您設定介於上下限範圍之間的要求大小限制。 下列是兩個可用的大小限制：

- 最大請求正文大小欄位以千位元組為單位指定，並控制不包括任何檔上載的總體請求大小限制。 此欄位值的範圍可從最小 1 KB 到最大 128 KB。 要求本文大小的預設值為 128 KB。
- 檔案上傳限制欄位是以 MB 為單位指定，而且它會控管允許的檔案上傳大小上限。 此欄位的最小值為 1 MB，最大值為：

   - 100 MB 用於 v1 中型 WAF 閘道
   - 500 MB 用於 v1 大型 WAF 閘道
   - 750 MB 用於 v2 WAF 閘道 

 檔案上傳限制的預設值為 100 MB。

WAF 也可提供可設定的旋鈕，以便開啟或關閉要求本文檢查。 根據預設，要求本文檢查是啟用的。 如果請求正文檢查已關閉，WAF 不會評估 HTTP 郵件內文的內容。 在此情況下，WAF 會繼續針對標頭、Cookie 與 URI 強制執行 WAF 規則。 如果要求本文檢查關閉，則最大要求本文大小欄位就不適用，而且也無法設定。 關閉要求本文檢查可讓要傳送給 WAF 的訊息大於 128 KB，但不會檢查訊息本文是否有漏洞。

## <a name="next-steps"></a>後續步驟

在您設定 WAF 設定之後，您可以了解如何檢視 WAF 記錄。 有關詳細資訊，請參閱[應用程式閘道診斷](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。
