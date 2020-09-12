---
title: 將 Azure 防火牆資料連線到 Azure Sentinel
description: 瞭解如何將 Azure 防火牆資料連線到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 05a2869152def0fc9a44defaa21f0643d9fdbb08
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2020
ms.locfileid: "89504871"
---
# <a name="connect-data-from-azure-firewall"></a>從 Azure 防火牆連接資料

> [!IMPORTANT]
> Azure Sentinel 中的 Azure 防火牆資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。 它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。 

您可以將 Azure 防火牆記錄連線到 Azure Sentinel，讓您可以在活頁簿中查看記錄資料、使用它來建立自訂警示，並加以合併以改善您的調查。

深入瞭解如何 [監視 Azure 防火牆記錄](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)。

## <a name="prerequisites"></a>必要條件

- 您必須擁有 Azure Sentinel 工作區的讀取和寫入權限。

## <a name="connect-to-azure-firewall"></a>連接至 Azure 防火牆
    
1. 從 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從資料連線器資源庫選取 [ **Azure 防火牆** ]，然後選取 [預覽] 窗格上的 [ **開啟連接器] 頁面**  。

1. 在您想要連接其記錄的所有防火牆上啟用 **診斷記錄** ：

    1. 選取 [ **開啟 Azure 防火牆資源] >** 連結。

    1. 從 [ **防火牆** ] 導覽功能表中，選取 [ **診斷設定**]。

    1. 選取清單底部的 [ **+ 新增診斷設定** ]。

    1. 在 [ **診斷設定** ] 畫面的 [  **診斷設定名稱** ] 欄位中，輸入名稱。
    
    1. 標示 [ **傳送至 Log Analytics** ] 核取方塊。 其中會顯示兩個新的欄位。 選擇 Azure Sentinel 所在) 的相關 **訂** 用帳戶和 **Log Analytics 工作區** (。

    1. 標示您想要內嵌其記錄檔之規則類型的核取方塊。 我們建議 **>azurefirewallapplicationrule** 和 **AzureFirewallNetworkRule**。

    1. 選取畫面頂端的 [ **儲存** ]。

1. 若要在 Log Analytics 中使用適用于 Azure 防火牆警示的相關架構，請搜尋 **AzureDiagnostics**。

> [!NOTE]
>
> 使用這個特定的資料連線器，線上狀態指標 (資料連線器資源庫中的色彩等量，以及資料類型名稱旁邊的連接圖示) 只會在過去兩周內的某個時間點內嵌資料時，才會顯示為 *已連線* (綠色) 。 一旦經過兩周後未內嵌資料，連接器將會顯示為已中斷連線。 資料越多， *線上狀態就* 會傳回。

## <a name="next-steps"></a>接下來的步驟
在本檔中，您已瞭解如何將 Azure 防火牆記錄連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
