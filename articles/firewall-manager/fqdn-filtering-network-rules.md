---
title: 'Azure 防火牆管理員 (preview 的網路規則篩選) '
description: 如何在網路規則中使用 FQDN 篩選
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 28cd26532ca5bdf83902854b7910f7d6c18a4eab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87460145"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>網路規則中的 FQDN 篩選 (預覽) 

> [!IMPORTANT]
> 網路規則中的 FQDN 篩選目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

 (FQDN 的完整功能變數名稱) 代表 (es) 之主機或 IP 位址的功能變數名稱。 您可以根據 Azure 防火牆和防火牆原則中的 DNS 解析，在網路規則中使用 Fqdn。 這項功能可讓您使用任何 TCP/UDP 通訊協定（包括 NTP、SSH、RDP 及更多) ）來篩選輸出流量 (。 您必須啟用 DNS Proxy，才能在網路規則中使用 Fqdn。 如需詳細資訊，請參閱 [Azure 防火牆原則 DNS 設定 (預覽) ](dns-settings.md)。

## <a name="how-it-works"></a>運作方式

一旦定義您的組織所需的 DNS 伺服器 (Azure DNS 或您自己的自訂 DNS) ，Azure 防火牆會根據選取的 DNS 伺服器，將 FQDN 轉譯為 (es) 的 IP 位址。 這項轉譯會針對應用程式和網路規則處理進行。

使用應用程式規則中的功能變數名稱與網路規則有何不同？ 

- HTTP/S 和 MSSQL 應用程式規則中的 FQDN 篩選是根據應用層級的透明 proxy 和 SNI 標頭。 因此，它可以辨別解析為相同 IP 位址的兩個 Fqdn。 在網路規則中使用 FQDN 篩選並不會發生這種情況。 請一律盡可能使用應用程式規則。
- 在應用程式規則中，您可以使用 HTTP/S 和 MSSQL 作為選取的通訊協定。 在網路規則中，您可以使用任何 TCP/UDP 通訊協定搭配目的地 Fqdn。

## <a name="next-steps"></a>接下來的步驟

[Azure 防火牆 DNS 設定](dns-settings.md)
