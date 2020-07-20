---
title: 使用 Azure Sentinel 來建立自訂分析規則，以偵測可疑威脅 |Microsoft Docs
description: 使用此教學課程來瞭解如何建立自訂分析規則，以使用 Azure Sentinel 來偵測可疑的威脅。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77585102"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>教學課程：建立自訂分析規則來偵測可疑威脅

將 [資料來源連接](quickstart-onboard.md)   到 Azure Sentinel 之後，您可以建立自訂規則，以便在您的環境中搜尋特定準則，並在符合準則時產生事件，以便您進行調查。 本教學課程可協助您建立自訂規則，以使用 Azure Sentinel 來偵測威脅。

本教學課程可協助您偵測 Azure Sentinel 的威脅。
> [!div class="checklist"]
> * 建立分析規則
> * 將威脅回應自動化

## <a name="create-custom-analytic-rules"></a>建立自訂分析規則

您可以建立自訂分析規則，協助您搜尋環境中可疑的威脅和異常類型。 此規則可確保您立即收到通知，讓您可以對威脅進行分級、調查及補救。

1. 在 Azure 入口網站的 [Azure Sentinel] 底下，選取 [分析]。

1. 在頂端功能表列中，選取 [ **+ 建立**]，然後選取 [已**排程的查詢規則**]。 這會開啟 [**分析規則] 嚮導**。

    ![建立排程的查詢](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. 在 [**一般**] 索引標籤中，提供唯一的**名稱**和**描述**。 在 [**策略**] 欄位中，您可以從用來分類規則的攻擊類別中選擇。 視需要設定警示**嚴重性**。 當您建立規則時，預設會**啟用**其**狀態**，這表示它會在您完成建立之後立即執行。 如果您不想要讓它立即執行，請選取 [**停用**]，此規則將會新增至 [作用中**規則**] 索引標籤，而您可以在需要時從該處啟用它。

    ![開始建立自訂分析規則](media/tutorial-detect-threats-custom/general-tab.png)

1. 在 [**設定規則邏輯**] 索引標籤中，您可以直接在 [**規則查詢**] 欄位中撰寫查詢，或在 Log Analytics 中建立查詢，然後在該處複製並貼上。
 
   ![在 Azure Sentinel 中建立查詢](media/tutorial-detect-threats-custom/settings-tab.png)

   - 請參閱右側的 [**結果預覽**] 區域，其中 Azure Sentinel 會顯示查詢將會產生的結果（記錄事件）數目，這是您在撰寫和設定查詢時的即時變更。 圖形會顯示在定義的時間週期內的結果數目，這取決於 [**查詢排程**] 區段中的設定。
    - 如果您發現您的查詢會觸發太多或太頻繁的警示，您可以在 [**警示閾值**] 區段中設定基準。

      以下是一個範例查詢，會在 Azure 活動中建立了異常數量的資源時發出警示。

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > 查詢長度應介於1到10000個字元之間，且不能包含 "search \* " 或 "union \* "。

    1. 使用 [**地圖實體**] 區段，將查詢結果中的參數連結到 Azure Sentinel 辨識的實體。 這些實體會形成進一步分析的基礎，包括在 [**事件設定**] 索引標籤中將警示分組至事件。
    1. 在 [**查詢排程**] 區段中，設定下列參數：

       1. 設定 [**執行查詢**] 以控制查詢的執行頻率-頻率是每隔5分鐘，或一天不常進行一次。

       1. 設定**自最後一個的查閱資料**，以判斷查詢所涵蓋資料的時間週期-例如，它可以查詢過去10分鐘的資料，或過去6小時的資料。

       > [!NOTE]
       > 這兩個設定彼此獨立，而不是點。 您可以在涵蓋時間週期超過間隔（作用中有重迭的查詢）的短時間間隔執行查詢，但無法以超過涵蓋範圍期間的間隔執行查詢，否則整體查詢涵蓋範圍中將會有間距。

    1. 使用 [**警示臨界值**] 區段來定義基準。 例如，設定 [**當查詢結果數目****大於**時產生警示]，並在每次執行時，如果查詢產生的結果超過1000，則輸入數位1000。 這是必要欄位，如果您不想要設定基準（也就是您想要警示註冊每個事件–請在 [數位] 欄位中輸入0）。

    1. 在 [**隱藏**] 區段中，您可以在**警示產生後**，開啟 [停止執行查詢 **] 設定。** 如果您收到警示，您會想要在超過查詢間隔的一段時間內暫停此規則的作業。 如果您開啟此功能，則必須將 [**停止執行查詢**] 設定為查詢應該停止執行的時間量（最多24小時）。

1. 在 [**事件設定**] 索引標籤中，您可以選擇 Azure Sentinel 如何將警示轉換成可採取動作的事件。 如果保留此索引標籤，Azure Sentinel 將會在每個警示中建立單一的個別事件。 您可以選擇不建立任何事件，或藉由變更此索引標籤中的設定，將數個警示組成單一事件。

    1. 在 [**事件設定**] 區段中，[**從這個分析規則所觸發的警示建立事件**] 預設會設定為 [**已啟用**]，這表示 Azure Sentinel 將會從每個規則所觸發的每個警示建立單一的個別事件。<br></br>如果您不想讓此規則建立任何事件（例如，如果此規則只是為了收集後續分析的資訊），請將此設定為 [**停用**]。

    1. 在 [**警示群組**] 區段中，如果您想要從一組類似或重複的警示產生單一事件，請將**此分析規則所觸發的群組相關警示**設定為 [要**啟用**的事件]，並設定下列參數。

    1. 將**群組限制在所選時間範圍內建立的警示**：<br></br> 判斷類似或重複的警示將會群組在一起的時間範圍。 此時間範圍內的所有對應警示都會共同產生事件或一組事件（視下面的群組設定而定）。 在此時間範圍外的警示會產生個別的事件或一組事件。

    2. 藉**由下列方式將此分析規則所觸發的警示分組到單一事件**：選擇要將警示分組在一起的基礎：

        - **如果所有實體都相符，則將警示分組為單一事件**： <br></br>如果警示會針對每個對應實體（在上方的 [設定規則邏輯] 索引標籤中定義）共用相同的值，則會群組在一起。 此為建議設定值。

        - **將此規則引發的所有警示組成單一事件**： <br></br>此規則所產生的所有警示會群組在一起，即使它們不會共用相同的值。

        - **如果選取的實體相符，則將警示分組為單一事件**： <br></br>如果警示會針對某些對應實體（您可以從下拉式清單中選取）共用相同的值，則會群組在一起。 例如，如果您想要根據來源或目標 IP 位址來建立個別的事件，您可能會想要使用此設定。

    3. **重新開啟已關閉**的比對事件：如果已關閉事件（表示已解決基礎問題），接著產生另一個已分組至該事件的警示，請將此設定設為 [**啟用**] （如果您想要讓關閉的事件重新開啟），並在您想要警示建立新事件時保留為 [**停用**]。

1. 在 [**自動回應**] 索引標籤中，選取您想要在自訂規則產生警示時自動執行的任何時間手冊。 如需建立和自動化操作手冊的詳細資訊，請參閱 [回應威脅](tutorial-respond-threats-playbook.md)。

1. 選取 [**審查] 和 [建立**] 以審查新警示規則的所有設定，然後選取 [建立]**以初始化您的警示規則**。
  
1. 建立警示之後，會將自訂規則新增至 [作用中**規則**] 底下的資料表。 從這份清單中，您可以啟用、停用或刪除每個規則。

1. 若要查看您所建立之警示規則的結果，請移至 [**事件**] 頁面，您可以在其中分級、 [調查事件](tutorial-investigate-cases.md)，以及補救威脅。


> [!NOTE]
> Azure Sentinel 中產生的警示可透過 [Microsoft Graph 安全性](https://aka.ms/securitygraphdocs)來取得。 如需詳細資訊，請參閱 [Microsoft Graph 安全性警示檔](https://aka.ms/graphsecurityreferencebetadocs)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已瞭解如何使用 Azure Sentinel 開始偵測威脅。

若要瞭解如何將威脅的回應自動化，請 [在 Azure Sentinel 中設定自動威脅回應](tutorial-respond-threats-playbook.md)。

