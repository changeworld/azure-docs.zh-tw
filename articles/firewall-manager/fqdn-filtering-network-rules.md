---
title: 網路規則中的 Azure 防火牆管理員篩選（預覽）
description: 如何在網路規則中使用 FQDN 篩選
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 28cd26532ca5bdf83902854b7910f7d6c18a4eab
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460145"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>網路規則中的 FQDN 篩選（預覽）

> [!IMPORTANT]
> 網路規則中的 FQDN 篩選目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

完整功能變數名稱（FQDN）代表主機或 IP 位址的功能變數名稱。 您可以根據 Azure 防火牆和防火牆原則中的 DNS 解析，使用網路規則中的 Fqdn。 這項功能可讓您使用任何 TCP/UDP 通訊協定（包括 NTP、SSH、RDP 等等）來篩選輸出流量。 您必須啟用 DNS Proxy，才能在您的網路規則中使用 Fqdn。 如需詳細資訊，請參閱[Azure 防火牆原則 DNS 設定（預覽）](dns-settings.md)。

## <a name="how-it-works"></a>運作方式

定義您的組織所需的 DNS 伺服器（Azure DNS 或您自己的自訂 DNS）之後，Azure 防火牆會根據選取的 DNS 伺服器將 FQDN 轉譯為 IP 位址。 這項轉譯會同時用於應用程式和網路規則處理。

與網路規則相較之下，在應用程式規則中使用功能變數名稱有何差異？ 

- HTTP/S 和 MSSQL 應用程式規則中的 FQDN 篩選是以應用層級透明 proxy 和 SNI 標頭為基礎。 因此，它可以分辨兩個解析成相同 IP 位址的 Fqdn。 這不是在網路規則中使用 FQDN 篩選的情況。 可能的話，請一律使用應用程式規則。
- 在 [應用程式規則] 中，您可以使用 HTTP/S 和 MSSQL 作為選取的通訊協定。 在 [網路規則] 中，您可以將任何 TCP/UDP 通訊協定與目的地 Fqdn 搭配使用。

## <a name="next-steps"></a>後續步驟

[Azure 防火牆 DNS 設定](dns-settings.md)
