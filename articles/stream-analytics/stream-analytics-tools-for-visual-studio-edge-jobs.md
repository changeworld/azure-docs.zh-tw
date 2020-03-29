---
title: 視覺化工作室中的 Azure 流分析邊緣作業
description: 本文介紹如何使用視覺化工作室的流分析工具在 IoT 邊緣作業上創作、調試和創建流分析。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 07f109b3d5539f7cd87a12fb42a36803573c2bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354559"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>使用 Visual Studio 工具來開發串流分析 Edge 作業

在本教程中，您將瞭解如何將流分析工具用於視覺化工作室。 您將瞭解如何創作、調試和創建流分析邊緣作業。 建立和測試作業之後，您可以前往 Azure 入口網站將其部署到您的裝置。 

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要下列必要條件：

* 安裝[視覺工作室 2019](https://visualstudio.microsoft.com/downloads/)，[視覺工作室 2015，](https://www.visualstudio.com/vs/older-downloads/)或[視覺工作室 2013 更新 4](https://www.microsoft.com/download/details.aspx?id=45326). 支援 Enterprise (Ultimate/Premium)、Professional 和 Community 版本。 不支援 Express 版本。  

* 按照[安裝說明](stream-analytics-tools-for-visual-studio-edge-jobs.md)為視覺化工作室安裝流分析工具。
 
## <a name="create-a-stream-analytics-edge-project"></a>建立串流分析 Edge 專案 

從視覺化工作室中，選擇 **"檔** > **新專案** > **"。** 巡覽至左側 [範本]**** 清單 > 展開 [Azure 串流分析]**** > [串流分析 Edge]**** > [Azure 串流分析 Edge 應用程式]****。 為您的專案提供名稱、位置及解決方案名稱，然後選取 [確定]****。

![視覺化工作室中的新流分析邊緣專案](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

建立專案之後，請巡覽至 [方案總管]**** 以檢視資料夾階層。

![串流分析 Edge 作業的解決方案總管檢視](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>選擇正確的訂用帳戶

1. 在 Visual Studio 的 [檢視]**** 功能表中，選取 [伺服器總管]****。  

2. 以滑鼠右鍵按一下 [Azure]****，選取 [連線到 Microsoft Azure 訂用帳戶]****，然後以您的 Azure 帳戶登入。

## <a name="define-inputs"></a>定義輸入

1. 從 [方案總管]**** 中，展開 [輸入]**** 節點，您應該會看到名為 **EdgeInput.json** 的輸入。 按兩下以檢視其設定。  

2. 將 [來源類型] 設定為 [資料流]****。 然後將 [來源] 設定為 [Edge 中樞]****，將 [事件序列化格式] 設定為 [Json]****，以及將 [編碼] 設定為 [UTF8]****。 (選擇性) 您可以將 [輸入別名]**** 重新命名，在此範例中，我們將它保持原狀。 如果您將輸入別名重新命名，請使用您定義查詢時指定的名稱。 選取 [儲存] **** 以儲存設定。  
   ![串流分析作業輸入組態](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>定義輸出

1. 在 [方案總管]**** 中，展開 [輸出]**** 節點，您應該會看到名為 **EdgeOutput.json** 的輸出。 按兩下以檢視其設定。  

2. 請確保將 Sink 設置為選擇**邊緣中心**，將事件序列化格式設置為**Json，** 將編碼設置為**UTF8**，並設置格式**陣列**。 (選擇性) 您可以將 [輸出別名]**** 重新命名，在此範例中，我們將它保持原狀。 如果您將輸出別名重新命名，請使用您定義查詢時指定的名稱。 選取 [儲存] **** 以儲存設定。 
   ![串流分析作業輸出組態](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>定義轉換查詢

在流分析 IoT 邊緣環境中部署的流分析作業支援大多數[流分析查詢語言引用](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396)。 但是，流分析邊緣作業尚不支援以下操作： 


|**類別目錄**  | **命令**  |
|---------|---------|
|其他運算子 | <ul><li>PARTITION BY</li><li>TIMESTAMP BY OVER</li><li>JavaScript UDF</li><li>使用者定義的聚合 （UDA）</li><li>GetMetadataPropertyValue</li><li>在一個步驟中使用超過 14 個聚合</li></ul>   |

在門戶中創建流分析邊緣作業時，編譯器將自動警告您，如果您沒有使用受支援的運算子。

在您的 Visual Studio 中，請在查詢編輯器中定義下列轉換查詢 (**script.asaql 檔案**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>在本機測試作業

若要在本機測試查詢，您應該上傳範例資料。 您可以從 [GitHub 存放庫](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json)下載註冊資料並將它儲存到本機電腦，以取得範例資料。 

1. 若要上傳範例資料，請以滑鼠右鍵按一下 [EdgeInput.json]**** 檔案並選擇 [新增本機輸入]****  

2. 在快顯視窗中，從本機路徑 [瀏覽]**** 範例資料 > 選取 [儲存]****。
   ![Visual Studio 中的本機輸入組態](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. 系統會自動在您的 inputs 資料夾中新增名為 **local_EdgeInput.json** 的檔案。  
4. 您可以在本地運行它或提交到 Azure。 要測試查詢，請選擇"**在本地運行**"。  
   ![Visual Studio 中的串流分析作業執行選項](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. 命令提示字元視窗會顯示作業的狀態。 作業執行成功時，它會在您的專案資料夾路徑 "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42" 中建立一個類似 "2018-02-23-11-31-42" 的資料夾。 巡覽至資料夾路徑以檢視本機資料夾中的結果：

   您也可以登入 Azure 入口網站，並確認已建立作業。 

   ![串流分析作業結果資料夾](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>將作業提交至 Azure

1. 將作業提交至 Azure 之前，您必須連線到 Azure 訂用帳戶。 開啟 [伺服器總管]**** > 以滑鼠右鍵按一下 [Azure]**** > [連線到 Microsoft Azure 訂用帳戶]**** > 登入您的 Azure 訂用帳戶。  

2. 若要將作業提交至 Azure，請巡覽至查詢編輯器 > 選取 [提交至 Azure]****。  

3. 將打開一個快顯視窗。 選擇更新現有的流分析邊緣作業或創建新的作業。 更新現有作業時，它將替換所有作業配置，在此方案中，您將發佈新作業。 選取 [建立新的 Azure 串流分析作業]**** > 為您的作業輸入名稱 (例如，MyASAEdgeJob)**** > 選擇所需的 [訂用帳戶]****、[資源群組]**** 和 [位置]**** > 選取 [提交]****。

   ![從 Visual Studio 將串流分析作業提交至 Azure](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   現在，您的流分析邊緣作業已創建。 您可以參考[IoT Edge 教程中的"運行作業](stream-analytics-edge.md)"，瞭解如何將其部署到設備。 

## <a name="manage-the-job"></a>管理作業 

您可以在 [伺服器總管] 中檢視作業的狀態和作業圖表。 從**伺服器資源管理器**中的**流分析**，展開部署流分析邊緣作業的訂閱和資源組。 您可以檢視 [已建立]**** 狀態的 MyASAEdgejob。 展開作業節點並按兩下來開啟作業檢視。

![伺服器總管作業管理選項](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
作業檢視視窗會為您提供諸如重新整理作業、刪除作業，以及從 Azure 入口網站開啟作業等作業。

![Visual Studio 中的作業圖表和其他選項](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>後續步驟

* [Azure IoT Edge 的詳細資訊](../iot-edge/about-iot-edge.md)
* [ASA IoT Edge 教學課程](../iot-edge/tutorial-deploy-stream-analytics.md)
* [使用這個調查將意見反應傳送給小組](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
