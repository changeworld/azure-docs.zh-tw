---
title: Azure Defender 和可用方案的總覽
description: 瞭解 Azure Defender 的方案、保護和警示。 然後繼續在您的訂用帳戶上啟用 Azure Defender。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bb1c1e6443b5087b48aad7c3171bef557707adb1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977294"
---
# <a name="introduction-to-azure-defender"></a>Azure Defender 簡介

Azure 資訊安全中心的功能涵蓋兩個雲端安全性的主要要素：

- **雲端安全性狀態管理 (CSPM) **
- **雲端工作負載保護 (CWP) **

安全性中心的 CSPM 功能，例如安全分數、偵測 Windows 和 Linux Azure 電腦中的安全性錯誤配置，全都是所有 Azure 使用者都能使用的免費安全性中心體驗的一部分。 使用這些 CSPM 功能來增強您的狀態，並確保法規遵循。

**Azure Defender** 是雲端工作負載保護平臺 (CWPP) 整合在安全中心內，以進行 Azure 和混合式工作負載的先進、智慧型、保護。

這是 Azure 資訊安全中心中的 Azure Defender 儀表板：

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Azure Defender 儀表板的範例" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Azure Defender 可以保護哪些資源類型？

Azure Defender 針對虛擬機器、SQL 資料庫、容器、web 應用程式、網路等提供安全性警示和先進的威脅防護。

當您從 Azure 資訊安全中心的 [ **定價和設定** ] 區域啟用 Azure defender 時，會同時啟用下列 Defender 方案，並為您環境的計算、資料和服務層提供完整的防禦措施：

- [適用於伺服器的 Azure Defender](defender-for-servers-introduction.md)
- [適用於 App Service 的 Azure Defender](defender-for-app-service-introduction.md)
- [適用於儲存體的 Azure Defender](defender-for-storage-introduction.md)
- [適用於 SQL 的 Azure Defender](defender-for-sql-introduction.md)
- [適用於 IoT 的 Azure Defender](defender-for-iot-introduction.md)
- [適用於 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)
- [適用於容器登錄的 Azure Defender](defender-for-container-registries-introduction.md)
- [適用於 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)

這些方案中的每一個都會分別在「安全性中心」檔中說明。


## <a name="hybrid-cloud-protection"></a>混合式雲端保護

除了保護您的 Azure 環境，您還可以將 Azure Defender 功能新增至您的混合式雲端環境：

- 保護您的非 Azure 伺服器
- 保護其他雲端中的虛擬機器 (例如 AWS 和 GCP) 
- 保護您的 IoT 裝置

您將會根據您的特定環境取得自訂的威脅情報和優先警示，讓您可以專注于最重要的內容

部署 [Azure Arc](https://azure.microsoft.com/services/azure-arc/) ，並讓 Azure Defender 將保護擴充至內部部署和多雲端虛擬機器和 SQL 資料庫。 適用於伺服器的 Azure Arc 是一項免費服務，但在啟用 Arc 的伺服器上使用的服務（例如 Azure Defender）將依該服務的定價計費。

[深入瞭解 Azure Arc](https://docs.microsoft.com/azure/azure-arc/overview)。


## <a name="azure-defender-alerts"></a>Azure Defender 警示 

當 Azure Defender 在您環境中的任何區域偵測到威脅時，它會產生警示。 這些警示會說明受影響資源的詳細資料、建議的補救步驟，以及在某些情況下會說明觸發邏輯應用程式以進行回應的選項。

無論警示是由安全性中心產生，或是由安全性中心從整合式安全性產品所接收，您都可以將它匯出。 若要將您的警示匯出至 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，請依照[將警示匯出至 SIEM](continuous-export.md) 中的指示操作。

> [!NOTE]
> 來自不同來源的警示顯示的所需時間可能不相同。 例如，需要分析網路流量的警示在顯示時，可能會比在虛擬機器上執行的可疑程序所產生的相關警示耗時。


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender advanced protection 功能

Azure Defender 針對您資源的相關建議，使用先進的分析。 

保護包括使用即時存取和彈性應用程式控制來保護您 Vm 的管理埠，以建立應用程式應該和不應在您的電腦上執行的允許清單。 

使用 Azure Defender 儀表板中的 advanced protection 磚來監視和設定這些保護。 

## <a name="vulnerability-assessment-and-management"></a>弱點評定與管理

Azure Defender 包含虛擬機器和容器登錄的弱點掃描，不需額外費用。 掃描器是由 Qualys 提供技術支援，但您不需要 Qualys 授權或甚至是 Qualys 帳戶-所有專案都是在資訊安全中心內無縫處理。 

查看這些弱點掃描程式的結果，並從安全中心內全部回應。 這會讓安全性中心更接近您所有雲端安全性工作的單一窗格。

深入瞭解下列頁面：

- [適用于 Azure 虛擬機器的安全性中心整合式弱點評估解決方案](deploy-vulnerability-assessment-vm.md)
- [識別 Azure container registry 映射中的弱點](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>下一步

在本文中，您已瞭解 Azure Defender 的優點。 

> [!div class="nextstepaction"]
> [啟用 Azure Defender](security-center-pricing.md)