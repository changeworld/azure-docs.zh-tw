---
title: 針對 Azure 虛擬網路 NAT 連線進行疑難排解
titleSuffix: Azure Virtual Network
description: 針對虛擬網路 NAT 的問題進行疑難排解。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2020
ms.author: allensu
ms.openlocfilehash: 690543ebc91e346e77509fbf993493f6978374ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84688276"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>針對 Azure 虛擬網路 NAT 連線進行疑難排解

本文可協助系統管理員診斷及解決使用虛擬網路 NAT 時的連線問題。

## <a name="problems"></a>問題

* [SNAT 耗盡](#snat-exhaustion)
* [ICMP Ping 失敗](#icmp-ping-is-failing)
* [連線失敗](#connectivity-failures)
* [IPv6 共存](#ipv6-coexistence)
* [連線並非來自 NAT 閘道 IP](#connection-doesnt-originate-from-nat-gateway-ips)

若要解決這些問題，請遵循下一節中的步驟。

## <a name="resolution"></a>解決方案

### <a name="snat-exhaustion"></a>SNAT 耗盡

單一 [NAT 閘道資源](nat-gateway-resource.md)支援 64,000 到 1 百萬個並行流程。  每個 IP 位址都會將 64,000 個 SNAT 連接埠提供給可用的清查。 每個 NAT 閘道資源最多可以使用 16 個 IP 位址。  [這裡](nat-gateway-resource.md#source-network-address-translation)有 SNAT 機制的詳細說明。

SNAT 耗盡的根本原因通常是一種反模式，說明如何建立、管理或設定計時器從其預設值變更。  請仔細檢閱這一節。

#### <a name="steps"></a>步驟

1. 檢查您是否已將預設的閒置逾時時間修改為大於 4 分鐘的值。
2. 調查您的應用程式如何建立輸出連線 (例如，程式碼檢閱或封包擷取)。 
3. 判斷此活動是否為預期的行為，或應用程式是否運作不正常。  使用 Azure 監視器中的[計量](nat-metrics.md)來證實您的發現。 針對 SNAT 連線計量，使用「失敗」類別。
4. 評估是否遵循適當的模式。
5. 評估是否能使用指派給 NAT 閘道資源的其他 IP 位址，減輕 SNAT 連接埠耗盡。

#### <a name="design-patterns"></a>設計模式

可能的話，一律利用連線重複使用和連線共用。  這些模式會避免發生資源耗盡問題，並產生可預測的行為。 您可以在許多開發程式庫和架構中找到這些模式的基元。

_**解決方案：**_ 使用適當的模式和最佳做法

- NAT 閘道資源的預設 TCP 閒置逾時為 4 分鐘。  如果將此設定變更為較高的值，NAT 將會維持較長時間的流量，並可能造成 [SNAT 連接埠清查的不必要壓力](nat-gateway-resource.md#timers)。
- 不可部分完成的要求 (每個連線一個要求) 是不良的設計選擇。 這種反向模式會限制規模調整、降低效能，並降低可靠性。 相反地，應重複使用 HTTP/S 連線以減少連線數目和關聯的 SNAT 連接埠。 使用 TLS 時會減少交握、額外負荷和密碼編譯作業成本，因此應用程式規模會增加並提升效能。
- 如果用戶端未快取 DNS 解析程式結果，DNS 就可以在磁碟區引進許多個別流程。 使用快取。
- UDP 流程 (例如 DNS 查閱) 會在閒置逾時期間配置 SNAT 連接埠。 閒置的逾時時間愈長，SNAT 連接埠上的壓力愈高。 使用較短的閒置時間 (例如4分鐘)。
- 使用連接集區來塑造您的連線磁碟區。
- 永遠不要以無訊息方式放棄 TCP 流程，並依賴 TCP 計時器來清除流程。 如果您未讓 TCP 明確關閉連線，則狀態會保持分配於中繼系統和端點，並讓 SNAT 連接埠無法供其他連線使用。 此模式可能會觸發應用程式失敗和 SNAT 耗盡的問題。 
- 在沒有從專業角度了解可能產生的影響時，請勿變更 作業系統層級 TCP 關閉相關的計時器值。 雖然 TCP 堆疊會復原，但當連線端點的表現不如預期時，您的應用程式效能可能會受到負面影響。 變更計時器的需求通常是基礎設計出現問題的信號。 請檢閱下列建議。

SNAT 耗盡也會隨著基礎應用程式中的其他反向模式而增加。 請參閱這些額外的模式和最佳做法，以改善服務的規模調整和可靠性。

- 探索減少 [TCP 閒置逾時](nat-gateway-resource.md#timers)的影響，以降低包括稍早要釋放 SNAT 連接埠清單的 4 分鐘預設閒置逾時。
- 請考慮針對長時間執行的作業使用[非同步輪詢模式](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply)，以釋出連線資源給其他作業使用。
- 長時間存留的流程 (例如，重複使用的 TCP 連線) 應該使用 TCP Keepalive 或應用程式層 Keepalive 來避免中繼系統逾時。增加閒置逾時的時間是最後手段，而且可能無法解決根本原因。 長時間逾時會在逾時時間到期時導致低速率失敗，並引來延遲和不必要的失敗。
- 正常的[重試模式](https://docs.microsoft.com/azure/architecture/patterns/retry)應該在暫時性失敗或失敗復原期間用來避免積極重試/高載。
為每個 HTTP 作業 (也稱為「不可部分完成的連線」) 建立新的 TCP 連線是一種反模式。  不可部分完成的連線會讓您的應用程式無法良好調整而浪費資源。  一律會透過管線將多個作業連至相同連線。  您的應用程式將受益於交易速度和資源成本。  當您的應用程式使用傳輸層加密 (例如 TLS) 時，處理新連線會產生相當大的成本。  如需其他最佳作法模式，請參閱 [Azure 雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns/)。

#### <a name="additional-possible-mitigations"></a>其他可能的因應措施

_**解決方案：**_ 調整輸出連線，如下所示：

| 狀況 | 辨識項 |降低 |
|---|---|---|
| 您在高使用量期間遇到 SNAT 連接埠和 SNAT 連接埠爭相耗盡。 | Azure 監視器中的 SNAT 連線的「失敗」類別目錄 [metric](nat-metrics.md) 會顯示一段時間和高連線量的暫時性或持續性失敗。  | 判斷您是否可以新增其他公用 IP 位址資源或公用 IP 首碼資源。 最多允許將總計 16 個 IP 位址新增至您的 NAT 閘道。 此新增作業將提供更多可用的 SNAT 連接埠清查 (每個 IP 位址64,000 個連接埠)，並可讓您進一步擴充您的案例。|
| 您已經指定了 16 個 IP 位址，且仍然遇到 SNAT 連接埠耗盡。 | 嘗試新增其他的 IP 位址失敗。 來自公用 IP 位址資源或公用 IP 前置詞資源的 IP 位址總數超過了 16 個。 | 將您的應用程式環境分散於多個子網路，並為每個子網路提供 NAT 閘道資源。  重新評估設計模式，根據前面的[指引](#design-patterns)進行最佳化。 |

>[!NOTE]
>請務必了解發生 SNAT 耗盡的原因。 請確定您將正確的模式用於可調整且可靠的案例。  在不了解需求原因的情況下，將更多 SNAT 連接埠新增至案例，應該是最後的手段。 如果您不了解為何您的案例會對 SNAT 連接埠清查造成壓力，藉由新增更多 IP 位址來將更多 SNAT 連接埠新增至清查，只會隨著您的應用程式擴充而延遲相同的耗盡失敗。  您可能會遮掩其他效率不佳情形和反模式。

### <a name="icmp-ping-is-failing"></a>ICMP Ping 失敗

[虛擬網路 NAT](nat-overview.md) 支援 IPv4 UDP 和 TCP 通訊協定。 ICMP 不受支援且預計會失敗。  

_**解決方案：**_ 相反地，請使用 TCP 連線測試 (例如「TCP Ping」) 和 UDP 特有應用程式層測試來驗證端對端連線能力。

下表可作為工具用來開始測試的起點。

| 作業系統 | 一般 TCP 連線測試 | TCP 應用程式層測試 | UDP |
|---|---|---|---|
| Linux | nc (一般連線測試) | curl (TCP 應用程式層測試) | 應用程式特有 |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | 應用程式特有 |

### <a name="connectivity-failures"></a>連線失敗

[虛擬網路 NAT](nat-overview.md) 的連線問題，可能是因為幾個不同的問題而產生：

* 因設定錯誤而造成的永久失敗。
* NAT 閘道暫時性或持續性的 [SNAT 耗盡](#snat-exhaustion)，
* Azure 基礎結構中的暫時性失敗， 
* Azure 和公用網際網路目的地之間的路徑發生暫時性失敗， 
* 公用網際網路目的地的暫時性或持續性失敗。

使用類似下列的工具來驗證連線能力。 [不支援 ICMP Ping](#icmp-ping-is-failing)。

| 作業系統 | 一般 TCP 連線測試 | TCP 應用程式層測試 | UDP |
|---|---|---|---|
| Linux | nc (一般連線測試) | curl (TCP 應用程式層測試) | 應用程式特有 |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | 應用程式特有 |

#### <a name="configuration"></a>組態

檢查組態：
1. NAT 閘道資源是否至少有一個公用 IP 資源或一個公用 IP 首碼資源？ 您必須至少有一個與 NAT 閘道相關聯的 IP 位址，才能提供輸出連線能力。
2. 虛擬網路的子網路是否已設定為使用 NAT 閘道？
3. 您是否使用 UDR (使用者定義的路由)，以及是否要覆寫目的地？  NAT 閘道資源會成為已設定子網路上的預設路由 (0/0)。

#### <a name="snat-exhaustion"></a>SNAT 耗盡

請檢閱本文中的 [SNAT 耗盡](#snat-exhaustion)一節。

#### <a name="azure-infrastructure"></a>Azure 基礎結構

Azure 會非常小心地監視並操作其基礎結構。 可能會發生暫時性失敗，而且不保證傳輸不失真。  使用可讓 TCP 應用程式進行 SYN 重新傳輸的設計模式。 使用夠長的連線逾時來允許 TCP SYN 重新傳輸，以減少因 SYN 封包遺失而造成的暫時性影響。

_**解決方案：**_

* 檢查 [SNAT 耗盡](#snat-exhaustion)。
* TCP 堆疊中控制 SYN 重新傳輸行為的組態參數稱為 RTO ([重新傳輸逾時](https://tools.ietf.org/html/rfc793))。 RTO 值是可調整的，但通常預設為 1 秒或以上，並具有指數輪詢。  如果應用程式的連線逾時時間太短 (例如 1 秒)，您可能偶爾會看到連線逾時的情況。  增加應用程式連線逾時。
* 如果您發現預設應用程式行為發生較長且非預期的逾時，請開啟支援案例以取得進一步疑難排解資訊。

我們不建議以人為方式減少 TCP 連線逾時，或調整 RTO 參數。

#### <a name="public-internet-transit"></a>公用網際網路傳輸

暫時性失敗的機率會隨著前往目的地和更多中繼系統的路徑較長而增加。 一般預期暫時性失敗的頻率會隨著 [Azure 基礎結構](#azure-infrastructure)而增加。 

遵循與先前 [Azure 基礎結構](#azure-infrastructure)一節相同的指導方針。

#### <a name="internet-endpoint"></a>網際網路端點

適用先前的章節，以及與之建立通訊的網際網路端點。 可能會影響連線成功的其他因素如下：

* 目的地端的流量管理，包括
- 目的地端加諸的 API 速率限制
- 大量 DDoS 緩和措施或傳輸層流量成形
* 目的地的防火牆或其他元件 

通常需要來源以及目的地 (如果有) 的封包擷取，才能判斷影響連線的因素為何。

_**解決方案：**_

* 檢查 [SNAT 耗盡](#snat-exhaustion)。 
* 驗證相同區域或其他位置中端點的連線能力，以進行比較。  
* 如果您要建立大量或交易速率測試，請探索降低速率是否可減少發生失敗的情況。
* 如果變更速率會影響失敗率，請檢查是否已達到目的地端的 API 速率限制或其他條件約束。
* 如果您的調查沒有明確結果，請開啟支援案例進一步,取得疑難排解資訊。

#### <a name="tcp-resets-received"></a>已接收的 TCP 重設

NAT 閘道會針對無法辨識為進行中的流量，在來源 VM 上產生 TCP 重設。

其中一個可能的原因是 TCP 連線閒置時間已逾時。您可以將閒置逾時時間從 4 分鐘調整為最多 120 分鐘。

NAT 閘道資源的公用端不會產生 TCP 重設。 目的地端上的 TCP 重設是由來源 VM 所產生，而不是 NAT 閘道資源。

_**解決方案：**_

* 請參閱[設計模式](#design-patterns)建議。  
* 如有必要，請開啟支援案例以取得進一步的疑難排解資訊。

### <a name="ipv6-coexistence"></a>IPv6 共存

[虛擬網路 NAT](nat-overview.md) 支援 IPv4 UDP 和 TCP 通訊協定，且[不支援在具有 IPv6 前置詞的子網路上進行部署](nat-overview.md#limitations)。

_**解決方案：**_ 在沒有 IPv6 前置詞的子網路上部署 NAT 閘道。

您可以透過 [虛擬網路 NAT UserVoice](https://aka.ms/natuservoice)，指出其他功能的相關性質。

### <a name="connection-doesnt-originate-from-nat-gateway-ips"></a>連線並非來自 NAT 閘道 IP

您可以設定 NAT 閘道、要使用的 IP 位址，以及應該使用 NAT 閘道資源的子網路。 不過，在部署 NAT 閘道之前，來自虛擬機器執行個體的連線不會使用 IP 位址。  這些連線似乎使用了不與 NAT 閘道資源搭配使用的 IP 位址。

_**解決方案：**_

[虛擬網路 NAT](nat-overview.md) 會取代其設定所在子網路的輸出連線能力。 從預設 SNAT 或負載平衡器輸出 SNAT 轉換為使用 NAT 閘道時，新的連線會立即開始使用與 NAT 閘道資源相關聯的 IP 位址。  不過，如果虛擬機器在切換到 NAT 閘道資源期間仍有已建立的連線，連線將會繼續使用建立連線時所指派的舊 SNAT IP 位址。  請確定您確實建立了新的連線，而不是重複使用已經存在的連線，因為 OS 或瀏覽器是在連線集區中快取連線。  例如，在 PowerShell 中使用「捲曲」時，請務必指定「-DisableKeepalive」參數來強制執行新的連線。  如果您使用的是瀏覽器，也可以將連線集區化。

您不需要重新啟動虛擬機器來設定 NAT 閘道資源的子網路。  不過，如果重新啟動虛擬機器，則連線狀態會是已排清。  將連線狀態排清後，所有連線都會開始使用 NAT 閘道資源的 IP 位址。  不過，這是重新啟動虛擬機器的副作用，而不是需要重新啟動的指標。

如果您仍然遇到問題，請開啟支援案例以取得進一步的疑難排解。

## <a name="next-steps"></a>後續步驟

* 了解[虛擬網路 NAT](nat-overview.md)
* 了解 [NAT 閘道資源](nat-gateway-resource.md)
* 了解 [NAT 閘道資源的計量和警示](nat-metrics.md)。
* [在 UserVoice 中告訴我們可為虛擬網路 NAT 打造的下一項功能](https://aka.ms/natuservoice)。

