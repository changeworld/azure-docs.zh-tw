---
title: 網路規則中的 Azure 防火牆 FQDN 篩選（預覽）
description: 如何使用網路規則中的 Azure 防火牆 FQDN 篩選
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: aef6fb52aaad3dd20db63f1d9023745169e84fcb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567414"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>網路規則中的 FQDN 篩選（預覽）

> [!IMPORTANT]
> 網路規則中的 FQDN 篩選目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

完整功能變數名稱（FQDN）代表主機的功能變數名稱。 定義功能變數名稱稱與單一或多個 IP 位址相關聯。 您可以允許或封鎖應用程式規則中的 Fqdn 和 FQDN 標記。 使用自訂 DNS 和 DNS proxy 設定時，您也可以在網路規則中使用 FQDN 篩選。

## <a name="how-it-works"></a>運作方式

Azure 防火牆會使用其 DNS 設定將 FQDN 轉譯為 IP 位址，並根據 Azure DNS 或自訂 DNS 設定進行規則處理。

若要在網路規則中使用 Fqdn，您應該啟用 DNS proxy。 如果您未啟用 DNS proxy，可靠的規則處理會有風險。 啟用時，會將 DNS 流量導向至 Azure 防火牆，您可以在其中設定您的自訂 DNS 伺服器。 然後防火牆和用戶端會使用相同設定的 DNS 伺服器。 如果 DNS proxy 未啟用，Azure 防火牆可能會產生不同的回應，因為用戶端和防火牆可能會使用不同的伺服器來進行名稱解析。 如果用戶端和防火牆收到不同的 DNS 回應，則網路規則中的 FQDN 篩選可能會發生錯誤或不一致。

您可以選擇在規則集合中選取 [**儲存**] 之前，先確認風險，以覆寫這項需求。

## <a name="next-steps"></a>後續步驟

[Azure 防火牆 DNS 設定](dns-settings.md)
