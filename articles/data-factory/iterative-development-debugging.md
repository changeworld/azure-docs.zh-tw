---
title: Azure Data Factory 中的反復式開發和調試
description: 瞭解如何在 ADF UX 中反復開發和調試 Data Factory 管線
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: f1f81af715bc4b2248a24076f3b12a74d0ee73e3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102061"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>透過 Azure Data Factory 的反覆式開發與偵錯
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 可讓您在開發資料整合解決方案時，反復地開發和偵測 Data Factory 管線。 這些功能可讓您在建立提取要求或將其發佈至 data factory 服務之前，先測試您的變更。 

如需此功能的簡介與示範，請觀看下列 8 分鐘長的影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>對管線進行偵錯

當您使用管線畫布進行撰寫時，您可以使用「偵測」 **功能來** 測試您的活動。 當您執行測試回合時，您不必在選取 [偵錯]  前，先將變更發佈至資料處理站。 如果您想要在更新資料處理站工作流程之前，先確保變更如預期般運作，此功能會很有幫助。

![管線畫布上的偵錯功能](media/iterative-development-debugging/iterative-development-1.png)

當管線正在執行時，您可以在管線畫布的 [ **輸出** ] 索引標籤中看到每個活動的結果。

在管線畫布的 [輸出]  視窗中檢視測試回合的結果。

![管線畫布的輸出視窗](media/iterative-development-debugging/iterative-development-2.png)

測試回合成功之後，將更多活動新增至您的管線並繼續以反覆方式進行偵錯。 您也可以 [取消]  正在進行的測試回合。

> [!IMPORTANT]
> 選取 [偵錯]  實際上會執行管線。 例如，如果管線包含複製活動，則測試回合會將資料從來源複製到目的地。 如此一來，我們建議您在偵錯時，於複製活動或其他活動中使用測試資料夾。 完成管線偵錯之後，請切換到您要在正常作業中使用的實際資料夾。

### <a name="setting-breakpoints"></a>設定中斷點

Azure Data Factory 可讓您在管線畫布上到達特定活動之前，對管線進行偵錯工具。 將中斷點放在要測試的活動上，然後選取 [ **Debug** ]。 Data Factory 可確保測試回合只會進行到管線畫布上的中斷點活動。 如果您不想測試整個管線，而只想測試管線內的部分活動，這項 [偵錯直到]  功能很實用。

![管線畫布上的中斷點](media/iterative-development-debugging/iterative-development-3.png)

若要設定中斷點，請選取管線畫布上的元素。 [偵錯直到]  選項會在元素的右上角顯示為空心的紅色圓圈。

![對選取的元素設定中斷點之前](media/iterative-development-debugging/iterative-development-4.png)

在您選取 [偵錯直到]  選項後，它會變更為實心的紅色圓圈，以指出中斷點已啟用。

![對選取的元素設定中斷點之後](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>監視偵錯回合

當您執行管線的偵錯工具時，結果會出現在管線畫布的 [ **輸出** ] 視窗中。 [輸出] 索引標籤只會包含目前瀏覽器會話期間發生的最新執行。 

![管線畫布的輸出視窗](media/iterative-development-debugging/iterative-development-2.png)

若要查看偵錯工具執行的歷程記錄，或查看所有使用中的偵錯工具執行清單，您可以進入 **監視器** 體驗。 

![選取 [檢視作用中偵錯回合] 圖示](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> Azure Data Factory 服務只會保存15天的 debug 執行歷程記錄。 

## <a name="debugging-mapping-data-flows"></a>調試對應資料流程

對應資料流程可讓您建立可大規模執行的無程式碼資料轉換邏輯。 建立您的邏輯時，您可以開啟 debug 會話，以互動方式使用即時 Spark 叢集來處理您的資料。 若要深入瞭解，請參閱 [對應資料流程](concepts-data-flow-debug-mode.md)的偵測模式。

您可以在 **監視器** 體驗中監視處理站之間的作用中資料流程偵測會話。

![查看資料流程的 debug 會話](media/iterative-development-debugging/view-dataflow-debug-sessions.png)
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>使用資料流程活動來對管線進行調試

執行具有資料流程的 debug 管線時，您有兩個選項可供使用的計算。 您可以使用現有的 debug 叢集，或為您的資料流程加速新的即時叢集。

使用現有的偵測會話會大幅減少資料流程啟動的時間，因為叢集已在執行中，但不建議針對複雜或平行的工作負載，因為在多個作業一次執行時可能會失敗。

使用活動執行時間將會使用每個資料流程活動的整合執行時間中指定的設定來建立新的叢集。 這可讓每個作業隔離，而且應該用於複雜的工作負載或效能測試。 您也可以在 Azure IR 中控制 TTL，讓用於偵錯工具的叢集資源仍可在該期間內使用，以提供額外的作業要求。

> [!NOTE]
> 如果您的管線中有平行執行的資料流程，請選擇 [使用活動執行時間]，讓 Data Factory 可以使用您在資料流程活動中選取的 Integration Runtime。 這可讓資料流程在多個叢集上執行，並可容納平行的資料流程執行。

![使用資料流程執行管線](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>下一步

測試您的變更之後，請使用 [Azure Data Factory 中的持續整合和部署](continuous-integration-deployment.md)，將這些變更升階到更高的環境。
