---
title: Azure Front 門-支援萬用字元網域
description: 本文可協助您瞭解 Azure Front 門板如何支援在自訂網域清單中對應和管理萬用字元網域。
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: 6d8a6d6f0b05b9b7fd0144959c82b6a2c9e659a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81768313"
---
# <a name="wildcard-domains"></a>萬用字元網域

除了頂點網域和子域以外，您還可以將萬用字元功能變數名稱對應至您 Azure Front 設定檔中的前端主機或自訂網域清單。 在您的 Azure Front 門板設定中擁有萬用字元網域，可以針對 API、應用程式或來自相同路由規則的網站，簡化多個子域的流量路由行為。 您不需要修改設定來分別新增或指定每個子域。 例如，您可以使用相同的路由規則並`customer1.contoso.com`加入`customer2.contoso.com`萬用字元網域`customerN.contoso.com` `*.contoso.com`，來定義、和的路由。

以萬用字元網域的支援改善的主要案例包括：

- 您不需要將每個子域上線，然後啟用 HTTPS 來系結每個子域的憑證。
- 如果應用程式新增了新的子域，您就不再需要變更生產環境的 Azure Front 門板設定。 在過去，您必須新增子域、將憑證系結至它、附加 web 應用程式防火牆（WAF）原則，然後將網域新增至不同的路由規則。

> [!NOTE]
> 目前，只有透過 API、PowerShell 和 Azure CLI 才支援萬用字元網域。 不提供在 Azure 入口網站中新增和管理萬用字元網域的支援。

## <a name="adding-wildcard-domains"></a>新增萬用字元網域

您可以在前端主機或網域的區段下新增萬用字元網域。 類似于子域，Azure Front 門板會驗證您的萬用字元網域是否有 CNAME 記錄對應。 此 DNS 對應可以是直接 CNAME 記錄對應，例如`*.contoso.com`對應至`contoso.azurefd.net`。 或者，您可以使用 afdverify 暫時對應。 例如， `afdverify.contoso.com`對應以`afdverify.contoso.azurefd.net`驗證萬用字元的 CNAME 記錄對應。

> [!NOTE]
> Azure DNS 支援 萬用字元記錄。

您可以在前端主機中新增多個萬用字元網域的單一層級子域，最多可達前端主機的限制。 這是可能需要的功能：

- 定義子域的不同路由，而不是網域的其餘部分（從萬用字元網域）。

- 針對特定子域擁有不同的 WAF 原則。 例如，允許`*.contoso.com`新增`foo.contoso.com` ，而不需要再次證明網域擁有權。 但它並不`foo.bar.contoso.com`允許，因為它不是的單一`*.contoso.com`層級子域。 若要`foo.bar.contoso.com`新增而不需要額外的`*.bar.contosonews.com`網域擁有權驗證，則必須新增。

您可以新增萬用字元網域及其子域，但有一些限制：

- 如果萬用字元網域已新增至 Azure Front 設定檔：
  - 萬用字元網域無法新增至任何其他 Azure Front 設定檔。
  - 無法將萬用字元網域的第一層子域新增至另一個 Azure Front 門板設定檔或 Azure 內容傳遞網路設定檔。
- 如果將萬用字元網域的子域新增至 Azure Front profile 或 Azure 內容傳遞網路設定檔，則無法將萬用字元網域新增至其他 Azure Front 門板設定檔。
- 如果兩個設定檔（Azure Front 或 Azure 內容傳遞網路）有不同的根域子域，則無法將萬用字元網域新增至其中一個設定檔。

## <a name="certificate-binding"></a>憑證系結

若要接受萬用字元網域上的 HTTPS 流量，您必須在萬用字元網域上啟用 HTTPS。 萬用字元網域的憑證系結需要萬用字元憑證。 也就是說，憑證的主體名稱也應該有萬用字元網域。

> [!NOTE]
> 目前，只有使用您自己的自訂 SSL 憑證選項，才能為萬用字元網域啟用 HTTPS。 Azure Front 門板受控憑證無法用於萬用字元網域。

您可以選擇使用相同的萬用字元憑證，從 Azure Key Vault 或從 Azure Front 門板受控憑證（適用于子域）。

如果為已有相關聯憑證的萬用字元網域新增子域，則無法停用子域的 HTTPS。 子域會使用萬用字元網域的憑證系結，除非不同的 Key Vault 或 Azure Front 門板受控憑證會覆寫它。

## <a name="waf-policies"></a>WAF 原則

WAF 原則可以附加至萬用字元網域，類似于其他網域。 不同的 WAF 原則可以套用至萬用字元網域的子域。 對於子域，您必須指定要使用的 WAF 原則，即使它是與萬用字元網域相同的原則也一樣。 子域*不*會自動繼承萬用字元網域的 WAF 原則。

如果您不想要為子域執行 WAF 原則，可以建立空的 WAF 原則，而不使用受控或自訂的規則集。

## <a name="routing-rules"></a>路由規則

設定路由規則時，您可以選取萬用字元網域做為前端主機。 您也可以為萬用字元網域和子域提供不同的路由行為。 如[Azure Front 門板執行路由](front-door-route-matching.md)比對的方式中所述，在執行時間會選擇跨不同路由規則之網域的最特定相符項。

> [!IMPORTANT]
> 您的路由規則必須有相符的路徑模式，否則您的用戶端將會看到失敗。 例如，您有兩個路由規則，例如路由 1`*.foo.com/*` （對應至後端集區 A）和路由 2`bar.foo.com/somePath/*` （對應至後端集區 B）。 然後，要求會抵達`bar.foo.com/anotherPath/*`。 Azure Front 門板會根據更特定的網域比對來選取路由2，而只會在路由中找不到相符的路徑模式。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[建立 Azure Front 門板設定檔](quickstart-create-front-door.md)。
- 瞭解如何[在 Azure Front 中新增自訂網域](front-door-custom-domain.md)。
- 瞭解如何[在自訂網域上啟用 HTTPS](front-door-custom-domain-https.md)。
