---
title: 使用 Synapse Studio 監視 Apache Spark 應用程式
description: 使用 Synapse Studio 來監視您的 Apache Spark 應用程式。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 4d6c50436ddf68e2610aeb10ddfaaab0a5d060f3
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387348"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>使用 Synapse Studio 來監視您的 Apache Spark 應用程式

透過 Azure Synapse Analytics，您可以使用 Apache Spark，在工作區中的 Apache Spark 集區上執行筆記本、作業和其他類型的應用程式。

本文說明如何監視您的 Apache Spark 應用程式，讓您留意最新狀態、問題和進度。

本教學課程涵蓋下列工作：

* 監視執行 Apache Spark 應用程式
* 查看已完成 Apache Spark 應用程式
* 已取消 Apache Spark 應用程式的 View
* Apache Spark 應用程式的 Debug 失敗

## <a name="prerequisites"></a>必要條件

開始本教學課程之前，請確定您符合下列需求：

- Synapse Studio 工作區。 如需指示，請參閱[建立 Synapse Studio 工作區](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace)。

- Apache Spark 集區。

## <a name="monitor-running-apache-spark-application"></a>監視執行 Apache Spark 應用程式

開啟 [**監視器**]，然後選取 [ **Apache Spark 應用程式**]。 若要查看正在執行之 Apache Spark 應用程式的詳細資料，請選取 [提交 Apache Spark] 應用程式並查看詳細資料。 如果 Apache Spark 應用程式仍在執行中，您可以監視進度。

  ![選取執行中的作業](./media/how-to-monitor-spark-applications/select-running-job.png)

1. 檢查**已完成**的工作、**狀態**和**持續時間總計**。

2. 取消 Apache Spark 應用程式。

3. 重新整理記錄查詢。

4. 觀看圖形。

5. 檢查**摘要**資訊。

6. 檢查**記錄**。 執行時，記錄資訊是空的。

    ![觀看執行中的作業](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-completed-apache-spark-application"></a>查看已完成 Apache Spark 應用程式

開啟 [**監視器**]，然後選取 [ **Apache Spark 應用程式**]。 若要查看已完成 Apache Spark 應用程式的詳細資料，請選取 [Apache Spark] 應用程式並查看詳細資料。

  ![選取已完成的工作](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. 檢查**已完成**的工作、**狀態**和**持續時間總計**。

2. 重新整理記錄查詢。

3. 按一下 [ **Spark 歷程記錄伺服器**]，以開啟 Apache Spark 歷程記錄伺服器] 連結。

4. 按一下圖形中的圖示，以檢查**摘要**資訊。

5. 檢查**記錄**。 您可以從下拉式清單中選取不同類型的記錄檔，也可以按一下 [**下載記錄**] 來下載記錄資訊。

6. 您可以在產生的作業圖形中查看作業的總覽。 根據預設，圖表會顯示所有作業。 您可以依**工作識別碼**篩選此視圖。

7. 預設會選取 [**進度**] 顯示。 您可以選取 [**顯示**] 下拉式清單中的 [**讀取**] 或 [**寫入**]，以檢查資料流程。

8. 若要播放作業，請選取 [**播放**]。 您可以隨時選取 [**停止**] 來停止。

9. 使用滑鼠滾輪來放大和縮小作業圖形，或選取 [**縮放至適當比例**]，使其符合螢幕大小。

10. [作業圖形] 節點會顯示每個階段的下列資訊：

    * 識別碼。

    * 名稱或描述。

    * 總工作數。

    * 讀取的資料：輸入大小和隨機讀取大小的總和。

    * 資料寫入：輸出大小和隨機寫入大小的總和。

    * 執行時間：第一次嘗試的開始時間與最後一次嘗試的完成時間之間的時間。

    * 資料列計數：輸入記錄、輸出記錄、隨機讀取記錄和隨機寫入記錄的總和。

    * 進度。

     ![查看已完成的工作](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. 按一下圖形，[階段] 的詳細資料就會顯示出來。

   ![階段的詳細資料](./media/how-to-monitor-spark-applications/details-for-stage.png)

## <a name="view-canceled-apache-spark-application"></a>已取消 Apache Spark 應用程式的 View

開啟 [**監視器**]，然後選取 [ **Apache Spark 應用程式**]。 若要查看已取消之 Apache Spark 應用程式的詳細資料，請選取 [Apache Spark] 應用程式並查看詳細資料。

 ![選取已取消的工作](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. 檢查**已完成**的工作、**狀態**和**持續時間總計**。

2. 重新整理記錄查詢。

3. 按一下 [ **Spark 歷程記錄伺服器**]，以開啟 [Apache 記錄伺服器] 連結。

4. 觀看圖形。

5. 檢查**摘要**資訊。

6. 檢查**記錄**。 您可以從下拉式清單中選取不同類型的記錄檔，也可以按一下 [**下載記錄**] 來下載記錄資訊。

   ![查看已取消的工作](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Apache Spark 應用程式的 Debug 失敗

開啟 [**監視器**]，然後選取 [ **Apache Spark 應用程式**]。 若要查看失敗 Apache Spark 應用程式的詳細資料，請選取 [Apache Spark] 應用程式並查看詳細資料。

![選取失敗的作業](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. 檢查**已完成**的工作、**狀態**和**持續時間總計**。

2. 重新整理記錄查詢。

3. 按一下 [ **Spark 歷程記錄伺服器**]，以開啟 Apache Spark 歷程記錄伺服器] 連結。

4. 觀看圖形。

5. 檢查**摘要**資訊。

6. 請檢查錯誤資訊。

   ![失敗的作業資訊](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>後續步驟

如需有關監視管線執行的詳細資訊，請參閱[使用 Synapse Studio 執行監視管線](how-to-monitor-pipeline-runs.md)一文。  
