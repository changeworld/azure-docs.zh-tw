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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264774"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure 防火牆規則處理邏輯
您可以在 Azure 防火牆上配置 NAT 規則、網路規則和應用程式規則。 規則的處理依據是規則類型。 

> [!NOTE]
> 如果啟用基於威脅智慧的篩選，則這些規則是最高優先順序，並且始終首先處理。 威脅智慧篩選可能會在處理任何配置的規則之前拒絕流量。 有關詳細資訊，請參閱[Azure 防火牆威脅基於智慧的篩選](threat-intel.md)。

## <a name="outbound"></a>輸出

### <a name="network-rules-and-applications-rules"></a>網路規則和應用程式規則

如果配置網路規則和應用程式規則，則網路規則在應用程式規則之前按優先順序順序應用。 之後規則就會終止。 因此，如果在網路規則中找到匹配項，則不會處理其他規則。  如果沒有網路規則匹配，並且協定是 HTTP、HTTPS 或 MSSQL，則應用程式規則按優先順序順序計算資料包。 如果仍未找到匹配項，則根據[基礎結構規則集合](infrastructure-fqdns.md)對資料包進行評估。 如果仍然沒有相符項目，則封包預設會遭到拒絕。

## <a name="inbound"></a>輸入

### <a name="nat-rules"></a>NAT 規則

可以通過配置目標網路位址轉譯 （DNAT） 來啟用入站互聯網連接，如教程中所述[：使用 Azure 門戶使用 Azure 防火牆 DNAT 篩選入站流量](tutorial-firewall-dnat.md)。 NAT 規則在網路規則之前在優先順序上應用。 如果找到相符項目，就會新增隱含的對應網路規則，以允許已轉譯的流量。 若要覆寫這個行為，您可以明確地使用符合已轉譯流量的拒絕規則來新增網路規則集合。

應用程式規則不適用於入站連接。 因此，如果要篩選入站 HTTP/S 流量，則應使用 Web 應用程式防火牆 （WAF）。 有關詳細資訊，請參閱什麼是[Azure Web 應用程式防火牆？](../web-application-firewall/overview.md)

## <a name="examples"></a>範例

以下示例顯示了其中一些規則組合的結果。

### <a name="example-1"></a>範例 1

由於匹配的網路規則，允許連接到google.com。

**網路規則**

- 動作：允許


|NAME  |通訊協定  |來源類型  |來源  |目的地類型  |目的地址  |目的地連接埠|
|---------|---------|---------|---------|----------|----------|--------|
|允許網     |TCP|IP 位址|*|IP 位址|*|80,443

**應用程式規則**

- 操作：拒絕

|NAME  |來源類型  |來源  |協定：埠|目標 FQDN|
|---------|---------|---------|---------|----------|----------|
|拒絕-谷歌     |IP 位址|*|HTTP：80，HTTPs：443|google.com

**結果**

允許連接到google.com，因為資料包與*允許 Web*網路規則匹配。 此時規則處理將停止。

### <a name="example-2"></a>範例 2

SSH 流量被拒絕，因為優先順序較高的*拒絕*網路規則集合會阻止它。

**網路規則集合 1**

- 名稱：允許收集
- 優先順序：200
- 動作：允許

|NAME  |通訊協定  |來源類型  |來源  |目的地類型  |目的地址  |目的地連接埠|
|---------|---------|---------|---------|----------|----------|--------|
|允許-SSH     |TCP|IP 位址|*|IP 位址|*|22

**網路規則集合 2**

- 名稱：拒絕集合
- 優先順序：100
- 操作：拒絕

|NAME  |通訊協定  |來源類型  |來源  |目的地類型  |目的地址  |目的地連接埠|
|---------|---------|---------|---------|----------|----------|--------|
|拒絕-SSH     |TCP|IP 位址|*|IP 位址|*|22

**結果**

SSH 連接被拒絕，因為優先順序較高的網路規則集合會阻止它。 此時規則處理將停止。

## <a name="rule-changes"></a>規則更改

如果更改規則以拒絕以前允許的流量，則刪除任何相關的現有會話。

## <a name="next-steps"></a>後續步驟

- 深入了解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。