---
title: Azure Defender 的儀表板及其功能
description: 瞭解 Azure Defender 儀表板的功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 35469c7a11d47e586187b55bde1e8ad8a0c94f5f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448414"
---
# <a name="the-azure-defender-dashboard"></a>Azure Defender 儀表板

Azure Defender 儀表板提供：

- 跨不同資源類型的 Azure Defender 涵蓋範圍可見度
- 設定 advanced 威脅防護功能的連結
- 上架狀態和代理程式安裝
- Azure Defender 威脅偵測警示 

若要存取 Azure Defender 儀表板，請從 [安全性中心] 功能表的 [雲端安全性] 區段中選取 [ **Azure defender** ]。

## <a name="whats-shown-on-the-dashboard"></a>儀表板上顯示的內容？

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Azure Defender 儀表板的範例" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

儀表板包含下列各節：

1. **Azure defender 涵蓋範圍** -您可以在這裡查看訂用帳戶中的資源類型，並符合 azure defender 的保護資格。 無論何時相關，您都可以選擇升級。 如果您想要升級所有可能的符合資格的資源，請選取 [ **全部升級**]。

1. **安全性警示區域** -當 Azure Defender 在您環境的任何區域偵測到威脅時，它會產生警示。 這些警示會說明受影響資源的詳細資料、建議的補救步驟，以及在某些情況下會說明觸發邏輯應用程式以進行回應的選項。 選取此圖表中的任何位置，就會開啟 [ **安全性警示] 頁面**。

1. **Advanced protection** -Azure Defender 包含許多適用于虛擬機器、SQL 資料庫、容器、web 應用程式、網路等的先進威脅防護功能。 在此 [advanced protection] 區段中，您可以查看所選訂用帳戶中每個保護的資源狀態。 選取其中任何一項，直接移至該保護類型的設定區域。

1. **深入** 解析-新聞的這個輪流窗格、建議閱讀，以及高優先順序的警示，可讓資訊安全中心深入瞭解與您和您的訂用帳戶相關的安全性問題。 無論是由弱點分析工具在您的 Vm 上探索到的高嚴重性 Cve 清單，或是由資訊安全中心小組成員提供的新 blog 文章，您都可以在 **Azure Defender 儀表板**的 [見解] 窗格中找到此清單。




## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解 Azure Defender 儀表板。 

如需 Azure Defender 的詳細資訊，請參閱 [Azure Defender 簡介](azure-defender.md)

> [!div class="nextstepaction"]
> [啟用 Azure Defender](security-center-pricing.md)