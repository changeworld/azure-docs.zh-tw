---
title: 網路規則中的 Azure 防火牆 FQDN 篩選
description: 如何使用網路規則中的 Azure 防火牆 FQDN 篩選
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: 2f2cf9639acfa1330c8347ff654649004d7c382e
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380894"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>在網路規則中使用 FQDN 篩選

 (FQDN 的完整功能變數名稱) 代表 (es) 之主機或 IP 位址的功能變數名稱。 您可以根據 Azure 防火牆和防火牆原則中的 DNS 解析，在網路規則中使用 Fqdn。 這項功能可讓您使用任何 TCP/UDP 通訊協定（包括 NTP、SSH、RDP 及更多) ）來篩選輸出流量 (。 您必須啟用 DNS Proxy，才能在網路規則中使用 Fqdn。 如需詳細資訊，請參閱 [Azure 防火牆 DNS 設定](dns-settings.md)。

> [!NOTE]
> 根據設計，FQDN 篩選不支援萬用字元。

## <a name="how-it-works"></a>運作方式

一旦定義您的組織所需的 DNS 伺服器 (Azure DNS 或您自己的自訂 DNS) ，Azure 防火牆會根據選取的 DNS 伺服器，將 FQDN 轉譯為 (es) 的 IP 位址。 這項轉譯會針對應用程式和網路規則處理進行。

發生新的 DNS 解析時，系統會將新的 IP 位址新增至防火牆規則。 DNS 伺服器不再傳回的舊 IP 位址會在15分鐘內到期。 Azure 防火牆規則會每隔15秒更新一次，從網路規則中的 Fqdn DNS 解析。

### <a name="differences-in-application-rules-vs-network-rules"></a>應用程式規則與網路規則的差異

- HTTP/S 和 MSSQL 應用程式規則中的 FQDN 篩選是根據應用層級的透明 proxy 和 SNI 標頭。 因此，它可以辨別解析為相同 IP 位址的兩個 Fqdn。 在網路規則中使用 FQDN 篩選並不會發生這種情況。 

   如果可能的話，請一律使用應用程式規則：
     - 如果通訊協定是 HTTP/S 或 MSSQL，請使用應用程式規則進行 FQDN 篩選。
   - 針對 HTTP/S 或 MSSQL 以外的任何其他通訊協定，您可以使用應用程式或網路規則來進行 FQDN 篩選。

## <a name="next-steps"></a>後續步驟

[Azure 防火牆 DNS 設定](dns-settings.md)
