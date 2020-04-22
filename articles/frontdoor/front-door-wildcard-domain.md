---
title: Azure 前門 ─ 對通配符網域的支援
description: 本文可説明您瞭解 Azure 前門如何支援在自定義網域清單中映射和管理通配符域。
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
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768313"
---
# <a name="wildcard-domains"></a>通配子網域

除了頂點域和子域之外,您可以將通配符域名映射到 Azure 前門配置檔中的前端主機或自定義網域清單。 在 Azure 前門配置中設定通配符域可簡化來自同一路由規則的 API、應用程式或網站的多個子域的流量路由行為。 您無需修改設定就可以單獨添加或指定每個子域。 `customer1.contoso.com`例如,您可以定義的路由,`customer2.contoso.com`並`customerN.contoso.com`使用 相同的路由規則並添加通配符`*.contoso.com`域。

以支援通配符網域而改進的關鍵方案包括:

- 您無需將 Azure 前門配置檔中的每個子域裝上板,然後啟用 HTTPS 為每個子域綁定證書。
- 如果應用程式添加新子域,則不再需要更改生產 Azure 前門配置。 以前,您必須添加子域,將證書綁定到該子域,附加 Web 應用程式防火牆 (WAF) 策略,然後將域添加到不同的路由規則。

> [!NOTE]
> 目前,通配符域僅通過 API、PowerShell 和 Azure CLI 支援。 在 Azure 門戶中添加和管理通配符域的支援不可用。

## <a name="adding-wildcard-domains"></a>新增通配子網域

您可以在前端主機或域的節下添加通配符域。 與子域類似,Azure 前門驗證通配符域是否有 CNAME 記錄映射。 此 DNS 對應可以是直接的`*.contoso.com`CNAME 紀錄`contoso.azurefd.net`映射, 就像映射到 。 或者,您可以使用 fd 驗證臨時映射。 例如,`afdverify.contoso.com`映射`afdverify.contoso.azurefd.net`以 驗證通配符的 CNAME 記錄映射。

> [!NOTE]
> Azure DNS 支援 萬用字元記錄。

您可以在前端主機中添加通配符域的盡可能多的單級子域,最高達前端主機的限制。 此功能可能需要用於:

- 為子域定義與域的其餘部分(來自通配符域)不同的路由。

- 為特定子域使用不同的 WAF 策略。 例如,`*.contoso.com`允許`foo.contoso.com`添加 ,而無需再次證明域擁有權。 但它不允許`foo.bar.contoso.com`,因為它`*.contoso.com`不是 的單個級別子域。 要添加`foo.bar.contoso.com`而不進行額外的域擁有權`*.bar.contosonews.com`驗證 ,需要添加。

您可以新增有某些限制的通配符網域與子域:

- 如果將通配子網域加入 Azure 前門設定檔:
  - 無法將通配符域添加到任何其他 Azure 前門配置檔中。
  - 無法將通配符域的第一級子域添加到其他 Azure 前門配置檔或 Azure 內容交付網路配置檔。
- 如果將通配符網域的子域添加到 Azure 前門配置檔或 Azure 內容傳遞網路配置檔中,則無法將通配符域添加到其他 Azure 前門配置檔中。
- 如果兩個配置檔(Azure 前門或 Azure 內容傳遞網路)具有根域的各種子域,則通配符域無法添加到任一配置檔中。

## <a name="certificate-binding"></a>憑證繫結

要接受通配符域上的 HTTPS 流量,必須在通配符域上啟用 HS。 通配符域的證書綁定需要通配符證書。 也就是說,證書的主題名稱還應具有通配符域。

> [!NOTE]
> 目前,僅使用您自己的自定義 SSL 證書選項才能為通配符域啟用 HTTPS。 Azure 前門託管證書不能用於通配符域。

您可以選擇使用 Azure 密鑰保管庫中的相同通配符證書,也可以從子域的 Azure 前門託管證書中使用相同的通配符證書。

如果為已具有與其關聯的證書的通配符域添加了子域,則無法禁用子域的 HTTPS。 子域使用通配符域的證書綁定,除非其他密鑰保管庫或 Azure 前門託管證書覆蓋它。

## <a name="waf-policies"></a>WAF 政策

WAF 策略可以附加到通配符域,類似於其他域。 可以將不同的 WAF 策略應用於通配符域的子域。 對於子域,必須指定要使用的 WAF 策略,即使它與通配符域的策略相同。 子域*不會*自動從通配符域繼承 WAF 策略。

如果不希望 WAF 策略為子域運行,則可以創建沒有託管或自訂規則集的空 WAF 策略。

## <a name="routing-rules"></a>路由規則

設定路由規則時,可以選擇通配符域作為前端主機。 還可以對通配符域和子域具有不同的路由行為。 如[Azure 前門如何進行路由匹配](front-door-route-matching.md)中所述,在運行時選擇跨不同路由規則的域的最具體匹配。

> [!IMPORTANT]
> 您必須跨路由規則具有匹配的路徑模式,否則客戶端將看到失敗。 例如,您有兩個路由規則,如路由`*.foo.com/*`1(映射到後端池 A)和路由`bar.foo.com/somePath/*`2( 映射到後端池 B)。 然後,要求到達`bar.foo.com/anotherPath/*`。 Azure 前門根據更具體的域匹配選擇路由 2,但找不到跨路徑的匹配路徑模式。

## <a name="next-steps"></a>後續步驟

- 瞭解如何建立[Azure 前門設定檔](quickstart-create-front-door.md)。
- 瞭解如何在[Azure 前門新增自訂網域](front-door-custom-domain.md)。
- 瞭解如何[在自定義網域上啟用 HTTPS。](front-door-custom-domain-https.md)
