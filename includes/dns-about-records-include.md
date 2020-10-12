---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 8ca054b3a3d5147b7d98a021ce1e26d02d5581b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050480"
---
### <a name="record-names"></a>記錄名稱

在 Azure DNS 中，記錄是使用相對名稱來指定。  (FQDN 的 *完整* 功能變數名稱) 包含區功能變數名稱稱，而 *相對* 名稱則否。 例如，區域中的相對記錄名稱會 `www` `contoso.com` 提供完整的記錄名稱 `www.contoso.com` 。

「頂點」** 記錄是 DNS 區域根目錄 (或「頂點」**) 的 DNS 記錄。 例如，在 DNS 區域中 `contoso.com` ，頂點記錄也有完整名稱 `contoso.com` (這有時稱為 *naked* 網域) 。  依照慣例，相對名稱 '\@' 用來代表頂點記錄。

### <a name="record-types"></a>記錄類型

每一筆 DNS 記錄都有名稱和類型。 記錄根據所包含的資料而組織成各種類型。 最常見的類型為 'A' 記錄，可將名稱對應到 IPv4 位址。 另一個一般類型為 'MX' 記錄，可將名稱對應到郵件伺服器。

Azure DNS 支援所有常見的 DNS 記錄類型：A、AAAA、CAA、CNAME、MX、NS、PTR、SOA、SRV 和 TXT。 請注意，[SPF 記錄是使用 TXT 記錄表示](../articles/dns/dns-zones-records.md#spf-records)。

### <a name="record-sets"></a>記錄集

有時候，您需要以指定的名稱和類型建立多筆 DNS 記錄。 例如，假設 'www.contoso.com' 網站裝載於兩個不同的 IP 位址。 網站需要兩筆不同的 A 記錄，每個 IP 位址各一筆。 以下是記錄集的範例：

```dns
www.contoso.com.        3600    IN    A    134.170.185.46
www.contoso.com.        3600    IN    A    134.170.188.221
```

Azure DNS 使用「記錄集」** 來管理所有 DNS 記錄。 記錄集 (也稱為「資源」** 記錄集) 是指一個區域中，具有相同名稱和相同類型的 DNS 記錄集合。 大部分的記錄集只包含單一記錄。 不過，像以上的記錄集包含多筆記錄的例子也屢見不鮮。

例如，假設您已經在區域 'contoso.com' 中建立了 A 記錄 'www'，指向 IP 位址 '134.170.185.46' （上述第一項記錄）。  若要建立第二筆記錄，您會將該記錄新增至現有的記錄集，而非建立額外的記錄集。

SOA 和 CNAME 記錄類型是例外狀況。 對於這些類型，DNS 標準不允許具有相同名稱的多筆記錄，因此這些記錄集只能包含單一記錄。