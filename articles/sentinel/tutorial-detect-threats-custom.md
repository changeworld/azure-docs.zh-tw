---
title: 使用 Azure Sentinel 來建立自訂分析規則來偵測威脅 |Microsoft Docs
description: 您可以使用本教學課程來瞭解如何建立自訂分析規則，以利用 Azure Sentinel 來偵測安全性威脅。 利用事件群組和警示群組，並瞭解自動停用。
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 5d856339632e0033e997e5c1665fab623fda9cd2
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795598"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>教學課程：建立自訂分析規則來偵測威脅

當您將 [資料來源連線](quickstart-onboard.md) 到 Azure Sentinel 之後，您可以建立自訂規則，以便在您的環境中搜尋特定準則，並在符合準則時產生事件，以便您進行調查。 本教學課程可協助您建立自訂規則，以利用 Azure Sentinel 來偵測威脅。

本教學課程可協助您使用 Azure Sentinel 來偵測威脅。
> [!div class="checklist"]
> * 建立分析規則
> * 定義事件和警示的處理方式
> * 定義警示和事件的產生方式
> * 將威脅回應自動化

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>使用已排程的查詢建立自訂分析規則

您可以建立自訂分析規則，以協助您探索環境中存在的威脅和異常行為。 此規則可確保您會立即收到通知，讓您可以分級、調查和修復威脅。

1. 在 Azure 入口網站的 [Azure Sentinel] 底下，選取 [分析]。

1. 在頂端功能表列中，選取 [ **+ 建立** ] 並選取 [已 **排程的查詢規則**]。 這會開啟 [ **分析規則]**。

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="建立排定的查詢" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

1. 在 [ **一般** ] 索引標籤中，提供唯一的 **名稱** 和 **描述**。 在 [ **策略** ] 欄位中，您可以選擇要將規則分類的攻擊類別。 視需要設定警示 **嚴重性** 。 當您建立規則時，預設會 **啟用** 其 **狀態**，這表示它會在您建立完成後立即執行。 如果您不想要讓它立即執行，請選取 [ **停用**]，此規則將會新增至 [作用中 **規則** ] 索引標籤，而且您可以在需要時從該處啟用它。

    :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="開始建立自訂分析規則":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>定義規則查詢邏輯和設定設定

1. 在 [ **設定規則邏輯** ] 索引標籤中，您可以直接在 [ **規則查詢** ] 欄位中撰寫查詢，或在 Log Analytics 中建立查詢，然後將它複製並貼到該處。 查詢是以 Kusto 查詢語言 (KQL) 撰寫的。 深入瞭解 KQL [概念](/azure/data-explorer/kusto/concepts/) 和 [查詢](/azure/data-explorer/kusto/query/)，並查看這個方便的 [快速參考指南](/azure/data-explorer/kql-quick-reference)。

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1.png" alt-text="設定查詢規則邏輯和設定" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1.png":::

   - 在右側的 [ **結果模擬** ] 區域中，選取 [ **使用目前的資料測試** ]，Azure Sentinel 將會顯示結果的圖形 (記錄事件) 查詢會根據目前定義的排程，在最後一個執行的50時間產生。 如果您修改查詢，請再次選取 [ **使用目前的資料測試** ] 以更新圖形。 圖形會顯示在定義的時間週期內的結果數目，取決於 [ **查詢排程** ] 區段中的設定。
  
      以下是上述螢幕擷取畫面中的查詢結果模擬可能會顯示的結果。 左邊是預設視圖，右邊則是當您將滑鼠停留在圖形上的某個時間點時所看到的。

     :::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="結果模擬螢幕擷取畫面":::

   - 如果您發現您的查詢會觸發太多或太頻繁的警示，您可以在 [ **警示閾值** ] 區段中設定基準 (請參閱下面) 。

      以下是在 Azure 活動中建立異常的資源數量時，會對您發出警示的範例查詢。

      ```kusto
      AzureActivity
      | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
      | where ActivityStatus == "Succeeded"
      | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
      ```

        > [!NOTE]
        > - 查詢長度應該介於1到10000個字元之間，且不能包含 "search \* " 或 "union \* "。
        >
        > - **不支援** 在 Log Analytics 查詢視窗內使用 ADX 函式來建立 Azure 資料總管查詢。

