---
title: Azure 區塊鏈服務概觀
description: Azure 區塊鏈服務的概觀
ms.date: 05/22/2020
ms.topic: overview
ms.reviewer: ravastra
ms.openlocfilehash: e82916d7366ad82746d2d3ddfc979264e113619b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334815"
---
# <a name="what-is-azure-blockchain-service"></a>什麼是 Azure 區塊鏈服務？

Azure 區塊鏈服務是完全受控的總帳服務，讓使用者能夠在 Azure 中大規模擴充及操作區塊鏈網路。 透過為基礎結構管理以及區塊鏈網路治理提供統一的控制，Azure 區塊鏈服務提供：

* 簡單的網路部署和作業
* 內建的聯盟管理
* 使用熟悉的開發工具來開發智慧型合約

Azure 區塊鏈服務旨在支援多個總帳通訊協定。 目前使用 [Istanbul Byzantine 容錯 (IBFT)](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) 共識機制來提供對 Ethereum [Quorum](https://www.goquorum.com/) 總帳的支援。

這些功能幾乎不需要管理，而且免費提供。 您可以專注於應用程式開發和商務邏輯，而非分配時間和資源來管理虛擬機器和基礎結構。 此外，您可以繼續使用所選擇的開放原始碼工具及平台，來開發應用程式以傳遞您的解決方案，而不需學習新技能。

## <a name="network-deployment-and-operations"></a>網路部署和作業

部署 Azure 區塊鏈服務是透過 Azure 入口網站、Azure CLI，或透過 Azure 區塊鏈擴充功能的 Visual Studio Code 來完成。 部署已簡化，包括佈建交易和驗證程式節點、適用於安全性隔離的 Azure 虛擬網路，以及服務管理的儲存體。  此外，部署新的區塊鏈成員時，使用者也會建立或加入聯盟。  聯盟讓不同 Azure 訂用帳戶中的多個合作對象能夠在共用區塊鏈上彼此安全地通訊。  這個簡化的部署可將區塊鏈網路部署從數天降至數分鐘。

### <a name="performance-and-service-tiers"></a>效能和服務層級

Azure 區塊鏈服務提供兩個服務層級：「基本」和「標準」。 每一層均提供不同的效能和功能，以支援精簡型開發並測試工作負載，最高可達大規模的生產區塊鏈部署。 使用「基本」層來進行開發、測試和概念證明。 使用「標準」層來進行生產等級的部署。 這兩個層級至少包含一個交易節點，以及一個驗證程式節點 (基本) 或兩個驗證程式節點 (標準)。 

![定價層](./media/overview/pricing-tiers.png)

除了提供兩個驗證程式節點，「標準」層可為每個交易和驗證程式節點提供兩個「虛擬核心」，而「基本」層可提供 1 個虛擬核心設定。  藉由為交易和驗證程式節點提供 2 個虛擬核心，即可將 1 個虛擬核心專用於 Quorum 總帳，而剩餘的 1 個虛擬核心可用於其他基礎結構相關服務，以確保會針對生產區塊鏈工作負載取得最佳效能。 如需價格詳細資料的詳細資訊，請參閱 [Azure 區塊鏈服務價格](https://azure.microsoft.com/pricing/details/blockchain-service)。

### <a name="security-and-maintenance"></a>安全性和維護

佈建第一個區塊鏈成員之後，您就能夠將其他交易節點新增至成員。  根據預設，交易節點會透過防火牆規則來保護，且必須設定以供存取。  此外，所有交易節點都會透過 TLS 來加密移動中的資料。  有多個選項可用來保護交易節點存取，包括防火牆規則、基本驗證、存取金鑰，以及 Azure Active Directory 整合。 如需詳細資訊，請參閱[設定交易節點](configure-transaction-nodes.md)和[設定 Azure Active Directory 存取](configure-aad.md)。

Azure 區塊鏈服務是一個受控服務，可確保您的區塊鏈成員節點會以最新的主機作業系統和總帳軟體堆疊更新進行修補，並設定為高可用性 (僅限標準層)，從而消除了傳統 IaaS 區塊鏈節點所需的許多 DevOps。  如需修補和更新的詳細資訊，請參閱[支援的 Azure 區塊鏈服務總帳版本](ledger-versions.md)。

### <a name="monitoring-and-logging"></a>監視和記錄

此外，Azure 區塊鏈服務可透過 Azure 監視器服務提供豐富的計量，進而提供節點的 CPU、記憶體和儲存體使用量深入解析。  Azure 監視器也會提供區塊鏈網路活動的實用深入解析，例如已挖掘的交易和區塊、交易佇列深度和作用中連線。  您可以自訂計量，以檢視對區塊鏈應用程式很重要的深入解析。  此外，您也可以透過警示來定義閾值，讓使用者可以觸發動作，例如，傳送電子郵件或簡訊、執行邏輯應用程式、Azure 函式或傳送至自訂定義的 Webhook。

![此螢幕擷取畫面顯示監視，其中包含區塊、交易、暫停交易和已處理要求的值。](./media/overview/metrics.png)

透過 Azure Log Analytics，使用者可以檢視與 Quorum 總帳相關的記錄或其他重要資訊，例如，已嘗試連線到交易節點。

## <a name="built-in-consortium-management"></a>內建的聯盟管理

部署第一個區塊鏈成員時，您可以加入或建立新的聯盟。  聯盟是一個邏輯群組，可用來管理在多方流程中進行交易之區塊鏈成員間的治理和連線能力。  Azure 區塊鏈服務透過預先定義的智慧型合約來提供內建的治理控制項，以決定聯盟中的成員可採取哪些動作。  這些治理控制項可以根據該聯盟系統管理員的需要來自訂。 當您建立新的聯盟時，您的區塊鏈成員即為該聯盟的預設系統管理員，讓您能夠邀請其他合作對象來加入您的聯盟。  您只有在先前已受邀的情況下，才能加入聯盟。  加入聯盟時，您的區塊鏈成員會受限於聯盟系統管理員所放置的治理控制項。

![聯盟管理](./media/overview/consortium.png)

您可以透過 PowerShell 和 REST API 來存取聯盟管理動作，例如，新增和移除聯盟中的成員。 您可以使用通用介面以程式設計方式管理聯盟，而不需修改並提交以 Solidity 為基礎的智慧型合約。 如需詳細資訊，請參閱[聯盟管理](consortium.md)。

## <a name="develop-using-familiar-development-tools"></a>使用熟悉的開發工具進行開發

根據開放原始碼的 Quorum Ethereum 總帳，您可以使用與現有 Ethereum 應用程式相同的方式，來開發適用於 Azure 區塊鏈服務的應用程式。 透過與領先業界的夥伴合作，Azure 區塊鏈開發套件 Visual Studio Code 擴充功能可讓開發人員運用熟悉的工具 (例如 Truffle Suite) 來建置智慧型合約。 您可以使用 Azure 區塊鏈 Visual Studio Code 擴充功能，來建立或連線到現有的聯盟，讓您可以從一個 IDE 建置和部署智慧型合約。 如需詳細資訊，請參閱 [VS Code Marketplace 中的 Azure 區塊鏈開發套件](https://aka.ms/vscodebcextension) \(英文\) 和 [Azure 區塊鏈開發套件使用者指南](https://aka.ms/vscodebcextensionwiki) \(英文\)。

## <a name="publish-blockchain-data"></a>發佈區塊鏈資料

Azure 區塊鏈服務的區塊鏈資料管理員會將 Azure 區塊鏈服務交易資料擷取、轉換及傳遞至 Azure 事件方格主題，以提供可靠且可擴充區塊鏈總帳與 Azure 服務的整合。 您可以使用區塊鏈資料管理員來整合離鏈應用程式和資料存放區。 如需詳細資訊，請參閱[適用於 Azure 區塊鏈服務的區塊鏈資料管理員](data-manager.md)。

## <a name="support-and-feedback"></a>支援與意見反應

如需 Azure 區塊鏈服務消息，請瀏覽 [Azure 區塊鏈服務部落格](https://azure.microsoft.com/blog/topics/blockchain/)，即時掌握 Azure 區塊鏈服務工程小組提供的區塊鏈服務供應項目和最新資訊。

若要提供產品意見反應或要求新功能，請透過 [Azure 區塊鏈服務的意見反應論壇](https://aka.ms/blockchainuservoice) \(英文\) 張貼或票選想法。

### <a name="community-support"></a>社群支援

洽詢 Microsoft 工程師和 Azure 區塊鏈服務社群專家。

* [Azure 區塊鏈服務的 Microsoft 問與答頁面](/answers/topics/azure-blockchain-service.html)
* [Microsoft 技術社群](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-service)

## <a name="next-steps"></a>後續步驟

若要開始使用，請嘗試使用快速入門，或從這些資源中尋找更多詳細資料。
* [使用 Azure 入口網站建立區塊鏈成員](create-member.md)或[使用 Azure CLI 建立區塊鏈成員](create-member-cli.md)
* 如需成本比較和計算機，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/blockchain-service)。
* 使用 [Azure 區塊鏈開發套件](https://github.com/Azure-Samples/blockchain-devkit) \(英文\) 建置您的第一個應用程式
* Azure 區塊鏈 VSCode 擴充功能[使用者指南](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) \(英文\)
