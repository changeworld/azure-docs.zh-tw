---
title: Azure 前門 - 對萬用字元域的支援
description: 本文可説明您瞭解 Azure 前門如何支援在自訂域清單中映射和管理萬用字元域
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537436"
---
# <a name="wildcard-domains"></a>萬用字元域

除了頂點域和子域之外，您還可以將萬用字元功能變數名稱映射到前門設定檔的前端主機或自訂域清單。 在前門配置中設置萬用字元域可簡化來自同一路由規則的 API、應用程式或網站的多個子域的流量路由行為，而無需修改配置以單獨添加和/或指定每個子域。 例如，`customer1.contoso.com`您可以定義 的路由，`customer2.contoso.com`並通過`customerN.contoso.com`添加萬用字元域`*.contoso.com`來定義 和使用相同的路由規則。

支援萬用字元域解決的一些關鍵方案包括：

- 不再需要將前門的每個子域裝上板，然後啟用 HTTPS 為每個子域綁定證書。
- 如果應用程式添加了新的子域，則不再需要更改生產前門配置。 否則，它以前需要添加子域，將證書綁定到該子域，附加 Web 應用程式防火牆 （WAF） 策略，將域添加到不同的路由規則中。

> [!NOTE]
> 目前，萬用字元域僅通過 API、PowerShell 和 CLI 支援。 支援通過 Azure 門戶添加管理萬用字元域不可用。

## <a name="adding-wildcard-domains"></a>添加萬用字元域

您可以在"前端主機或域"部分下登載萬用字元域。 與子域類似，前門驗證萬用字元域是否有 CNAME 映射。 此 DNS 映射可以是直接的 CNAME`*.contoso.com`映射，`contoso.azurefd.net`如映射到或通過 afdverify`afdverify.contoso.com`臨時映射（`afdverify.contoso.azurefd.net`如映射以驗證萬用字元的 CNAME 映射）（Azure DNS 支援萬用字元記錄）。

如果萬用字元域未達到最大值，則可以在前端主機中添加盡可能多的萬用字元域的單級子域。前端主機的限制。 定義子域與域的其餘部分（來自萬用字元域）的不同路由或為特定子域使用不同的 WAF 策略可能需要此功能。 因此，`*.contoso.com`將允許添加`foo.contoso.com`，而無需再次證明域擁有權，但不是`foo.bar.contoso.com`因為這不是`*.contoso.com`的單個級別的子域。 要添加`foo.bar.contoso.com`而不進行額外的域擁有權驗證`*.bar.contosonews.com`，則需要添加。

### <a name="limitations"></a>限制

1. 如果在給定的前門設定檔中添加了萬用字元域，則無法將相同的域添加到任何其他前門設定檔中。 
2. 如果在給定的前門設定檔中添加了萬用字元域，則無法將該萬用字元域的任何子域添加到其他前門或 Microsoft 設定檔中的 Azure CDN
3. 如果萬用字元域的子域在前門設定檔或 Microsoft 設定檔中的 Azure CDN 中添加，則無法將萬用字元域添加到任何其他前門設定檔。 
4. 如果兩個設定檔（Microsoft 的前門或 Azure CDN）具有根域的各種子域，則無法在任一設定檔上添加萬用字元域。

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>萬用字元域及其子域的證書綁定

要接受萬用字元域上的 HTTPS 流量，必須在萬用字元域上啟用 HTTPS。 萬用字元域的證書綁定需要萬用字元證書，也就是說，證書的主題名稱還應具有萬用字元域。

> [!NOTE]
> 目前，僅使用您自己的自訂 SSL 憑證選項才能為萬用字元域啟用 HTTPS。 前門託管證書不能用於萬用字元域。 

您可以選擇對子域使用金鑰保存庫中的相同萬用字元證書，或者支援對子域使用前門託管證書。
如果為萬用字元域添加了子域，並且萬用字元域已關聯證書，則無法禁用此子域的 HTTPS。 預設情況下，子域將使用萬用字元域的證書綁定，除非被其他金鑰保存庫證書或前門託管證書覆蓋。

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>萬用字元域及其子域的 Web 應用程式防火牆

WAF 策略可以附加到與其他域類似的萬用字元域。 可以將不同的 WAF 策略應用於萬用字元域的子域。 對於子域，必須顯式指定要使用的 WAF 策略，即使它與萬用字元域的策略相同。 子域**不會**自動從萬用字元域繼承 WAF 策略。

如果方案不希望 WAF 為子域運行，則可以創建沒有託管或自訂規則集的空白 WAF 策略。

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>萬用字元域及其子域的路由規則

配置路由規則時，可以選擇萬用字元域作為前端主機。 還可以對萬用字元域與子域具有不同的路由行為。 如[前門如何進行路由匹配](front-door-route-matching.md)，將在運行時選擇不同路由規則域的最具體匹配。

> [!WARNING]
> 如果您有兩個路由規則，如**路由**1 `*.foo.com/*` ：映射到後端池 A**和路由** `bar.foo.com/somePath/*` 2 ：映射到後端池 B，並且如果請求到達`bar.foo.com/anotherPath/*`，則用戶端將看到失敗，因為前門將找不到兩個路由的任何匹配項。 這是因為根據我們的[路由匹配演算法](front-door-route-matching.md)，前門會根據更具體的域匹配選擇路由 2，但只能發現沒有匹配的路徑模式。 


## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 瞭解如何[在前門添加自訂域](front-door-custom-domain.md)。
- 瞭解如何[在自訂域上啟用 HTTPS。](front-door-custom-domain-https.md)