1. 您可以使用 [ **對應實體** ] 區段，將查詢結果中的參數連結至 Azure Sentinel 辨識的實體。 這些實體會形成進一步分析的基礎，包括在 [ **事件設定** ] 索引標籤中將警示分組到事件中。 

    深入瞭解 Azure Sentinel 中的 [實體](identify-threats-with-entity-behavior-analytics.md#entities-in-azure-sentinel) 。
  
1. 在 [ **查詢排程** ] 區段中，設定下列參數：

    :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="設定查詢排程和事件群組" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2.png":::

    1. 設定 [ **執行查詢間隔** ] 以控制查詢的執行頻率，頻率為每隔5分鐘一次或最少一天一次。

    1. 設定 **最後一個的查閱資料** 來判斷查詢所涵蓋資料的時間週期，例如，它可以查詢過去10分鐘的資料，或過去6小時的資料。

        > [!NOTE]
        > **查詢間隔和回顧期限**
        > - 這兩個設定彼此獨立，最多可達點。 您可以在一段短時間內執行查詢，此間隔涵蓋的時間長度超過間隔 (的效果會有重迭的查詢) ，但您無法以超過涵蓋範圍期間的間隔執行查詢，否則整體查詢涵蓋範圍中將會有間距。
        >
        > - 您最多可以設定14天的回顧期限。
        >
        > **內嵌延遲**
        > - 為了考慮在來源產生事件和將其內嵌到 Azure Sentinel 之間可能發生的 **延遲** ，並確保完全涵蓋範圍而不重復資料，Azure Sentinel 會從排程的時間 **內五分鐘的延遲** 執行排程分析規則。

1. 使用 [ **警示閾值** ] 區段來定義基準。 例如，如果您想要規則在每次執行時傳回超過1000的結果，請設定 [當查詢 **結果的數目大於時產生警示**]，然後輸入數位1000。 這是必要欄位，因此，如果您不想設定基準，也就是如果您想要讓警示註冊每個事件–請在 [數位] 欄位中輸入0。
    
1. 在 [**事件群組**] 下，選擇兩種方式之一來處理 **警示****的群組**： 

    - 將 **所有事件組成單一警示** (預設設定) 。 此規則會在每次執行時產生單一警示，只要查詢傳回的結果多於上述的指定 **警示閾值** 。 警示包含結果中傳回之所有事件的摘要。 

    - **觸發每個事件的警示**： 此規則會針對查詢所傳回的每個事件產生唯一的警示。 如果您想要個別顯示事件，或是想要依使用者、主機名稱或其他的參數將事件分組，這會很有用。 您可以在查詢中定義這些參數。
    
    規則目前可以產生的警示數目上限為20。 如果在特定的規則中， **事件群組** 會設定為 **觸發每個事件的警示**，而規則的查詢會傳回超過20個事件，則前19個事件中的每個事件都會產生唯一的警示，而第二十個警示則會摘要出一組完整的傳回事件。 換句話說，第二十個警示是在 [將 **所有事件分組到單一警示** ] 選項下產生的警示。

    > [!NOTE]
    > **事件** 與 **警示** 之間有何差異？
    >
    > - **事件** 是單一出現次數的描述。 例如，記錄檔中的單一專案可能會計算為事件。 在此內容中，事件會參考分析規則中的查詢所傳回的單一結果。
    >
    > - **警示** 是從安全性的觀點來看，所構成的事件集合。 如果事件有重大的安全性影響，警示可能會包含單一事件，例如，來自辦公室以外的外部國家/地區的系統管理登入。
       >
    > - 順便一提，什麼是 **事件**？ Azure Sentinel 的內部邏輯會從 **警示** 或警示群組建立 **事件**。 事件佇列是分析師的工作、調查和補救的焦點。
    > 
    > Azure Sentinel 從某些資料來源內嵌未經處理的事件，以及其他人已處理的警示。 請務必注意，您可以隨時進行處理。

    > [!IMPORTANT]
    > 事件群組目前為公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
    
1. 在 [ **隱藏** 專案] 區段中，您可以在收到警示之後，開啟 [在 **產生警示後停止執行查詢** **] 設定，** 如果您收到警示，您會想要在超過查詢間隔的一段時間內暫停此規則的操作。 如果您開啟此功能，您必須將 [ **停止執行查詢** ] 設定為查詢應該停止執行的時間量（最多24小時）。

## <a name="configure-the-incident-creation-settings"></a>設定事件建立設定

在 [ **事件設定** ] 索引標籤中，您可以選擇是否及如何 Azure Sentinel 將警示轉換成可採取動作的事件。 如果此索引標籤是獨立的，Azure Sentinel 將會從每個警示建立單一的個別事件。 您可以選擇不建立任何事件，也可以變更此索引標籤中的設定，將數個警示分組為單一事件。

> [!IMPORTANT]
> [事件設定] 索引標籤目前處於公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="定義事件建立和警示群組設定":::

1. 在 [ **事件設定** ] 區段中，依預設會將 **此分析規則所觸發之警示的 [建立事件** ] 設定為 [ **啟用**]，這表示 Azure Sentinel 將會從每個規則所觸發的每個警示建立單一的個別事件。
       - 如果您不想讓此規則產生任何事件 (例如，如果此規則只是為了收集後續分析) 的資訊，請將此規則設定為 **停用**。

1. 在 [ **警示群組** ] 區段中，如果您想要從最多150個類似或週期性警示的群組產生單一事件 (請參閱) 、將 **此分析規則所觸發的群組相關警示** ，設定為要 **啟用** 的事件），並設定下列參數。

    - 將 **群組限制在選取的時間範圍內建立的警示**：決定將類似或週期性警示群組在一起的時間範圍。 此時間範圍內所有對應的警示，都會根據以下) 的分組設定，共同產生事件或一組事件 (。 此時間範圍外的警示會產生個別的事件或一組事件。

    - 藉 **由以下方式將此分析規則所觸發的警示群組至單一事件**：選擇將警示分組在一起的基礎：

        - **如果所有實體相符，則將警示分組為單一事件**：如果它們共用每個對應實體的相同值 (在) 上方的 [設定規則邏輯] 索引標籤中定義，則會將警示分組在一起。 此為建議設定值。

        - **將此規則引發的所有警示分組為單一事件**：此規則產生的所有警示都會群組在一起，即使它們共用沒有相同的值也一樣。

        - **如果選取的實體相符，則將警示分組為單一事件**：如果它們共用部分對應實體的相同值，則會將警示群組在一起， (您可以從下拉式清單中選取) 。 例如，如果您想要根據來源或目標 IP 位址建立個別事件，您可能會想要使用此設定。

    - **重新開啟已關閉** 的比對事件：如果事件已解決並關閉，且稍後在產生的另一個警示應屬於該事件，請將此設定設為 [ **啟用** ] （如果您想要讓警示建立新的事件），並保持為 **停用狀態** 。
    
        > [!NOTE]
        > 最多可將150個警示分組為單一事件。 如果有超過150的警示是由將這些警示組成單一事件的規則所產生，則會產生新的事件，並產生與原始事件相同的事件詳細資料，而且會將多餘的警示分組至新的事件。

## <a name="set-automated-responses-and-create-the-rule"></a>設定自動回應並建立規則

1. 在 [ **自動回應** ] 索引標籤中，選取當自訂規則產生警示時，要自動執行的任何工作手冊。 如需有關建立和自動化操作手冊的詳細資訊，請參閱 [回應威脅](tutorial-respond-threats-playbook.md)。

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="定義自動回應設定":::

1. 選取 [ **審核並建立** ] 以檢查新警示規則的所有設定，然後選取 [ **建立] 以初始化您的警示規則**。

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="檢查所有設定並建立規則":::

## <a name="view-the-rule-and-its-output"></a>查看規則及其輸出
  
1. 警示建立之後，會將自訂規則新增至 [作用中 **規則**] 下的資料表。 從這份清單中，您可以啟用、停用或刪除每個規則。

1. 若要查看您所建立警示規則的結果，請移至 [ **事件** ] 頁面，您可以在其中分級、 [調查事件](tutorial-investigate-cases.md)，以及補救威脅。

> [!NOTE]
> Azure Sentinel 中產生的警示可透過 [Microsoft Graph 安全性](/graph/security-concept-overview)來取得。 如需詳細資訊，請參閱 [Microsoft Graph 安全性警示檔](/graph/api/resources/security-api-overview)。

## <a name="troubleshooting"></a>疑難排解

### <a name="issue-no-events-appear-in-query-results"></a>問題：查詢結果中未顯示任何事件

如果 **事件群組** 設定為 **針對每個事件觸發警示**，則在某些情況下，在稍後查看查詢結果時 (例如，當您回溯至事件) 的警示時，可能不會出現任何查詢結果。 這是因為事件的警示連接是透過特定事件資訊的雜湊，以及在查詢中包含雜湊來完成。 如果查詢結果自從產生警示之後已經變更，雜湊將不再有效，也不會顯示任何結果。 

若要查看事件，請使用規則查詢中的雜湊手動移除該行，然後執行查詢。

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>問題：已排程的規則無法執行，或以自動停用的方式（已新增至名稱）顯示

排程查詢規則無法執行，但可能會發生這種情況。 Azure Sentinel 會根據失敗的特定類型和發生的情況，以暫時性或永久性的方式將失敗分類。

#### <a name="transient-failure"></a>暫時性失敗

暫時性失敗發生的原因是暫時性的，而且很快就會恢復正常，此時規則執行將會成功。 Azure Sentinel 分類為暫時性的一些失敗範例如下：

- 規則查詢花費的時間太長，而無法執行。
- 資料來源與 Log Analytics 之間，或 Log Analytics 與 Azure Sentinel 之間的連線能力問題。
- 任何其他新的和未知的失敗都會被視為暫時性。

如果發生暫時性失敗，Azure Sentinel 會繼續嘗試在預先決定且不斷增加的間隔之後再次執行此規則，直到某個點為止。 之後，規則只會在下次排程的時間執行。 因為暫時性失敗，所以永遠不會自動停用規則。

#### <a name="permanent-failure---rule-auto-disabled"></a>永久失敗-規則自動停用

因為允許執行規則的條件有變更，而不需要人為介入的情況下，將不會回復到先前的狀態，所以會發生永久失敗。 以下是分類為永久的一些失敗範例：

- 已刪除規則查詢操作) 的目標工作區 (。
- 已刪除規則查詢)  (的目標資料表。
- 已從目標工作區移除 Azure Sentinel。
- 規則查詢所使用的函式已不再有效;已修改或移除。
- 規則查詢的其中一個資料來源的許可權已變更。
- 規則查詢的其中一個資料來源已刪除或中斷連接。

**在預先決定的連續永久失敗數目（相同類型和相同規則）的情況下** ，Azure Sentinel 停止嘗試執行規則，而且也會採取下列步驟：

- 停用規則。
- 將「 **自動停用** 」單字新增至規則名稱的開頭。
- 新增失敗 (的原因和停用規則描述的) 。

您可以藉由依名稱排序規則清單，輕鬆地判斷是否有任何自動停用的規則存在。 自動停用的規則會位於清單頂端或附近。

SOC 管理員應務必定期檢查規則清單是否有自動停用的規則。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已瞭解如何使用 Azure Sentinel 開始偵測威脅。

若要瞭解如何將您對威脅的回應自動化，請 [在 Azure Sentinel 中設定自動威脅回應](tutorial-respond-threats-playbook.md)。
