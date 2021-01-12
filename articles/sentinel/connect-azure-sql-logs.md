---
title: 將 Azure SQL database 診斷和審核記錄連線至 Azure Sentinel
description: 瞭解如何將 Azure SQL database 診斷記錄和安全性審核記錄連線到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: df132c35ebb04596d91720431f5b08cb88e2abd9
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98104176"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>連接 Azure SQL database 診斷和審核記錄

Azure SQL 是完全受控的平臺即服務 (PaaS) database engine，可處理大部分的資料庫管理功能，例如升級、修補、備份和監視，而不需要使用者介入。 

Azure SQL database 連接器可讓您將資料庫的審核和診斷記錄串流至 Sentinel，讓您能夠持續監視所有實例中的活動。

- 連接診斷記錄可讓您將不同資料類型的資料庫診斷記錄傳送至 Sentinel 工作區。

- 連接審核記錄可讓您從伺服器層級的所有 Azure SQL 資料庫串流安全性審核記錄。

深入瞭解如何 [監視 AZURE SQL 資料庫](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)。

## <a name="prerequisites"></a>必要條件

- 您必須擁有 Azure Sentinel 工作區的讀取和寫入權限。

- 若要連線到審核記錄，您必須擁有 Azure SQL Server audit 設定的讀取和寫入權限。

## <a name="connect-to-azure-sql-database"></a>連線到 Azure SQL 資料庫
    
1. 從 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從資料連線器資源庫中選取 **Azure SQL Database** ，然後選取 [預覽] 窗格上的 [ **開啟連接器] 頁面**  。

1. 在 [連接器] **頁面的 [設定] 區段中** ，記下您可以連線的兩個記錄類別。

### <a name="connect-diagnostics-logs"></a>連接診斷記錄

1. 在 [ **診斷記錄**] 底下，展開 [在 **每個 Azure SQL 資料庫上手動啟用診斷記錄**]。

1. 選取 [ **開啟 AZURE sql >** ] 連結以開啟 [ **azure sql** 資源] 分頁。

1. **(選擇性)** 若要輕鬆地尋找您的資料庫資源，請選取頂端篩選列上的 [ **加入篩選** ]。
    1. 在 [ **篩選** ] 下拉式清單中，選取 [ **資源類型**]。
    1. 在 [ **值** ] 下拉式清單中，取消 **選取**[全選]，然後選取 **[SQL database**]。
    1. 按一下 [套用]。
    
1. 選取您要將診斷記錄傳送至 Azure Sentinel 的資料庫資源。

    > [!NOTE]
    > 針對您要收集記錄的每個資料庫資源，您必須重複此程式，從此步驟開始。

1. 從您所選取資料庫的 [資源] 頁面中，在導覽功能表的 [ **監視** ] 底下，選取 [ **診斷設定**]。

    1. 選取資料表底部的 [ **+ 新增診斷設定** ] 連結。

    1. 在 [ **診斷設定** ] 畫面的 [  **診斷設定名稱** ] 欄位中輸入名稱。
    
    1. 在 [ **目的地詳細資料] 資料** 行中，標示 [ **傳送至 Log Analytics 工作區** ] 核取方塊。 其中會顯示兩個新的欄位。 選擇 Azure Sentinel 所在) 的相關 **訂** 用帳戶和 **Log Analytics 工作區** (。

    1. 在 [ **類別目錄詳細資料] 資料** 行中，標示您想要內嵌之記錄和度量類型的核取方塊。 建議您選取 **記錄****和計量下的所有** 可用類型。

    1. 選取畫面頂端的 [ **儲存** ]。

- 或者，您可以使用提供的 **PowerShell 腳本** 來連接診斷記錄。
    1. 在 [ **診斷記錄**] 底下，展開 [ **由 PowerShell 啟用腳本**]。

    1. 複製程式碼區塊，並在 PowerShell 中貼上。

### <a name="connect-audit-logs"></a>連接 audit 記錄

1. 在 [ **(預覽) 的 [審核記錄**] 下，展開 **[在伺服器層級) 的所有 Azure SQL 資料庫 (啟用審核記錄**]。

1. 選取 [ **開啟 AZURE sql >** ] 連結以開啟 [ **SQL server** 資源] 分頁。

1. 選取您要傳送其審核記錄至 Azure Sentinel 的 SQL server。

    > [!NOTE]
    > 針對您要收集記錄的每個伺服器資源，您必須重複此程式，從此步驟開始。

1. 從您選取之伺服器的 [資源] 頁面的導覽功能表中，選取 [ **安全性** ] 底下的 [ **審核**]。

    1. 將 [ **啟用 AZURE SQL 審核** 開關] 移至 [ **開啟**]。

    1. 在 [ **審核記錄目的地**] 底下，選取 [ **Log Analytics (預覽])**。
    
    1. 從顯示的工作區清單中，選擇 Azure Sentinel 所在的工作區 () 。

    1. 選取畫面頂端的 [ **儲存** ]。

- 或者，您可以使用提供的 **PowerShell 腳本** 來連接診斷記錄。
    1. 在 [ **審核記錄**] 底下，展開 [ **由 PowerShell 啟用腳本**]。

    1. 複製程式碼區塊，並在 PowerShell 中貼上。


> [!NOTE]
>
> 使用這個特定的資料連線器，線上狀態指標 (資料連線器資源庫中的色彩等量，以及資料類型名稱旁邊的連接圖示) 只會在過去兩周內的某個時間點內嵌資料時，才會顯示為 *已連線* (綠色) 。 一旦經過兩周後未內嵌資料，連接器將會顯示為已中斷連線。 資料越多， *線上狀態就* 會傳回。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure SQL database 診斷和審核記錄連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。