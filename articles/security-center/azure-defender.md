---
title: Azure Defender 和可用方案的概觀
description: 了解 Azure Defender 的方案、保護和警示。 然後，在您的訂用帳戶上啟用 Azure Defender。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5a5b96d5a9ea6aa05da30238690b8f5fa745b3f3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448419"
---
# <a name="introduction-to-azure-defender"></a>Azure Defender 簡介

Azure 資訊安全中心的功能涵蓋雲端安全性的兩大要素：

- **雲端安全性態勢管理 (CSPM)**
- **雲端工作負載保護 (CWP)**

資訊安全中心的 CSPM 功能 (例如安全分數、偵測 Windows 和 Linux Azure 機器中的安全性錯誤設定)，全都可供所有 Azure 使用者在資訊安全中心內免費體驗。 使用這些 CSPM 功能可強化安全性狀態，並確保能夠符合法規。

**Azure Defender** 是整合於資訊安全中心的雲端工作負載保護平台 (CWPP)，可為您的 Azure 和混合式工作負載提供進階的智慧型保護功能。

這是 Azure 資訊安全中心的 Azure Defender 儀表板：

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Azure Defender 儀表板的範例" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Azure Defender 可保護哪些資源類型？

Azure Defender 可為虛擬機器、SQL 資料庫、容器、Web 應用程式及網路等項目提供安全性警示和進階威脅防護。

當您從 Azure 資訊安全中心的 [定價和設定] 區域啟用 Azure Defender 時，下列 Defender 方案會同時啟用，並為您環境的計算、資料和服務層提供全面的防護：

- [適用於伺服器的 Azure Defender](defender-for-servers-introduction.md)
- [適用於 App Service 的 Azure Defender](defender-for-app-service-introduction.md)
- [適用於儲存體的 Azure Defender](defender-for-storage-introduction.md)
- [適用於 SQL 的 Azure Defender](defender-for-sql-introduction.md)
- [適用於 IoT 的 Azure Defender](defender-for-iot-introduction.md)
- [適用於 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)
- [適用於容器登錄的 Azure Defender](defender-for-container-registries-introduction.md)
- [適用於 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)

這些方案會分別在資訊安全中心文件中說明。


## <a name="hybrid-cloud-protection"></a>混合式雲端保護

除了保護您的 Azure 環境以外，您還可以將 Azure Defender 功能新增至混合式雲端環境：

- 保護您的非 Azure 伺服器
- 保護其他雲端中的虛擬機器 (例如 AWS 和 GCP)
- 保護您的 IoT 裝置

您將可根據本身的特定環境自訂威脅情報及排定警示的優先順序，以全心處理最重要的事務

部署 [Azure Arc](https://azure.microsoft.com/services/azure-arc/)，讓 Azure Defender 將保護延伸至內部部署和多重雲端虛擬機器與 SQL 資料庫。 適用於伺服器的 Azure Arc 是免費服務，但在已啟用 Arc 的伺服器上使用的服務 (例如 Azure Defender) 將依據該服務的定價收費。

[深入了解 Azure Arc](https://docs.microsoft.com/azure/azure-arc/overview)。


## <a name="azure-defender-alerts"></a>Azure Defender 警示 

Azure Defender 在您環境的任何區域中偵測到威脅時，將會產生警示。 這些警示會說明受影響資源的詳細資料、建議的補救步驟，以及在某些情況下會說明觸發邏輯應用程式以進行回應的選項。

無論是由資訊安全中心產生的警示，還是資訊安全中心從整合式安全性產品接收到的警示，您都可以將其匯出。 若要將您的警示匯出至 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，請依照[將警示匯出至 SIEM](continuous-export.md) 中的指示操作。

> [!NOTE]
> 來自不同來源的警示顯示的所需時間可能不相同。 例如，需要分析網路流量的警示在顯示時，可能會比在虛擬機器上執行的可疑程序所產生的相關警示耗時。


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender 進階保護功能

Azure Defender 會使用進階分析產生與您的資源有關的專屬建議。 

其保護機制包括使用 Just-In-Time 存取來保護 VM 的管理連接埠，以及利用自適性應用程式控制建立允許清單，指定可以和不應在您的機器上執行的應用程式。 

使用 Azure Defender 儀表板中的進階保護圖格，可監視和設定各個保護功能。 

## <a name="vulnerability-assessment-and-management"></a>弱點評量和管理

Azure Defender 包含對您的虛擬機器和容器登錄進行弱點掃描的功能，不需額外收費。 掃描器的技術由 Qualys 提供，但您不需要 Qualys 授權或 Qualys 帳戶，一切都可在資訊安全中心內順暢進行。 

您可以檢閱這些弱點掃描器發現的結果，並直接從資訊安全中心加以因應。 如此，資訊安全中心就更像是能統籌處理您所有雲端安全工作的單一窗口了。

深入了解下列頁面的資訊：

- [資訊安全中心的 Azure 虛擬機器整合式弱點評量解決方案](deploy-vulnerability-assessment-vm.md)
- [識別 Azure 容器登錄中的映像有何弱點](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>後續步驟

在本文中，您已了解 Azure Defender 的優點。 

> [!div class="nextstepaction"]
> [啟用 Azure Defender](security-center-pricing.md)