---
title: 新功能 版本資訊-Azure 區塊鏈服務
description: 瞭解 Azure 區塊鏈 Service 的新功能，例如最新的版本資訊、版本、已知問題，以及即將進行的變更。
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 80ece6cb6bb81b7ce168da997603e17d1238171b
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921882"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Azure 區塊鏈 Service 有哪些新功能？

> 藉由複製並貼上此 URL，取得何時要重新流覽此頁面以進行更新的通知：在 `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` 您的 rss 摘要讀取器[ ![ rss 摘要讀取器圖示](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us)中。

Azure 區塊鏈 Service 會持續收到改進功能。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 新功能
- 版本升級
- 已知問題

---

## <a name="june-2020"></a>2020 年 6 月

### <a name="version-upgrades"></a>版本升級

- 將仲裁版本升級至2.6.0。 使用版本2.6.0 時，您可以傳送已簽署的私用交易。 如需有關傳送私用交易的詳細資訊，請參閱[仲裁 API 檔](https://docs.goquorum.com/en/latest/Getting%20Started/api/)。
- Tessera 版本升級至0.10.5。

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>合約大小和交易大小已增加至 128 KB

類型：設定變更

合約大小（MaxCodeSize）已增加至 128 KB，因此您可以部署較大的智慧合約。 此外，交易大小（txnSizeLimit）已增加至 128 KB。 設定變更適用于 19 2020 年6月之後在 Azure 區塊鏈 Service 上建立的新協會。

### <a name="trietimeout-value-reduced"></a>TrieTimeout 值減少

類型：設定變更

TrieTimeout 值已減少，因此記憶體內部狀態會更頻繁地寫入磁片。 較低的值可確保在節點損毀的罕見情況下，更快速地復原節點。

## <a name="may-2020"></a>2020 年 5 月

### <a name="version-upgrades"></a>版本升級

- Ubuntu 版本升級至18.04
- 將仲裁版本升級至2.5。0
- Tessera 版本升級0.10。4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Azure 區塊鏈 Service 支援傳送 rawPrivate 交易

類型：功能

客戶可以在節點上的帳戶外簽署私人交易。

### <a name="two-phase-member-provisioning"></a>兩階段成員布建

類型：增強功能

兩個階段可協助將成員建立于長時間現有的聯盟中的案例優化。 成員基礎結構會在第一個階段中布建。 在第二個階段中，成員會與區塊鏈同步處理。 兩階段布建有助於避免因為超時而造成成員建立失敗。

## <a name="known-issues"></a>已知問題

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>estimateGas 函式在仲裁 v 2.6.0 中擲回例外狀況

在仲裁 v 2.6.0 中，會呼叫*estimateGas*函式，但不提供額外的*值*參數會導致*方法處理常式*損毀例外狀況。 仲裁小組已收到通知，而且2020年7月結束了預期的修正程式。 您可以使用下列因應措施，直到有可用的修正：

- 避免使用*estimateGas* ，因為它可能會影響效能。 如需有關 estimateGas 效能問題的詳細資訊，請參閱呼叫 estimateGas 函式可[降低效能](#calling-ethestimategas-function-reduces-performance)。 包含每筆交易的天然氣值。 大部分的程式庫會呼叫 estimateGas，如果未提供天然氣值，這會導致仲裁 v 2.6.0 損毀。
- 如果您需要呼叫*estimateGas*，仲裁小組會建議您將額外的參數*值*傳遞為*0* ，做為因應措施。

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>如果少於四個驗證程式節點，則會停止進行

生產網路應該至少有四個驗證程式節點。 仲裁建議至少要有四個驗證器節點，才能符合 IBFT 損毀容錯（3F + 1）。 您至少要有兩個 Azure 區塊鏈 Service*標準*層節點，才能取得四個驗證器節點。 標準節點會布建兩個驗證器節點。  

如果 Azure 區塊鏈服務上的區塊鏈網路沒有四個驗證程式節點，則可能會在網路上停止進行挖掘。 您可以藉由在已處理的區塊上設定警示，來偵測已停止的挖掘。 在狀況良好的網路中，已處理的區塊每五分鐘會是每個節點60個區塊。

作為緩和措施，Azure 區塊鏈服務小組必須重新開機節點。 客戶需要開啟支援要求以重新開機節點。 Azure 區塊鏈服務小組正致力於自動偵測和修正挖掘問題。

使用「標準」層來進行生產等級的部署。 使用「基本」層來進行開發、測試和概念證明。 不支援在建立成員之後變更基本和標準間的定價層。

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>區塊鏈資料管理員需要標準層節點

如果您使用區塊鏈資料管理員，請使用*標準*層。 *基本*層僅有 4 GB 的記憶體。 因此，它無法調整為區塊鏈資料管理員及其他在其上執行的服務所需的使用量。

使用「基本」層來進行開發、測試和概念證明。 不支援在建立成員之後變更基本和標準間的定價層。

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>大量的解除鎖定帳戶呼叫會導致 geth 損毀

提交交易時，大量的解除鎖定帳戶呼叫可能會導致 geth 在交易節點上損毀。 因此，您必須停止內嵌交易。 否則，暫止的交易佇列會增加。

Geth 會在不到一分鐘的時間內自動重新開機。 視節點而定，同步處理可能需要很長的時間。 Azure 區塊鏈服務小組正在啟用封存功能，可減少同步處理的時間。

若要識別 geth 損毀，您可以在應用程式記錄檔中的區塊鏈訊息中，檢查記錄是否有任何錯誤訊息。 您也可以檢查已處理的區塊是否會在暫止的交易增加時減少。

若要減少此問題，請傳送已簽署的交易，而不是使用命令來解除鎖定帳戶，而不是傳送未簽署的交易 對於已經在外部簽署的交易，則不需要解除鎖定帳戶。

如果您想要傳送不帶正負號的交易，請在解除鎖定命令中將0當做 time 參數，以將帳戶解除鎖定無限時間。 提交所有交易之後，您可以將帳戶鎖定回來。  

以下是 Azure 區塊鏈服務所使用的 geth 參數。 您無法調整這些參數。

- 伊斯坦布爾區塊期間：5秒
- 樓層的限制：700000000
- Ceil 天然氣限制：800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>大量的私用交易會降低效能

如果您使用 Azure 區塊鏈 Service 基本層和私人交易，Tessera 可能會當機。

您可以藉由檢查區塊鏈應用程式記錄檔並搜尋訊息，來偵測 Tessera 當機 `Tessera crashed. Restarting Tessera...` 。

當損毀時，Azure 區塊鏈 Service 會重新開機 Tessera。 重新開機大約需要一分鐘的時間。

如果您要傳送大量的私人交易，請使用*標準*層。 使用「基本」層來進行開發、測試和概念證明。 不支援在建立成員之後變更基本和標準間的定價層。

### <a name="calling-ethestimategas-function-reduces-performance"></a>呼叫 estimateGas 函式可降低效能

呼叫*estimateGas*函式多次會大幅減少每秒的交易數。 請不要在每次提交交易時使用*estimateGas*函數。 *EstimateGas*函數會耗用大量記憶體。

可能的話，請使用保守天然氣值來提交交易，並將*estimateGas*的使用降至最低。

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>智慧合約中的未系結迴圈可降低效能

避免智慧型合約中的未系結迴圈，因為它們可以降低效能。 如需詳細資訊，請參閱下列資源：

- [避免無限迴圈](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [智慧合約安全性最佳做法](https://github.com/ConsenSys/smart-contract-best-practices)
- [仲裁所提供的智慧合約指導方針](http://docs.goquorum.com/en/latest/Security/Framework/Decentralized%20Application/Smart%20Contracts%20Security/)
- [密度所提供的天然氣限制和迴圈指導方針](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)
