---
title: 針對 Azure 虛擬網路 NAT 連線問題進行疑難排解
titleSuffix: Azure Virtual Network NAT troubleshooting
description: 針對虛擬網路 NAT 的問題進行疑難排解。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d56cd3b3d286d69a51d8cc14eb8020343cf7295a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302979"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>針對 Azure 虛擬網路 NAT 連線問題進行疑難排解

本文可協助系統管理員診斷及解決使用虛擬網路 NAT 時的連線問題。

>[!NOTE] 
>虛擬網路 NAT 目前可作為公開預覽版提供。 其目前僅適用於一組有限的[區域](nat-overview.md#region-availability)。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。

## <a name="problems"></a>問題

- [SNAT 耗盡](#snat-exhaustion)。
- [ICMP Ping 失敗](#icmp-ping-is-failing)。

若要解決這些問題，請遵循下一節中的步驟。

## <a name="resolution"></a>解決方案

### <a name="snat-exhaustion"></a>SNAT 耗盡

單一 [NAT 閘道資源](nat-gateway-resource.md)支援 64,000 到 1 百萬個並行流程。  每個 IP 位址都會將 64,000 個 SNAT 連接埠提供給可用的清查。 每個 NAT 閘道資源最多可以使用 16 個 IP 位址。  [這裡](nat-gateway-resource.md#source-network-address-translation)有 SNAT 機制的詳細說明。

#### <a name="steps"></a>步驟：

1. 調查您的應用程式如何建立輸出連線 (例如，程式碼檢閱或封包擷取)。 
2. 判斷此活動是否為預期的行為，或應用程式是否運作不正常。  使用 Azure 監視器中的[計量](nat-metrics.md)來證實您的發現。
3. 評估是否遵循適當的模式。
4. 評估是否能使用指派給 NAT 閘道資源的其他 IP 位址，減輕 SNAT 連接埠耗盡。

#### <a name="design-pattern"></a>設計模式：

可能的話，一律利用連線重複使用和連線共用。  此模式會立即避免發生資源耗盡問題並導致可預測的行為。 您可以在許多開發程式庫和架構中找到這些模式的基元。
- 請考慮針對長時間執行的作業使用[非同步輪詢模式](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply)，以釋出連線資源給其他作業使用。
- 長時間存留的流程 (例如，重複使用的 TCP 連線) 應該使用 TCP Keepalive 或應用程式層 Keepalive 來避免中繼系統逾時。
- 正常的[重試模式](https://docs.microsoft.com/azure/architecture/patterns/retry)應該在暫時性失敗或失敗復原期間用來避免積極重試/高載。
為每個 HTTP 作業 (也稱為「不可部分完成的連線」) 建立新的 TCP 連線是一種反模式。  不可部分完成的連線會讓您的應用程式無法良好調整而浪費資源。  一律會透過管線將多個作業連至相同連線。  您的應用程式將受益於交易速度和資源成本。  當您的應用程式使用傳輸層加密 (例如 TLS) 時，處理新連線會產生相當大的成本。  如需其他最佳作法模式，請參閱 [Azure 雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns/)。

#### <a name="mitigations"></a>風險降低

您可以調整輸出連線，如下所示：

| 狀況 | 降低 |
|---|---|
| 您在高使用量期間遇到 SNAT 連接埠和 SNAT 連接埠爭相耗盡。 | 判斷您是否可以新增其他公用 IP 位址資源或公用 IP 首碼資源。 最多允許將總計 16 個 IP 位址新增至您的 NAT 閘道。 此新增作業將提供更多可用的 SNAT 連接埠清查 (每個 IP 位址64,000 個連接埠)，並可讓您進一步擴充您的案例。|
| 您已經指定了 16 個 IP 位址，且仍然遇到 SNAT 連接埠耗盡。 | 將您的應用程式環境分散於多個子網路，並為每個子網路提供 NAT 閘道資源。 |

>[!NOTE]
>請務必了解發生 SNAT 耗盡的原因。 請確定您將正確的模式用於可調整且可靠的案例。  在不了解需求原因的情況下，將更多 SNAT 連接埠新增至案例，應該是最後的手段。 如果您不了解為何您的案例會對 SNAT 連接埠清查造成壓力，藉由新增更多 IP 位址來將更多 SNAT 連接埠新增至清查，只會隨著您的應用程式擴充而延遲相同的耗盡失敗。  您可能會遮掩其他效率不佳情形和反模式。

### <a name="icmp-ping-is-failing"></a>ICMP Ping 失敗

[虛擬網路 NAT](nat-overview.md) 支援 IPv4 UDP 和 TCP 通訊協定。 ICMP 不受支援且預計會失敗。  相反地，請使用 TCP 連線測試 (例如「TCP Ping」) 和 UDP 特有應用程式層測試來驗證端對端連線能力。

下表可作為工具用來開始測試的起點。

| 作業系統 | 一般 TCP 連線測試 | TCP 應用程式層測試 | UDP |
|---|---|---|---|
| Linux | nc (一般連線測試) | curl (TCP 應用程式層測試) | 應用程式特有 |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | 應用程式特有 |

## <a name="next-steps"></a>後續步驟

- 了解[虛擬網路 NAT](nat-overview.md)
- 了解 [NAT 閘道資源](nat-gateway-resource.md)
- 了解 [NAT 閘道資源的計量和警示](nat-metrics.md)。
