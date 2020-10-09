---
title: Azure 區塊鏈服務開發總覽
description: 在 Azure 區塊鏈服務上開發解決方案的簡介。
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80348370"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure 區塊鏈服務開發總覽

有了 Azure 區塊鏈服務，您可以建立聯盟區塊鏈網路來啟用企業案例，例如資產追蹤、數位權杖、忠誠度和報酬、供應鏈財務和來源。 下列各節介紹執行企業區塊鏈解決方案的 Azure 區塊鏈服務開發。

## <a name="connecting-to-azure-blockchain-service"></a>連接到 Azure 區塊鏈服務

區塊鏈網路有不同類型的用戶端，包括完整節點、淺色節點和遠端用戶端。 Azure 區塊鏈服務建立包含節點的區塊鏈網路。 您可以使用不同的用戶端做為您的閘道，以 Azure 區塊鏈服務進行區塊鏈開發。 Azure 區塊鏈服務提供基本驗證或存取金鑰作為開發端點。 以下是常用的用戶端，您可以使用 connect。

### <a name="visual-studio-code"></a>Visual Studio Code

您可以使用 Azure 區塊鏈開發工具組 Visual Studio Code 擴充功能連接到聯盟成員。 在連線至聯盟後，您就可以對 Azure 區塊鏈服務聯盟成員編譯、建置和部署智慧型合約。

若要開發精密的企業區塊鏈解決方案，需要開發架構才能連線至不同的區塊鏈網路，以及管理智慧型合約生命週期。 大部分的專案會與至少兩個區塊鏈節點互動。 開發人員在開發期間使用本機區塊鏈。 當應用程式準備好進行測試或發行時，開發人員會部署至區塊鏈網路。 例如，主要公用乙太坊網路或 Azure 區塊鏈服務。 適用于乙太坊 Visual Studio Code 延伸模組的 Azure 區塊鏈開發工具組會使用 Truffle。 Truffle 是一種熱門的區塊鏈開發架構，可在乙太坊區塊鏈上撰寫、編譯、部署及測試分散式應用程式。 您也可以將 Truffle 視為可嘗試順暢地整合智慧型合約開發和傳統 網頁程式開發的架構。

如需詳細資訊，請參閱 [快速入門：使用 Visual Studio Code 連接到 Azure 區塊鏈服務聯盟網路](connect-vscode.md)。

### <a name="metamask"></a>MetaMask

MetaMask 是以瀏覽器為基礎的錢包 (遠端用戶端) 、RPC 用戶端和基本的合約瀏覽器。 不同于其他瀏覽器錢包，MetaMask 會將 web3 實例插入瀏覽器 JavaScript 內容，做為連接到各種乙太坊區塊鏈 (*mainnet*、 *Ropsten >testnet.treyresearch.net*、 *Kovan >testnet.treyresearch.net*、本機 RPC 節點等 ) 的 RPC 用戶端。 您可以輕鬆地設定自訂 RPC 以連接到 Azure 區塊鏈服務，並使用 Remix 開始區塊鏈開發。

如需詳細資訊，請參閱 [快速入門：使用 MetaMask 連接及部署智慧型合約](connect-metamask.md)

### <a name="geth"></a>Geth

Geth 是命令列介面，可用於執行在 Go 中執行的完整乙太坊節點。 您不需要執行完整節點，但可以啟動其互動式主控台，以提供 JavaScript 執行時間環境，以公開 JavaScript API 來與 Azure 區塊鏈服務互動。

如需詳細資訊，請參閱 [快速入門：使用 Geth 附加至 Azure 區塊鏈服務的交易節點](connect-geth.md)。

## <a name="ethereum-quorum-private-transactions"></a>乙太坊仲裁私用交易

仲裁是以乙太坊為基礎的分散式總帳通訊協定，具有交易加上合約隱私權和新的共識機制。 Go-Ethereum 的主要增強功能包括：

* **隱私權** -仲裁透過公開和私用狀態分隔支援私用交易和私用合約，並使用對等的加密訊息交換，將私用資料的導向傳輸到網路參與者。
* 許可網路不需要**替代的共識機制**，也就是工作證明或有考驗的共識。 仲裁提供多個針對聯盟鏈（例如次浮動定位和 IBFT）設計的共識機制。Azure 區塊鏈服務使用 IBFT 的共識機制。
* **對等** 互連-使用智慧型合約的節點和對等互連可確保只有已知的合作物件可以加入網路。
* **較高的效能** -仲裁提供比公用 Geth 更高的效能。

## <a name="block-explorers"></a>封鎖流覽區

封鎖瀏覽器是線上區塊鏈瀏覽器，可顯示個別的區塊內容、交易位址資料和歷程記錄。 基本區塊資訊可透過 Azure 區塊鏈服務中的 Azure 監視器取得。 但是，如果您在開發期間需要更多詳細資訊，封鎖的流覽人員可能會很有用。  下列區塊流覽區會使用 Azure 區塊鏈服務：

* 從 Web3 Labs [Epirus Azure 區塊鏈服務 Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview)
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

您也可以使用區塊鏈資料管理員和 Azure Cosmos DB 來建立自己的區塊 explorer，請參閱 [教學課程：使用區塊鏈資料管理員將資料傳送至 Azure Cosmos DB](data-manager-cosmosdb.md)。

## <a name="tps-measurement"></a>TPS 測量

由於區塊鏈是在更多企業案例中使用，因此每秒交易 (TPS) 速度很重要，以避免瓶頸和系統效率不佳。 高交易率在非集中式區塊鏈內可能很難維護。 精確的 TPS 測量可能會受到不同因素的影響，例如伺服器執行緒、交易佇列大小、網路延遲和安全性。 如果您需要在開發期間測量 TPS 速度，常用的開放原始碼工具為 [ChainHammer](https://github.com/drandreaskrueger/chainhammer)。

## <a name="next-steps"></a>後續步驟

使用適用于乙太坊的 Azure 區塊鏈開發工具組嘗試快速入門，以 Azure 區塊鏈服務上的聯盟。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 連線至聯盟網路](connect-vscode.md)