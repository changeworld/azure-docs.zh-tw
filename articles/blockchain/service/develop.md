---
title: Azure 區塊鏈服務開發概述
description: 有關開發 Azure 區塊鏈服務解決方案的介紹。
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348370"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure 區塊鏈服務開發概述

借助 Azure 區塊鏈服務，您可以創建聯合區塊鏈網路，以啟用企業方案，如資產跟蹤、數位權杖、忠誠度和獎勵、供應鏈財務和來源。 以下各節介紹用於實現企業區塊鏈解決方案的 Azure 區塊鏈服務開發。

## <a name="connecting-to-azure-blockchain-service"></a>連接到 Azure 區塊鏈服務

區塊鏈網路有不同類型的用戶端，包括完整節點、光節點和遠端用戶端。 Azure 區塊鏈服務構建包含節點的區塊鏈網路。 您可以使用不同的用戶端作為 Azure 區塊鏈服務的閘道，用於區塊鏈開發。 Azure 區塊鏈服務作為開發終結點提供基本驗證或訪問金鑰。 以下是可以使用連接的熱門用戶端。

### <a name="visual-studio-code"></a>Visual Studio Code

您可以使用 Azure 區塊鏈開發工具組視覺化工作室代碼擴展連接到聯合成員。 在連線至聯盟後，您就可以對 Azure 區塊鏈服務聯盟成員編譯、建置和部署智慧型合約。

為了開發複雜的企業區塊鏈解決方案，需要一個開發框架來連接到不同的區塊鏈網路並管理智慧合同生命週期。 大多數專案至少與兩個區塊鏈節點交互。 開發人員在開發過程中使用本地區塊鏈。 當應用程式準備好進行測試或發佈時，開發人員將部署到區塊鏈網路。 例如，主要公共以地庭網路或 Azure 區塊鏈服務。 Visual Studio 代碼中用於以地庭擴展的 Azure 區塊鏈開發工具組使用 Truffle。 Truffle 是一個流行的區塊鏈開發框架，用於在以泰倫區塊鏈上編寫、編譯、部署和測試分散的應用程式。 您還可以將 Truffle 視為一個框架，試圖無縫集成智慧合約開發和傳統 Web 開發。

有關詳細資訊，請參閱[快速入門：使用視覺化工作室代碼連接到 Azure 區塊鏈服務聯合體網路](connect-vscode.md)。

### <a name="metamask"></a>元框

MetaMask 是基於瀏覽器的錢包（遠端用戶端）、RPC 用戶端和基本合同資源管理器。 與其他瀏覽器錢包不同，MetaMask 將 Web3 實例注入瀏覽器 JavaScript 上下文，充當 RPC 用戶端，連接到各種乙太坊區塊鏈（*主網**、Ropsten 測試網*、*科萬測試網*、本地 RPC 節點等）。 您可以輕鬆設置自訂 RPC 以連接到 Azure 區塊鏈服務，並使用混音啟動區塊鏈開發。

有關詳細資訊，請參閱[快速入門：使用 MetaMask 連接和部署智慧合約](connect-metamask.md)

### <a name="geth"></a>Geth

Geth 是運行在 Go 中實現的完整以非庭節點的命令列介面。 您無需運行完整節點，但可以啟動其互動式主控台，該主控台提供一個 JavaScript 運行時環境，公開 JavaScript API 與 Azure 區塊鏈服務進行交互。

有關詳細資訊，請參閱[快速入門：使用 Geth 附加到 Azure 區塊鏈服務事務節點](connect-geth.md)。

## <a name="ethereum-quorum-private-transactions"></a>以天特魯姆私人交易

仲裁是一種基於以裡瑟姆的分散式分類帳協定，具有事務加上合同隱私和新的共識機制。 Go-以地庭的主要增強功能包括：

* **隱私**- Quorum 通過公共和私有狀態分離支援私人交易和私人合同，並利用點對點加密消息交換將私人資料傳輸給網路參與者。
* **其他共識機制**- 工作證明或風險證明共識不需要許可網路。 Quorum 為 RAFT 和 IBFT 等財團鏈提供了多種共識機制。Azure 區塊鏈服務使用 IBFT 共識機制。
* 使用智慧合約**的對等許可權**節點和對等許可權可確保只有已知方才能加入網路.
* **更高的性能**- Quorum 提供比公共 Geth 更高的性能。

## <a name="block-explorers"></a>阻止資源管理器

塊資源管理器是線上區塊鏈瀏覽器，顯示單個塊內容、事務位址資料和歷史記錄。 基本塊資訊可通過 Azure 區塊鏈服務中的 Azure 監視器獲得。 但是，如果您在開發過程中需要更詳細的資訊，則塊資源管理器可能很有用。  以下塊資源管理器與 Azure 區塊鏈服務配合使用：

* 來自 Web3 實驗室的[Epirus Azure 區塊鏈服務資源管理器](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview)
* [塊Scout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

您還可以使用區塊鏈資料管理器和 Azure Cosmos DB 構建自己的塊資源管理器，請參閱[教程：使用區塊鏈資料管理器將資料發送到 Azure Cosmos DB](data-manager-cosmosdb.md)。

## <a name="tps-measurement"></a>TPS 測量

由於區塊鏈用於更多的企業方案，每秒交易 （TPS） 速度對於避免瓶頸和系統效率低下非常重要。 高事務速率可能很難在分散的區塊鏈中維護。 準確的 TPS 測量可能受不同因素的影響，例如伺服器執行緒、事務佇列大小、網路延遲和安全性。 如果您需要在開發過程中測量 TPS 速度，一個流行的開源工具是[鏈哈默](https://github.com/drandreaskrueger/chainhammer)。

## <a name="next-steps"></a>後續步驟

嘗試使用以地坊的 Azure 區塊鏈開發工具組快速入門，以附加到 Azure 區塊鏈服務上的聯合體。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 連線至 Azure 區塊鏈服務](connect-vscode.md)