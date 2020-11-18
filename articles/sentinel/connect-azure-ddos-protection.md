---
title: 將 Azure DDoS 保護資料連線至 Azure Sentinel
description: 瞭解如何將 Azure DDoS 保護資料內嵌至 Azure Sentinel，讓您可以加以查看、分析和調查。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/01/2020
ms.author: yelevin
ms.openlocfilehash: b5d98a834ed4f87fb95a13e666967a7b2d80975d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656049"
---
# <a name="connect-data-from-azure-ddos-protection"></a>從 Azure DDoS 保護連接資料

> [!IMPORTANT]
> Azure Sentinel 中的 Azure DDoS 保護資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

分散式阻斷服務 (DDoS) 攻擊嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以鎖定可透過網際網路公開觸達的任何端點。 結合應用程式設計最佳做法的[Azure DDoS 保護](../ddos-protection/ddos-protection-overview.md)，可針對 DDoS 攻擊提供穩固的防禦。 您可以將 Azure DDoS 保護記錄連接到 Azure Sentinel，讓您可以在活頁簿中查看記錄資料、使用它來建立自訂警示，並加以合併以改善您的調查。 

## <a name="prerequisites"></a>先決條件

- 您必須擁有 Azure Sentinel 工作區的讀取和寫入權限。

- 您必須有已設定的 [Azure DDoS 標準保護計劃](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan)。

- 您必須有已設定的 [虛擬網路，且已啟用 Azure DDoS Standard](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network)。

## <a name="connect-to-azure-ddos-protection"></a>連接到 Azure DDoS 保護
    
1. 從 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從資料連線器資源庫中選取 **Azure DDoS 保護** ，然後選取 [預覽] 窗格上的 [ **開啟連接器] 頁面** 。

1. 在您想要連接其記錄的所有防火牆上啟用 **診斷記錄** ：

    1. 選取 [ **開啟診斷設定] >** 連結，然後從清單中選擇 **公用 IP 位址** 資源。

    1. 選取 [+新增診斷設定]。

    1. 在 [ **診斷設定** ] 畫面中：
       - 在 [  **診斷設定名稱** ] 欄位中輸入名稱。

       - 標示 [ **傳送至 Log Analytics** ] 核取方塊。 其中會顯示兩個新的欄位。 選擇 Azure Sentinel 所在) 的相關 **訂** 用帳戶和 **Log Analytics 工作區** (。

       - 標示您想要內嵌其記錄檔之規則類型的核取方塊。 我們建議 **DDoSProtectionNotifications**、 **DDoSMitigationFlowLogs** 和 **DDoSMitigationReports**。

    1. 按一下畫面頂端的 [儲存]。 針對您已啟用 DDoS 保護的任何其他防火牆 (公用 IP 位址) 重複此程式。

1. 若要在 Log Analytics 中使用相關的架構來 Azure DDoS 保護警示，請搜尋 **AzureDiagnostics**。

> [!NOTE]
>
> 使用這個特定的資料連線器，線上狀態指標 (資料連線器資源庫中的色彩等量，以及資料類型名稱旁邊的連接圖示) 只會在過去兩周內的某個時間點內嵌資料時，才會顯示為 *已連線* (綠色) 。 一旦經過兩周後未內嵌資料，連接器將會顯示為已中斷連線。 資料越多， *線上狀態就* 會傳回。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Azure DDoS 保護記錄連接至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。