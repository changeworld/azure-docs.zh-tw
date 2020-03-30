---
title: 創建自訂分析規則，以便使用 Azure Sentinel 檢測可疑威脅*微軟文檔
description: 使用本教程瞭解如何創建自訂分析規則以使用 Azure Sentinel 檢測可疑威脅。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585102"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>教程：創建自訂分析規則以檢測可疑威脅

 [將資料來源](quickstart-onboard.md) 連接到 Azure Sentinel 後，可以創建自訂規則，這些規則可以搜索整個環境中的特定條件，並在匹配條件時建置事件，以便可以調查它們。 本教程可説明您創建自訂規則，以便使用 Azure Sentinel 檢測威脅。

本教程可説明您使用 Azure Sentinel 檢測威脅。
> [!div class="checklist"]
> * 創建分析規則
> * 將威脅回應自動化

## <a name="create-custom-analytic-rules"></a>創建自訂分析規則

您可以創建自訂分析規則，以説明您搜索環境中可疑的威脅和異常類型。 該規則確保立即通知您，以便您可以對威脅進行會審、調查和補救。

1. 在 Azure 入口網站的 [Azure Sentinel] 底下，選取 [分析]****。

1. 在頂部功能表列中，選擇 **"創建"** 並選擇 **"計畫查詢規則**"。 這將打開**分析規則嚮導**。

    ![創建計畫查詢](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. 在 **"常規"** 選項卡中，提供唯一**的名稱**和**說明**。 在 **"戰術"** 欄位中，您可以從對規則進行分類的攻擊類別中進行選擇。 根據需要設置警報**嚴重性**。 創建規則時，預設情況下，其**狀態****處於啟用狀態**狀態，這意味著它將在您完成創建後立即運行。 如果不希望它立即運行，請選擇 **"禁用**"，規則將添加到 **"活動規則"** 選項卡中，並在需要時可從那裡啟用它。

    ![開始創建自訂分析規則](media/tutorial-detect-threats-custom/general-tab.png)

1. 在 **"設置規則邏輯"** 選項卡中，可以直接在 **"規則"查詢**欄位中編寫查詢，也可以在日誌分析中創建查詢，然後複製並粘貼到該查詢。
 
   ![在 Azure 哨兵中創建查詢](media/tutorial-detect-threats-custom/settings-tab.png)

   - 請參閱右側**的"結果預覽"** 區域，其中 Azure Sentinel 顯示查詢將生成的結果（日誌事件），在編寫和配置查詢時動態更改。 該圖顯示定義時間段內的結果數，由 **"查詢計劃"** 部分中的設置確定。
    - 如果您發現查詢將觸發太多或太頻繁的警報，則可以在 **"警報閾值"** 部分設置基線。

      下面是一個依例查詢，當在 Azure 活動中創建異常數量的資源時，它會提醒您。

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > 查詢長度應介於 1 到 10，000 個字元之間，不能\*包含"搜索"\*或"聯合"。

    1. 使用 **"映射實體**"部分將查詢結果中的參數連結到 Azure Sentinel 識別的實體。 這些實體構成進一步分析的基礎，包括在 **"事件設置"** 選項卡中將警報分組到事件中。
    1. 在 **"查詢計劃"** 部分中，設置以下參數：

       1. 每次設置**Run 查詢**以控制查詢的運行頻率 - 頻率為每 5 分鐘，或不常以每天一次的速度運行。

       1. 從**上次設置查找資料**以確定查詢涵蓋的資料的時間段 - 例如，它可以查詢過去 10 分鐘的資料或過去 6 小時的資料。

       > [!NOTE]
       > 這兩個設置彼此獨立，最多一點。 可以在短時間內執行查詢，覆蓋的時間週期比間隔長（實際上具有重疊查詢），但不能在超過覆蓋率的間隔內執行查詢，否則整個查詢覆蓋率將存在差距。

    1. 使用 **"警報閾值"** 部分定義基線。 例如，將查詢結果數設置為**大於"為大於"****時生成警報**，如果希望規則僅在每次運行時生成 1000 多個結果時生成警報，則設置"生成警報"，並輸入數位 1000。 由於這是必要欄位，因此，如果您不想設置基線（即，如果希望警報註冊每個事件），請在數位欄位中輸入 0。

    1. 在 **"抑制"** 部分中，您可以在**生成警報後打開"停止運行"查詢**，設置 **"** 如果一旦收到警報，則要暫停此規則的操作超過查詢間隔的時間段。 如果打開此選項，則必須將 **"停止執行查詢"設置為**查詢應停止運行的時間，最多 24 小時。

1. 在"**事件設置"** 選項卡中，您可以選擇 Azure 哨兵是否以及如何將警報轉換為可操作事件。 如果此選項卡單獨保留，Azure Sentinel 將創建與每個警報分開的單一事件。 通過更改此選項卡中的設置，您可以選擇不創建事件，或將多個警報分組到單個事件中。

    1. 在 **"事件設置"** 部分中，從**此分析規則引發的警報創建事件**預設設置為**啟用**，這意味著 Azure Sentinel 將從規則引發的每個警報創建單個單獨的事件。<br></br>如果不希望此規則導致創建任何事件（例如，如果此規則只是為了收集資訊以便後續分析），則將此規則設置為 **"已禁用**"。

    1. 在 **"警報分組**"部分中，如果希望從一組類似或定期警報生成單個事件，則**將由此分析規則引發的組相關警報設置為"****已啟用"，** 並設置以下參數。

    1. **將組限制為在選定時間範圍內創建的警報**：<br></br> 確定將類似或定期警報分組在一起的時間範圍。 此時間段內的所有相應警報將共同建置事件或一組事件（具體取決於下面的分組設置）。 此時間範圍以外的警報將生成單獨的事件或一組事件。

    2. 通過：選擇將警報分組在一起的基礎：將**此分析規則引發的警報分組到單個事件中**：

        - **如果所有實體都匹配，則將警報分組到單個事件**中： <br></br>如果警報為每個映射的實體共用相同的值（在上面的"設置規則邏輯"選項卡中定義），則它們將分組在一起。 此為建議設定值。

        - **將此規則引發的所有警報分組到單個事件中**： <br></br>此規則生成的所有警報都分組在一起，即使它們沒有共用相同的值。

        - **如果所選實體匹配，則將警報分組到單個事件**中： <br></br>如果警報共用某些映射實體的相同值（您可以從下拉清單中選擇），則將組合在一起。 例如，如果要基於源或目標 IP 位址創建單獨的事件，則可能需要使用此設置。

    3. **重新打開已關閉的匹配事件**：如果事件已關閉（意味著基礎問題已解決），隨後將生成另一個警報，該警報將分組到該事件中，如果希望重新打開已關閉的事件，請將此設置設置為 **"已啟用"，** 如果希望警報創建新事件，則保留為 **"已禁用**"。

1. 在 **"自動回應**"選項卡中，選擇自訂規則生成警報時要自動運行的任何行動手冊。 有關創建和自動化行動手冊的詳細資訊，請參閱 [回應威脅](tutorial-respond-threats-playbook.md)。

1. 選擇 **"查看並創建**"以查看新警報規則的所有設置，然後選擇 **"創建"以初始化警報規則**。
  
1. 創建警報後，在**活動規則**下向表添加自訂規則。 在此清單中，您可以啟用、禁用或刪除每個規則。

1. 要查看您創建的警報規則的結果，請轉到 **"事件"** 頁，您可以在其中會審、 [調查事件](tutorial-investigate-cases.md)並修復威脅。


> [!NOTE]
> 在 Azure 哨兵中生成的警報可通過 [Microsoft 圖形安全](https://aka.ms/securitygraphdocs)版獲得。 有關詳細資訊，請參閱 Microsoft [圖形安全警報文檔](https://aka.ms/graphsecurityreferencebetadocs)。

## <a name="next-steps"></a>後續步驟

在本教程中，您學習了如何使用 Azure Sentinel 開始檢測威脅。

要瞭解如何自動回應威脅，請 [在 Azure Sentinel 中設置自動威脅回應](tutorial-respond-threats-playbook.md)。

