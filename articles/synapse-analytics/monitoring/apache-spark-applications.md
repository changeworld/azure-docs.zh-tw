---
title: 使用 Synapse Studio 監視 Apache Spark 的應用程式
description: 使用 Synapse Studio 來監視您的 Apache Spark 應用程式。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 9f55d65b215c03769caa7c812dcda138f8e83595
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322469"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>使用 Synapse Studio 監視您 Apache Spark 的應用程式

使用 Azure Synapse Analytics 時，您可以使用 Apache Spark 在您的工作區中的 Apache Spark 集區上執行筆記本、作業及其他類型的應用程式。

本文說明如何監視您的 Apache Spark 應用程式，讓您可以留意最新狀態、問題和進度。

本教學課程涵蓋下列工作：

* 監視正在執行 Apache Spark 應用程式
* 查看已完成的 Apache Spark 應用程式
* View Apache Spark 應用程式的已取消
* Apache Spark 應用程式的偵錯工具失敗

## <a name="prerequisites"></a>必要條件

開始本教學課程之前，請確定您符合下列需求：

- Synapse Studio 工作區。 如需相關指示，請參閱 [建立 Synapse Studio 工作區](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace)。

- 無伺服器 Apache Spark 集區。

## <a name="monitor-running-apache-spark-application"></a>監視正在執行 Apache Spark 應用程式

開啟 [ **監視** ]，然後選取 [ **Apache Spark 應用程式** ]。 若要查看執行中 Apache Spark 應用程式的詳細資料，請選取 [提交 Apache Spark 應用程式]，並查看詳細資料。 如果 Apache Spark 應用程式仍在執行中，您可以監視進度。

  ![選取正在執行的作業](./media/how-to-monitor-spark-applications/select-running-job.png)

1. 檢查 **已完成** 的工作、 **狀態** 和 **總持續時間** 。

2. 取消 Apache Spark 的應用程式。

3. 重新整理記錄查詢。

4. 觀看圖形。

5. 檢查 **摘要** 資訊。

6. 檢查 **記錄** 檔。 執行時，記錄檔資訊是空的。

    ![查看正在執行的作業](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-completed-apache-spark-application"></a>查看已完成的 Apache Spark 應用程式

開啟 [ **監視** ]，然後選取 [ **Apache Spark 應用程式** ]。 若要查看已完成的 Apache Spark 應用程式的詳細資料，請選取 Apache Spark 的應用程式，並查看詳細資料。

  ![選取已完成的作業](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. 檢查 **已完成** 的工作、 **狀態** 和 **總持續時間** 。

2. 重新整理記錄查詢。

3. 按一下 [ **Spark 歷程記錄伺服器** ]，開啟 Apache Spark 歷程記錄伺服器連結。

4. 按一下圖形中的圖示來檢查 **摘要** 資訊。

5. 檢查 **記錄** 檔。 您可以從下拉式清單中選取不同類型的記錄，也可以按一下 [ **下載記錄** 檔] 來下載記錄檔資訊。

6. 您可以在產生的作業圖形中查看作業的總覽。 依預設，圖表會顯示所有工作。 您可以依 **作業識別碼** 篩選此視圖。

7. 依預設，會選取 **進度** 顯示。 您可以在 [ **顯示** ] 下拉式清單中選取 [ **讀取** ] 或 [ **寫入** ] 來檢查資料流程。

8. 若要播放作業，請選取 [ **播放** ]。 您可以隨時選擇 **停止** 。

9. 您可以使用滑鼠滾輪來放大和縮小作業圖形，或選取 [ **縮放至適當比例** ]，使其符合螢幕大小。

10. [作業圖表] 節點會顯示每個階段的下列資訊：

    * 識別碼。

    * 名稱或描述。

    * 總工作數。

    * 讀取的資料：輸入大小和隨機讀取大小的總和。

    * 資料寫入：輸出大小和隨機寫入大小的總和。

    * 執行時間：第一次嘗試的開始時間與最後一次嘗試的完成時間之間的時間。

    * 資料列計數：輸入記錄、輸出記錄、隨機讀取記錄和隨機寫入記錄的總和。

    * 進度。

     ![查看已完成的作業](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. 按一下圖形，接著會顯示階段的詳細資料。

   ![階段的詳細資料](./media/how-to-monitor-spark-applications/details-for-stage.png)

## <a name="view-canceled-apache-spark-application"></a>View Apache Spark 應用程式的已取消

開啟 [ **監視** ]，然後選取 [ **Apache Spark 應用程式** ]。 若要查看已取消的 Apache Spark 應用程式的詳細資料，請選取 Apache Spark 的應用程式，並查看詳細資料。

 ![選取取消的作業](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. 檢查 **已完成** 的工作、 **狀態** 和 **總持續時間** 。

2. 重新整理記錄查詢。

3. 按一下 [ **Spark 歷程記錄伺服器** ] 以開啟 [Apache 歷程記錄伺服器] 連結。

4. 觀看圖形。

5. 檢查 **摘要** 資訊。

6. 檢查 **記錄** 檔。 您可以從下拉式清單中選取不同類型的記錄，也可以按一下 [ **下載記錄** 檔] 來下載記錄檔資訊。

   ![查看取消的作業](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Apache Spark 應用程式的偵錯工具失敗

開啟 [ **監視** ]，然後選取 [ **Apache Spark 應用程式** ]。 若要查看失敗 Apache Spark 應用程式的詳細資料，請選取 Apache Spark 應用程式，並查看詳細資料。

![選取失敗的作業](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. 檢查 **已完成** 的工作、 **狀態** 和 **總持續時間** 。

2. 重新整理記錄查詢。

3. 按一下 [ **Spark 歷程記錄伺服器** ]，開啟 Apache Spark 歷程記錄伺服器連結。

4. 觀看圖形。

5. 檢查 **摘要** 資訊。

6. 檢查錯誤資訊。

   ![失敗的作業資訊](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>後續步驟

如需有關監視管線執行的詳細資訊，請參閱 [使用 Synapse Studio 的監視器管線執行](how-to-monitor-pipeline-runs.md) 一文。  
