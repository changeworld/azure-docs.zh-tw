---
title: 將 Azure 資訊保護連接到 Azure 哨兵
description: 瞭解如何在 Azure Sentinel 中連接 Azure 資訊保護資料。
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588547"
---
# <a name="connect-data-from-azure-information-protection"></a>連接 Azure 資訊保護的資料

> [!IMPORTANT]
> Azure 哨兵中的 Azure 資訊保護資料連線器當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

通過配置[Azure 資訊保護](https://azure.microsoft.com/services/information-protection/)資料連線器，可以將 Azure 資訊保護中的日誌記錄資訊資料流到 Azure Sentinel 中。 Azure 資訊保護可説明您控制和保護敏感性資料，無論這些資料存儲在雲中還是本地。

如果已配置[Azure 資訊保護的中央報告](https://docs.microsoft.com/azure/information-protection/reports-aip)，以便此服務的日誌記錄資訊存儲在當前為 Azure Sentinel 選擇的日誌分析工作區中，則可以跳過此資料連線器的配置。 Azure 資訊保護的日誌記錄資訊已可供 Azure 哨兵使用。

但是，如果從 Azure 資訊保護中記錄資訊將到與當前為 Azure Sentinel 選擇的日誌分析工作區不同的工作區，請執行以下操作之一：

- 更改 Azure 哨兵中選擇的工作區。

- 更改 Azure 資訊保護工作區，可以通過配置此資料連線器來執行此操作。
    
    如果更改工作區，Azure 資訊保護的新報告資料現在將存儲在用於 Azure Sentinel 的工作區中，並且 Azure Sentinel 不可用歷史資料。 此外，如果為自訂查詢、警報或 REST API 配置了以前的工作區，則必須為 Azure Sentinel 工作區重新配置這些工作區，如果要繼續使用它們進行 Azure 資訊保護。 對於使用 Azure 資訊保護的用戶端和服務，無需重新配置。

## <a name="prerequisites"></a>Prerequisites

- 租戶的以下 Azure AD 管理員角色之一： 
    - Azure 資訊保護管理員
    - 安全性系統管理員
    - 規範管理員
    - 合規性資料管理員
    - 全域管理員
    
    > [!NOTE]
    > 如果您的租戶位於[統一標籤平臺上](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)，則無法使用 Azure 資訊保護管理員角色。
    
    這些管理員角色僅需要配置 Azure 資訊保護連接器，並且在 Azure Sentinel 連接到 Azure 資訊保護時不需要這些管理員角色。

- 讀取和寫入用於 Azure 哨兵和 Azure 資訊保護的日誌分析工作區的許可權。

- Azure 資訊保護已添加到 Azure 門戶。 如果需要有關此步驟的説明，請參閱[向 Azure 門戶添加 Azure 資訊保護](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)。

## <a name="connect-to-azure-information-protection"></a>連接到 Azure 資訊保護

如果尚未為 Azure 資訊保護設定日誌分析工作區，或者需要更改存儲 Azure 資訊保護日誌記錄資訊的工作區，請使用以下說明。

1. 在 Azure 哨兵中，選擇**資料連線器**，然後選擇**Azure 資訊保護（預覽）。**

2. 選擇 **"打開連接器"頁**。

3. 在 **"配置分析（預覽）"** 邊欄選項卡上，選擇當前用於 Azure Sentinel 的工作區。 如果選擇其他工作區，Azure 資訊保護的報告資料將不適用於 Azure 哨兵。

4. 選擇工作區後，選擇 **"確定"，** 並且連接器**狀態**現在應更改為 **"已連接**"。

5. Azure 資訊保護的報告資料存儲在所選工作區中的**InformationProtectionLogs_CL**表中。 
    
    要使用此報告資料的 Azure 監視器中的相關架構，搜索**資訊保護事件**。 有關這些事件函數的資訊，請參閱 Azure 資訊保護文檔中[的事件函數友好架構參考](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions)部分。

## <a name="next-steps"></a>後續步驟

在本文檔中，您學習了如何將 Azure 資訊保護連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
