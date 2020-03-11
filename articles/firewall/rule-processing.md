---
title: Azure 防火牆規則處理邏輯
description: Azure 防火牆具有 NAT 規則、網路規則和應用程式規則。 規則的處理依據是規則類型。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: d3f8e52b4582c9467ae3ec61ee984771b801fe4f
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082587"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure 防火牆規則處理邏輯
您可以在 Azure 防火牆上設定 NAT 規則、網路規則和應用程式規則。 規則的處理依據是規則類型。 

> [!NOTE]
> 如果您啟用以威脅情報為基礎的篩選，這些規則會是最高優先順序，而且一律會先處理。 威脅情報篩選可能會在處理任何已設定的規則之前拒絕流量。 如需詳細資訊，請參閱以[Azure 防火牆威脅情報為基礎的篩選](threat-intel.md)。

## <a name="outbound"></a>輸出

### <a name="network-rules-and-applications-rules"></a>網路規則和應用程式規則

如果您設定網路規則和應用程式規則，則會在應用程式規則之前依優先順序套用網路規則。 之後規則就會終止。 因此，如果在網路規則中找到相符的項，則不會處理其他規則。  如果沒有符合的網路規則，而且如果通訊協定為 HTTP、HTTPS 或 MSSQL，則封包接著會以優先順序來評估應用程式規則。 如果仍然找不到相符的結果，則會針對[基礎結構規則集合](infrastructure-fqdns.md)評估封包。 如果仍然沒有相符項目，則封包預設會遭到拒絕。

## <a name="inbound"></a>輸入

### <a name="nat-rules"></a>NAT 規則

您可以設定目的地網路位址轉譯（DNAT）來啟用輸入網際網路連線，如[教學課程：使用 Azure 入口網站以 Azure 防火牆 DNAT 篩選輸入流量](tutorial-firewall-dnat.md)中所述。 NAT 規則會優先套用到網路規則之前。 如果找到相符項目，就會新增隱含的對應網路規則，以允許已轉譯的流量。 若要覆寫這個行為，您可以明確地使用符合已轉譯流量的拒絕規則來新增網路規則集合。

應用程式規則不會套用至輸入連接。 因此，如果您想要篩選輸入 HTTP/S 流量，您應該使用 Web 應用程式防火牆（WAF）。 如需詳細資訊，請參閱[什麼是 Azure Web 應用程式防火牆？](../web-application-firewall/overview.md)

## <a name="examples"></a>範例

下列範例會顯示其中一些規則組合的結果。

### <a name="example-1"></a>範例 1

因為有相符的網路規則，所以允許連線至 google.com。

**網路規則**

- 動作：允許


|NAME  |通訊協定  |來源類型  |來源  |目的地類型  |目的地址  |目的地連接埠|
|---------|---------|---------|---------|----------|----------|--------|
|允許-web     |TCP|IP 位址|*|IP 位址|*|80,443

**應用程式規則**

- 動作：拒絕

|NAME  |來源類型  |來源  |通訊協定：埠|目標 Fqdn|
|---------|---------|---------|---------|----------|----------|
|拒絕-google     |IP 位址|*|HTTP：80、HTTPs：443|google.com

**結果**

允許連線至 google.com，因為封包符合*允許-web*網路規則。 規則處理此時就會停止。

### <a name="example-2"></a>範例 2

SSH 流量遭到拒絕，因為較高優先順序的*拒絕*網路規則集合會封鎖它。

**網路規則集合1**

- 名稱：允許-集合
- 優先順序：200
- 動作：允許

|NAME  |通訊協定  |來源類型  |來源  |目的地類型  |目的地址  |目的地連接埠|
|---------|---------|---------|---------|----------|----------|--------|
|允許-SSH     |TCP|IP 位址|*|IP 位址|*|22

**網路規則集合2**

- 名稱： Deny-集合
- 優先順序：100
- 動作：拒絕

|NAME  |通訊協定  |來源類型  |來源  |目的地類型  |目的地址  |目的地連接埠|
|---------|---------|---------|---------|----------|----------|--------|
|拒絕-SSH     |TCP|IP 位址|*|IP 位址|*|22

**結果**

SSH 連線會遭到拒絕，因為較高優先權的網路規則集合會封鎖它。 規則處理此時就會停止。

## <a name="rule-changes"></a>規則變更

如果您將規則變更為拒絕先前允許的流量，則會捨棄任何相關的現有會話。

## <a name="next-steps"></a>後續步驟

- 深入了解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。