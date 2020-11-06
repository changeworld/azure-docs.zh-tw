---
title: 新功能 版本資訊-Azure 區塊鏈服務
description: 瞭解 Azure 區塊鏈服務的新功能，例如最新的版本資訊、版本、已知問題，以及即將進行的變更。
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 6a3113a2d28e704b188d701da13493ecd8263cab
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335021"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Azure 區塊鏈服務的新功能

> 藉由複製並貼上此 URL，取得有關何時重新流覽此頁面的通知： `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` 輸入 rss 摘要讀取程式[ ![ rss 摘要讀取器圖示](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522)。

Azure 區塊鏈服務持續不斷改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 新功能
- 版本升級
- 已知問題

---

## <a name="june-2020"></a>2020 年 6 月

### <a name="version-upgrades"></a>版本升級

- 將仲裁版本升級至2.6.0。 使用版本2.6.0 時，您可以傳送已簽署的私用交易。 如需有關傳送私用交易的詳細資訊，請參閱 [仲裁 API 檔](https://docs.goquorum.consensys.net/en/latest/Reference/APIs/ContractExtensionAPIs/#apis)。
- Tessera 版本升級為0.10.5。

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>合約大小和交易大小增加到 128 KB

類型：設定變更

合約大小 (MaxCodeSize) 已增加到 128 KB，讓您可以部署更大的智慧型合約。 此外， (txnSizeLimit) 的交易大小已增加到 128 KB。 設定變更適用于 19 2020 年6月之後 Azure 區塊鏈服務建立的新協會。

### <a name="trietimeout-value-reduced"></a>TrieTimeout 值減少

類型：設定變更

TrieTimeout 值已減少，以便將記憶體內部狀態更頻繁地寫入磁片。 較低的值可確保在發生節點損毀的罕見情況下，能夠更快速地復原節點。

## <a name="may-2020"></a>2020 年 5 月

### <a name="version-upgrades"></a>版本升級

- Ubuntu 版本升級為18.04
- 將仲裁版本升級至2.5。0
- Tessera 版本升級0.10。4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Azure 區塊鏈服務支援傳送 rawPrivate 交易

類型：功能

客戶可以在節點上的帳戶以外的地方簽署私用交易。

### <a name="two-phase-member-provisioning"></a>兩階段成員布建

類型：增強功能

兩個階段有助於將成員以長期現有的聯盟建立的案例進行優化。 成員基礎結構會在第一個階段布建。 在第二個階段中，成員會與區塊鏈同步處理。 兩階段布建有助於避免因為超時而導致成員建立失敗。

## <a name="known-issues"></a>已知問題

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>estimateGas 函式在仲裁 v 2.6.0 中擲回例外狀況

在仲裁 v 2.6.0 中，呼叫 *estimateGas* 函式，但不提供額外的 *值* 參數導致 *方法處理常式* 損毀例外狀況。 仲裁小組已收到通知，而且預計會在2020年7月結束時進行修正。 您可以使用下列因應措施，直到有可用的修正程式為止：

- 避免使用 *estimateGas* ，因為它會影響效能。 如需有關 estimateGas 效能問題的詳細資訊，請參閱 [呼叫 eth. estimateGas 函數可降低效能](#calling-ethestimategas-function-reduces-performance)。 為每筆交易包含天然氣值。 大部分的程式庫都會呼叫 estimateGas，如果未提供加油站值，導致仲裁 v 2.6.0 損毀。
- 如果您需要呼叫 *estimateGas* ，仲裁團隊建議您將額外的參數 *值* 當作 *0* 傳遞，以作為因應措施。

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>如果有少於四個驗證程式節點，則會停止進行

生產網路至少應有四個驗證程式節點。 仲裁建議至少需要四個驗證程式節點，以符合 IBFT 損毀容錯 (3F + 1) 。 您應該至少有兩個 Azure 區塊鏈服務 *標準* 層節點，才能取得四個驗證程式節點。 標準節點會使用兩個驗證程式節點進行布建。  

如果 Azure 區塊鏈服務上的區塊鏈網路沒有四個驗證程式節點，則可能會在網路上停止進行挖掘。 您可以藉由在已處理的區塊上設定警示，來偵測已停止的挖掘。 在狀況良好的網路中，已處理的區塊將會是每個節點每五分鐘60個區塊。

作為緩和措施，Azure 區塊鏈服務團隊必須重新開機節點。 客戶必須開啟支援要求才能重新開機節點。 Azure 區塊鏈服務團隊正在努力自動偵測和修正挖掘問題。

使用「標準」層來進行生產等級的部署。 使用「基本」層來進行開發、測試和概念證明。 不支援在建立成員之後變更基本和標準間的定價層。

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>區塊鏈資料管理員需要標準層節點

如果您使用區塊鏈資料管理員，請使用 *標準* 層。 *基本* 層僅具有 4 GB 的記憶體。 因此，它無法調整為區塊鏈資料管理員和其上執行的其他服務所需的使用量。

使用「基本」層來進行開發、測試和概念證明。 不支援在建立成員之後變更基本和標準間的定價層。

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>大量的解除鎖定帳戶呼叫造成 geth 損毀

提交交易時，大量的解除鎖定帳戶呼叫可能會導致 geth 在交易節點上損毀。 因此，您必須停止擷取的交易。 否則，暫止的交易佇列會增加。

Geth 會在不到一分鐘內自動重新開機。 視節點而定，同步處理可能需要很長的時間。 Azure 區塊鏈服務團隊正在啟用封存功能，可減少同步的時間。

若要識別 geth 損毀，您可以在應用程式記錄檔的區塊鏈訊息中檢查記錄檔中是否有任何錯誤訊息。 您也可以在擱置交易增加時，檢查處理的區塊是否減少。

若要緩解此問題，請傳送已簽署的交易，而不是使用可解除鎖定帳戶的命令來傳送未簽署的交易。 對於已在外部簽署的交易，不需要解除鎖定帳戶。

如果您想要傳送未簽署的交易，請將0傳送為 unlock 命令中的 time 參數，以將帳戶解除鎖定無限時間。 您可以在提交所有交易之後，將帳戶鎖定回來。  

以下是 Azure 區塊鏈服務使用的 geth 參數。 您無法調整這些參數。

- 伊斯坦布爾區塊期間：5秒
- 樓層氣限制：700000000
- Ceil 天然氣限制：800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>大量私用交易可減少效能

如果您使用 Azure 區塊鏈服務基本層和私用交易，Tessera 可能會損毀。

您可以藉由查看區塊鏈應用程式記錄檔並搜尋訊息，來偵測 Tessera 損毀 `Tessera crashed. Restarting Tessera...` 。

Azure 區塊鏈服務在發生損毀時重新開機 Tessera。 重新開機大約需要一分鐘的時間。

如果您要傳送大量的私用交易，請使用 *標準* 層。 使用「基本」層來進行開發、測試和概念證明。 不支援在建立成員之後變更基本和標準間的定價層。

### <a name="calling-ethestimategas-function-reduces-performance"></a>呼叫 estimateGas 函數可減少效能

多次呼叫 *estimateGas* 函式會大幅減少每秒的交易數。 請勿針對每個提交的交易使用 *estimateGas* 函數。 *EstimateGas* 函式需要記憶體密集的功能。

可能的話，請使用保守的天然氣價值來提交交易，並將 *estimateGas* 的使用降至最低。

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>智慧型合約中的未系結迴圈可減少效能

避免智慧型合約中的未系結迴圈，因為它們可以降低效能。 如需詳細資訊，請參閱下列資源：

- [避免未系結迴圈](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [智慧型合約安全性最佳作法](https://github.com/ConsenSys/smart-contract-best-practices)
- [仲裁提供的智慧型合約指導方針](https://docs.goquorum.consensys.net/en/stable/Concepts/Security/Framework/DecentralizedApplication/SmartContractsSecurity/)
- [密度提供的天然氣限制和迴圈指導方針](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)