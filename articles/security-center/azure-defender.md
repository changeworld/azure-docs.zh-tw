---
title: Azure Defender 和可用方案的概觀
description: 了解 Azure Defender 的方案、保護和警示。 然後在您的訂用帳戶上針對進階安全性啟用 Azure Defender。
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 41d98750f3c869eff5abf7482a9bb04435407318
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436754"
---
# <a name="introduction-to-azure-defender"></a>Azure Defender 簡介

Azure 資訊安全中心的功能涵蓋雲端安全性的兩大要素：

- **雲端安全性態勢管理 (CSPM)** - 資訊安全中心對於所有 Azure 使用者都是 **免費** 提供的。 免費體驗包括 CSPM 功能，例如安全分數、偵測 Azure 機器中的安全性設定錯誤、資產清查等等。 使用這些 CSPM 功能來強化您的混合式雲端態勢，並且追蹤內建原則的合規性。

- **雲端工作負載保護 (CWP)** - 資訊安全中心的整合雲端工作負載保護平台 (CWPP)，**Azure Defender**，為您的 Azure 和混合式資源和工作負載提供進階、智慧型的保護。 啟用 Azure Defender 提供了一系列額外的安全性功能，如本頁面所述。 除了內建原則之外，當您啟用任何 Azure Defender 方案時，您可以新增自訂原則和計畫。 您可以新增法規標準 (例如 NIST 和 Azure CI) 和 Azure 安全性效能評定，以取得合規性的真正自訂檢視。

資訊安全中心的 Azure Defender 儀表板可為您的環境提供 CWP 功能的可見度和控制權：

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Azure Defender 儀表板的範例" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Azure Defender 可保護哪些資源類型？

Azure Defender 可為虛擬機器、SQL 資料庫、容器、Web 應用程式及網路等項目提供安全性警示和進階威脅防護。

當您從 Azure 資訊安全中心的 [定價和設定] 區域啟用 Azure Defender 時，下列 Defender 方案會同時啟用，並為您環境的計算、資料和服務層提供全面的防護：

- [適用於伺服器的 Azure Defender](defender-for-servers-introduction.md)
- [適用於 App Service 的 Azure Defender](defender-for-app-service-introduction.md)
- [適用於儲存體的 Azure Defender](defender-for-storage-introduction.md)
- [適用於 SQL 的 Azure Defender](defender-for-sql-introduction.md)
- [適用於 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)
- [適用於容器登錄的 Azure Defender](defender-for-container-registries-introduction.md)
- [適用於 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)

這些方案會分別在資訊安全中心文件中說明。

> [!TIP]
> 適用於 Azure Defender 的 IoT (預覽) 是一種不同的產品。 您可以在[介紹適用於 Azure Defender 的 IoT (預覽)](../defender-for-iot/overview.md) 中找到所有詳細資料。 

## <a name="hybrid-cloud-protection"></a>混合式雲端保護

除了保護您的 Azure 環境以外，您還可以將 Azure Defender 功能新增至混合式雲端環境：

- 保護您的非 Azure 伺服器
- 保護其他雲端中的虛擬機器 (例如 AWS 和 GCP)

您將可根據本身的特定環境自訂威脅情報及排定警示的優先順序，以全心處理最重要的事務。

若要將保護延伸至其他雲端或內部部署中的虛擬機器和 SQL 資料庫，請部署 [Azure Arc](https://azure.microsoft.com/services/azure-arc/) 以及啟用 Azure Defender。 適用於伺服器的 Azure Arc 是免費服務，但在已啟用 Arc 的伺服器上使用的服務 (例如 Azure Defender) 將依據該服務的定價收費。 若要深入了解，請參閱[新增具有 Azure Arc 的非 Azure 機器](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc)。

> [!TIP]
> 適用於 AWS 的原生連接器會以透明方式為您處理 Azure Arc 部署。 若要深入了解，請參閱[將您的 AWS 帳戶連線到 Azure 資訊安全中心](quickstart-onboard-aws.md)。



## <a name="security-alerts"></a>安全性警示 

Azure Defender 在您環境的任何區域中偵測到威脅時，將會產生警示。 這些警示會說明受影響資源的詳細資料、建議的補救步驟，以及在某些情況下會說明觸發邏輯應用程式以進行回應的選項。

無論是由資訊安全中心產生的警示，還是資訊安全中心從整合式安全性產品接收到的警示，您都可以將其匯出。 若要將您的警示匯出至 Azure Sentinel、任何協力廠商 SIEM 或任何其他外部工具，請依照[將警示串流至 SIEM、SOAR 或 IT 服務管理解決方案](export-to-siem.md)中的指示操作。

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