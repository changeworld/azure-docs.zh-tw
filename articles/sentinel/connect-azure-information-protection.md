---
title: 將 Azure 資訊保護連接到 Azure Sentinel
description: 藉由設定 Azure 資訊保護資料連線器，將 Azure 資訊保護中的記錄資訊串流至 Azure Sentinel。 
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: f06f8d3450f8907aa1be34b738565ac55e5e3d2d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559137"
---
# <a name="connect-data-from-azure-information-protection"></a>從 Azure 資訊保護連接資料

> [!IMPORTANT]
> Azure Sentinel 中的 Azure 資訊保護資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以藉由設定 Azure 資訊保護資料連線器，將[Azure 資訊保護](https://azure.microsoft.com/services/information-protection/)中的記錄資訊串流至 Azure Sentinel。 Azure 資訊保護可協助您控制及保護您的敏感性資料，不論其儲存在雲端或內部部署中。

如果已設定[Azure 資訊保護的中央報告](https://docs.microsoft.com/azure/information-protection/reports-aip)，讓來自此服務的記錄資訊儲存在與您目前為 Azure Sentinel 所選取的相同 Log Analytics 工作區中，您可以略過此資料連線器的設定。 Azure 資訊保護的記錄資訊已可供 Azure Sentinel。

不過，如果 Azure 資訊保護的記錄資訊會與您目前為 Azure Sentinel 選取的記錄分析工作區不同，請執行下列其中一項動作：

- 變更 Azure Sentinel 中選取的工作區。

- 變更 Azure 資訊保護的工作區，您可以藉由設定此資料連線器來執行。
    
    如果您變更工作區，Azure 資訊保護的新報告資料現在會儲存在您用於 Azure Sentinel 的工作區中，而歷程資料無法供 Azure Sentinel 使用。 此外，如果先前的工作區是針對自訂查詢、警示或 REST Api 進行設定，如果您想要使用它們來進行 Azure 資訊保護，就必須為 Azure Sentinel 工作區重新設定這些專案。 使用 Azure 資訊保護的用戶端和服務不需要重新設定。

## <a name="prerequisites"></a>必要條件

- 您租使用者的下列其中一個 Azure AD 系統管理員角色： 
    - Azure 資訊保護管理員
    - 安全性系統管理員
    - 規範管理員
    - 合規性資料管理員
    - 全域管理員
    
    > [!NOTE]
    > 如果您的租使用者位於[統一標籤平臺](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)上，則無法使用 Azure 資訊保護系統管理員角色。
    
    只有在設定 Azure 資訊保護連接器時，才需要這些系統管理員角色，Azure Sentinel 連接到 Azure 資訊保護時，則不需要這些角色。

- 讀取和寫入您要用於 Azure Sentinel 和 Azure 資訊保護之 Log Analytics 工作區的許可權。

- Azure 資訊保護已新增至 Azure 入口網站。 如果您需要此步驟的說明，請參閱[將 Azure 資訊保護新增至 Azure 入口網站](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)。

## <a name="connect-to-azure-information-protection"></a>連接到 Azure 資訊保護

如果您尚未為 Azure 資訊保護設定 Log Analytics 工作區，或需要變更儲存 Azure 資訊保護記錄資訊的工作區，請使用下列指示。

1. 在 Azure Sentinel 中，選取 [**資料連線器**]，然後**Azure 資訊保護（預覽）**]。

2. 選取 [**開啟連接器] 頁面**。

3. 在 [**設定分析（預覽）** ] 分頁上，選取您目前用來進行 Azure Sentinel 的工作區。 如果您選取不同的工作區，Azure 資訊保護的報告資料將無法供 Azure Sentinel。

4. 當您選取工作區時，選取 **[確定]** ，連接器**狀態**現在應該會變更為 [**已連線**]。

5. 來自 Azure 資訊保護的報告資料會儲存在所選工作區的**InformationProtectionLogs_CL**資料表中。 
    
    若要在此報告資料的 Azure 監視器中使用相關的架構，請搜尋**InformationProtectionEvents**。 如需這些事件函數的詳細資訊，請參閱 Azure 資訊保護檔中的事件函式的[易記架構參考](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions)一節。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Azure 資訊保護連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
