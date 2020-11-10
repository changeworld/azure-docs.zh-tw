---
title: Azure Front Door-萬用字元網域的支援
description: 本文可協助您瞭解 Azure Front Door 如何支援在自訂網域清單中對應和管理萬用字元網域。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 18504f1ed4200889b20c9608c9c0ad2c13c9aaa5
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425757"
---
# <a name="wildcard-domains"></a>萬用字元網域

除了頂點網域和子域，您也可以將萬用字元網域對應到您的 Azure Front Door 設定檔的前端主機或自訂網域。 在您的 Azure Front Door 設定中擁有萬用字元網域，可針對 API、應用程式或網站的相同路由規則，簡化多個子域的流量路由行為。 您不需要修改設定來個別新增或指定每個子域。 例如，您可以 `customer1.contoso.com` `customer2.contoso.com` `customerN.contoso.com` 使用相同的路由規則和新增萬用字元網域，來定義、和的路由 `*.contoso.com` 。

以萬用字元網域支援改善的主要案例包括：

- 您不需要將 Azure Front Door 設定檔中的每個子域上架，然後啟用 HTTPS 來系結每個子域的憑證。
- 如果應用程式新增子域，您就不再需要變更生產 Azure Front Door 設定。 之前，您必須新增子域、將憑證系結至該子域、將 web 應用程式防火牆 (WAF) 原則，然後將網域新增至不同的路由規則。

> [!NOTE]
> 目前，只有透過 API、PowerShell 和 Azure CLI 才支援透過 Azure DNS 新增萬用字元網域。 無法支援在 Azure 入口網站中新增和管理萬用字元網域。

## <a name="adding-wildcard-domains"></a>新增萬用字元網域

您可以在前端主機或網域的區段底下新增萬用字元網域。 與子域類似，Azure Front Door 會驗證是否有萬用字元網域的 CNAME 記錄對應。 此 DNS 對應可以是直接 CNAME 記錄對應，例如 `*.contoso.com` 對應至 `contoso.azurefd.net` 。 或者，您可以使用 afdverify 暫時性對應。 例如， `afdverify.contoso.com` 對應以 `afdverify.contoso.azurefd.net` 驗證萬用字元的 CNAME 記錄對應。

> [!NOTE]
> Azure DNS 支援 萬用字元記錄。

您可以在前端主機中新增多個萬用字元網域的單一層級子域，最多可達前端主機的限制。 以下是可能需要的功能：

- 針對子域定義不同的路由，而不是從萬用字元網域 (的其餘網域) 。

- 針對特定子域擁有不同的 WAF 原則。 例如， `*.contoso.com` 允許新增， `foo.contoso.com` 而不需要再次證明網域擁有權。 但它不允許， `foo.bar.contoso.com` 因為它不是的單一層級子域 `*.contoso.com` 。 若要加入 `foo.bar.contoso.com` 而不需要額外的網域擁有權驗證，則必須 `*.bar.contosonews.com` 新增。

您可以新增萬用字元網域及其子域，但有某些限制：

- 如果將萬用字元網域新增至 Azure Front Door 設定檔：
  - 萬用字元網域無法新增至任何其他 Azure Front Door 設定檔。
  - 萬用字元網域的第一級子域無法新增至另一個 Azure Front Door 設定檔或 Azure 內容傳遞網路設定檔。
- 如果萬用字元網域的子域已新增至 Azure Front Door 設定檔或 Azure 內容傳遞網路設定檔中，則萬用字元網域不能用於其他 Azure Front Door 設定檔。
- 如果 (Azure Front Door 或 Azure 內容傳遞網路的兩個設定檔) 具有根域的不同子域，則無法將萬用字元網域新增至其中一個設定檔。

## <a name="certificate-binding"></a>憑證系結

若要在萬用字元網域上接受 HTTPS 流量，您必須在萬用字元網域上啟用 HTTPS。 萬用字元網域的憑證系結需要萬用字元憑證。 也就是說，憑證的主體名稱也應該有萬用字元網域。

> [!NOTE]
> 目前，只有使用您自己的自訂 SSL 憑證選項可啟用萬用字元網域的 HTTPS。 Azure Front Door 受控憑證無法用於萬用字元網域。

您可以選擇使用 Azure Key Vault 中的相同萬用字元憑證，或是子域的 Azure Front Door 受控憑證。

如果針對已有相關聯憑證的萬用字元網域新增子域，您就無法停用子域的 HTTPS。 子域會使用萬用字元網域的憑證系結，除非不同的 Key Vault 或 Azure Front Door 受控憑證覆寫它。

## <a name="waf-policies"></a>WAF 原則

WAF 原則可以附加至萬用字元網域，類似于其他網域。 不同的 WAF 原則可以套用至萬用字元網域的子域。 針對子域，您必須指定要使用的 WAF 原則，即使它是與萬用字元網域相同的原則也一樣。 子域不 *會自動* 從萬用字元網域繼承 WAF 原則。

如果您不想要針對子域執行 WAF 原則，您可以建立不含受控或自訂規則集的空白 WAF 原則。

## <a name="routing-rules"></a>路由規則

設定路由規則時，您可以選取萬用字元網域作為前端主機。 萬用字元網域和子域也可以有不同的路由行為。 如 [Azure Front Door 如何進行路由](front-door-route-matching.md)比對的方式所述，在執行時間會選擇跨不同路由規則之定義域的最特定相符項。

> [!IMPORTANT]
> 您必須在路由規則之間擁有相符的路徑模式，否則您的用戶端將會看到失敗。 例如，您有兩個路由規則（例如路由1） (`*.foo.com/*` 對應至後端集區 A) ，而路由 2 (`/bar.foo.com/somePath/*` 對應至後端集區 B) 。 然後，要求送達 `bar.foo.com/anotherPath/*` 。 Azure Front Door 會根據更明確的定義域比對來選取路由2，而只會找出路由之間沒有相符的路徑模式。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [建立 Azure Front Door 設定檔](quickstart-create-front-door.md)。
- 瞭解如何 [在 Azure Front Door 上新增自訂網域](front-door-custom-domain.md)。
- 瞭解如何 [在自訂網域上啟用 HTTPS](front-door-custom-domain-https.md)。
