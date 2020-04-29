---
title: Azure 應用程式閘道中的 Web 應用程式防火牆要求大小限制與排除清單 - Azure 入口網站
description: 本文提供應用程式閘道中具有 Azure 入口網站的 Web 應用程式防火牆要求大小限制與排除清單設定的相關資訊。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7244788bbc7431c7f26363b2852babb72d5697e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77526785"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Web 應用程式防火牆要求大小限制與排除清單

Azure 應用程式閘道 Web 應用程式防火牆（WAF）提供 Web 應用程式的保護。 此文章說明 WAF 要求大小限制與排除清單設定。 這些設定位於與您應用程式閘道相關聯的 WAF 原則中。 若要深入瞭解 WAF 原則，請參閱[Azure 應用程式閘道上的 Azure Web 應用程式防火牆](ag-overview.md)和[建立應用程式閘道的 Web 應用程式防火牆原則](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>WAF 排除清單

![要求大小限制](../media/application-gateway-waf-configuration/waf-policy.png)

WAF 排除清單可讓您略過 WAF 評估的特定要求屬性。 常見範例是用於驗證或密碼欄位的 Active Directory 插入式權杖。 這類屬性較可能包含特殊字元，而會觸發 WAF 規則的誤判。 一旦屬性新增至 WAF 排除清單，任何已設定和作用中 WAF 規則就不會考慮該屬性。 排除清單的範圍是全域的。

下列屬性可以依名稱新增至排除清單。 選擇的欄位值不會針對 WAF 規則進行評估，但其名稱仍然是（請參閱下面的範例1，從 WAF 評估中排除使用者代理程式標頭的值）。 排除清單會移除域值的檢查。

* 要求標頭
* 要求 Cookie
* 您可以將要求屬性名稱（args）新增為排除元素，例如：

   * 表單欄位名稱
   * XML 實體
   * JSON 實體
   * URL 查詢字串引數

您可以指定要精確比對要求標頭、本文、Cookie 或查詢字串屬性。  或者，您也可以選擇指定部分相符即可。 排除規則屬於全域範圍，可套用至所有頁面和所有規則。

以下是支援的比對條件運算子：

- **等於**：此運算子適用於精確比對。 例如，若要選取名為 **bearerToken** 的標頭，請使用等於運算子搭配設定為 **bearerToken** 的選取器。
- **開頭為**：此運算子會比對開頭與指定之選取器值相符的所有欄位。
- **結尾為**：此運算子會比對結尾與指定之選取器值相符的所有欄位。
- **包含**：此運算子會比對包含與指定之選取器值相符的所有欄位。
- **Equals any**：此運算子符合所有要求欄位。 * 將會是選取器的值。

在所有情況下，比對都不會區分大小寫，且不允許使用規則運算式作為選取器。

> [!NOTE]
> 如需詳細資訊和疑難排解協助，請參閱[WAF 疑難排解](web-application-firewall-troubleshoot.md)。

### <a name="examples"></a>範例

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

下列範例示範排除專案的使用。

#### <a name="example-1"></a>範例 1

在此範例中，您想要排除使用者代理程式標頭。 使用者代理程式要求標頭包含特性字串，可讓網路通訊協定對等電腦識別要求軟體使用者代理程式的應用程式類型、作業系統、軟體廠商或軟體版本。 如需詳細資訊，請參閱[使用者代理程式](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)。

停用評估此標頭的原因有很多。 可能會出現 WAF 所見的字串，並假設它是惡意的。 例如，字串中的傳統 SQL 攻擊 "x = x"。 在某些情況下，這可能是合法的流量。 因此，您可能需要將此標頭從 WAF 評估中排除。

下列 Azure PowerShell Cmdlet 會將使用者代理程式標頭從評估中排除：

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>範例 2

這個範例會將*使用者*參數中的值排除在透過 URL 傳遞的要求中。 例如，假設您的環境中常見的使用者欄位包含 WAF 視為惡意內容的字串，因此它會封鎖它。  在此情況下，您可以排除 user 參數，讓 WAF 不會評估欄位中的任何專案。

下列 Azure PowerShell Cmdlet 會從評估中排除 user 參數：

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
因此，如果 URL `http://www.contoso.com/?user%281%29=fdafdasfda`傳遞至 WAF，它就不會評估字串**fdafdasfda**，但它仍會評估參數名稱**使用者 %281 %29**。 

## <a name="waf-request-size-limits"></a>WAF 要求大小限制



Web 應用程式可讓您設定介於上下限範圍之間的要求大小限制。 下列是兩個可用的大小限制：

- [要求本文大小上限] 欄位以 kb 為單位指定，並控制所有檔案上傳的整體要求大小限制。 此欄位值的範圍可從最小 1 KB 到最大 128 KB。 要求本文大小的預設值為 128 KB。
- 檔案上傳限制欄位是以 MB 為單位指定，而且它會控管允許的檔案上傳大小上限。 此欄位的最小值為 1 MB 和下列上限：

   - 100 MB 適用于 v1 中型 WAF 閘道
   - 500 MB 適用于 v1 大型 WAF 閘道
   - 750 MB 適用于 v2 WAF 閘道 

 檔案上傳限制的預設值為 100 MB。

WAF 也可提供可設定的旋鈕，以便開啟或關閉要求本文檢查。 根據預設，要求本文檢查是啟用的。 如果要求主體檢查已關閉，WAF 就不會評估 HTTP 訊息內文的內容。 在此情況下，WAF 會繼續針對標頭、Cookie 與 URI 強制執行 WAF 規則。 如果要求本文檢查關閉，則最大要求本文大小欄位就不適用，而且也無法設定。 關閉要求本文檢查可讓要傳送給 WAF 的訊息大於 128 KB，但不會檢查訊息本文是否有漏洞。

## <a name="next-steps"></a>後續步驟

在您設定 WAF 設定之後，您可以了解如何檢視 WAF 記錄。 如需詳細資訊，請參閱[應用程式閘道診斷](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。
