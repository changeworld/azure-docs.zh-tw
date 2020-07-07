---
title: Azure 區塊鏈服務開發總覽
description: 在 Azure 區塊鏈 Service 上開發解決方案的簡介。
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80348370"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure 區塊鏈服務開發總覽

您可以使用 Azure 區塊鏈 Service 建立聯盟區塊鏈網路，以啟用企業案例，例如資產追蹤、數位權杖、忠誠度和報酬、供應鏈財務和來源。 下列各節介紹用來執行企業區塊鏈解決方案的 Azure 區塊鏈服務開發。

## <a name="connecting-to-azure-blockchain-service"></a>連接到 Azure 區塊鏈 Service

區塊鏈網路有不同類型的用戶端，包括完整節點、光線節點和遠端用戶端。 Azure 區塊鏈 Service 會建立包含節點的區塊鏈網路。 您可以使用不同的用戶端作為 Azure 區塊鏈 Service 的閘道，以進行區塊鏈開發。 Azure 區塊鏈 Service 提供基本驗證或存取金鑰作為開發端點。 以下是您可以使用 connect 的熱門用戶端。

### <a name="visual-studio-code"></a>Visual Studio Code

您可以使用 Azure 區塊鏈開發套件 Visual Studio Code 延伸模組，連接到聯盟成員。 在連線至聯盟後，您就可以對 Azure 區塊鏈服務聯盟成員編譯、建置和部署智慧型合約。

若要開發精密的企業區塊鏈解決方案，需要開發架構來連接到不同的區塊鏈網路，並管理智慧型合約生命週期。 大部分的專案都與至少兩個區塊鏈節點互動。 開發人員在開發期間使用本機區塊鏈。 當應用程式準備好進行測試或發行時，開發人員會部署到區塊鏈網路。 例如，主要的公用乙太坊網路或 Azure 區塊鏈服務。 Visual Studio Code 中的乙太坊擴充功能 Azure 區塊鏈開發套件使用 Truffle。 Truffle 是一種熱門的區塊鏈開發架構，可在乙太坊區塊鏈上撰寫、編譯、部署及測試分散的應用程式。 您也可以將 Truffle 視為一種架構，它會嘗試順暢地整合智慧型合約開發和傳統 網頁程式開發。

如需詳細資訊，請參閱[快速入門：使用 Visual Studio Code 連接到 Azure 區塊鏈 Service 聯盟網路](connect-vscode.md)。

### <a name="metamask"></a>MetaMask

MetaMask 是以瀏覽器為基礎的錢包（遠端用戶端）、RPC 用戶端和基本的合約瀏覽器。 不同于其他瀏覽器錢包，MetaMask 會將 web3 實例插入瀏覽器 JavaScript 內容中，做為連接到各種乙太坊區塊鏈（*mainnet*、 *Ropsten testnet*、 *Kovan testnet*、LOCAL RPC node 等）的 RPC 用戶端。 您可以輕鬆設定自訂 RPC 來連線到 Azure 區塊鏈 Service，並使用 Remix 開始區塊鏈開發。

如需詳細資訊，請參閱[快速入門：使用 MetaMask 連接和部署智慧合約](connect-metamask.md)

### <a name="geth"></a>Geth

Geth 是命令列介面，可執行在 Go 中執行的完整乙太坊節點。 您不需要執行完整節點，而可以啟動其互動式主控台，以提供公開 JavaScript API 的 JavaScript 執行時間環境來與 Azure 區塊鏈 Service 互動。

如需詳細資訊，請參閱[快速入門：使用 Geth 附加至 Azure 區塊鏈 Service 交易節點](connect-geth.md)。

## <a name="ethereum-quorum-private-transactions"></a>乙太坊仲裁私人交易

仲裁是以乙太坊為基礎的分散式總帳通訊協定，具有交易加上合約隱私權和新的共識機制。 乙太坊的主要增強功能包括：

* **隱私權**-仲裁透過公用和私用狀態分隔來支援私用交易和私人合約，並利用對等加密訊息交換，將私人資料導向至網路參與者。
* **替代的共識機制**-許可網路不需要對工作進行證明或因應意見證明。 仲裁提供多種針對聯盟鏈（例如次浮動定位和 IBFT）所設計的共識機制。Azure 區塊鏈 Service 使用 IBFT 共識機制。
* **對等**互連：使用智慧合約的節點和對等人才可確保只有已知的合作物件可以加入網路。
* **較高的效能**-仲裁提供比公用 Geth 更高的效能。

## <a name="block-explorers"></a>封鎖流覽

封鎖瀏覽器是可顯示個別區塊內容、交易位址資料和歷程記錄的線上區塊鏈。 基本的區塊資訊可透過 Azure 區塊鏈 Service 中的 Azure 監視器取得。 不過，如果您在開發期間需要更多詳細資訊，封鎖流覽流覽會很有用。  下列區塊流覽人員可與 Azure 區塊鏈 Service 搭配使用：

* 從 Web3 Labs [Epirus Azure 區塊鏈 Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview)
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

您也可以使用區塊鏈資料管理員和 Azure Cosmos DB 來建立自己的區塊瀏覽器，請參閱[教學課程：使用區塊鏈資料管理員將資料傳送至 Azure Cosmos DB](data-manager-cosmosdb.md)。

## <a name="tps-measurement"></a>TPS 測量

當區塊鏈在更多企業案例中使用時，每秒交易數（TP）速度非常重要，以避免發生瓶頸和系統效率不佳的情況。 高交易率可能很容易在非集中式區塊鏈中維護。 精確的 TP 量測可能會受到不同因素的影響，例如伺服器執行緒、交易佇列大小、網路延遲和安全性。 如果您需要在開發期間測量 TPS 速度，則會[ChainHammer](https://github.com/drandreaskrueger/chainhammer)熱門的開放原始碼工具。

## <a name="next-steps"></a>後續步驟

嘗試使用 Azure 區塊鏈開發套件乙太坊的快速入門，以附加至 Azure 區塊鏈 Service 上的聯盟。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 連線至聯盟網路](connect-vscode.md)