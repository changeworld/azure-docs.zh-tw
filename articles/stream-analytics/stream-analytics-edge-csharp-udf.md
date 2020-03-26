---
title: 教學課程 - 在 Visual Studio 中針對 Azure 串流分析作業撰寫 C# 使用者定義的函式 (預覽)
description: 本教學課程說明如何在 Visual Studio 中針對串流分析作業撰寫 C# 使用者定義的函式。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 1d71f4c5616efb05efe2733c49507b085ca2dcf6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75426304"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>教學課程：針對 Azure 串流分析作業撰寫 C# 使用者定義的函式 (預覽)

在 Visual Studio 中建立的 C# 使用者定義函式 (UDF)，可讓您使用自己的函式來延伸 Azure 串流分析查詢語言。 您可以重複使用現有的程式碼 (包括 DLL)，並利用 C# 來使用數學或複雜的邏輯。 有三種方式可以實作 UDF：串流分析專案中的 CodeBehind 檔案、來自本機 C# 專案的 UDF 或來自儲存體帳戶之現有套件的 UDF。 此教學課程會使用 CodeBehind 方法來實作基本的 C# 函式。 適用於串流分析作業的 UDF 功能目前為預覽狀態，不應該用於生產環境工作負載。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用 CodeBehind 來建立 C# 使用者定義的函式。
> * 在本機測試您的串流分析作業。
> * 將您的作業發佈至 Azure。

## <a name="prerequisites"></a>Prerequisites

開始之前，請先確定您已完成下列必要條件：

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 安裝[適用於 Visual Studio 的串流分析工具](stream-analytics-tools-for-visual-studio-install.md)與 **Azure 開發**或**資料儲存和處理**工作負載。
* 如果您要建立 IoT Edge 作業 (stream-analytics-tools-for-visual-studio-edge-jobs.md)，請參閱現有的 [串流分析 Edge 開發指南]。

## <a name="create-a-container-in-your-azure-storage-account"></a>在 Azure 儲存體帳戶中建立容器

您建立的容器會用來儲存已編譯的 C# 封裝。 如果要建立 Edge 作業，也會使用此儲存體帳戶將套件部署到您的 IoT Edge 裝置。 針對每一個串流分析作業使用專用容器。 不支援針對多個串流分析 Edge 作業重複使用相同的容器。 如果您的儲存體帳戶已經內含現有容器，則您可以使用它們。 如果沒有，您必須[建立新的容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>在 Visual Studio 中建立串流分析專案

1. 啟動 Visual Studio。

2. 選取 [檔案] > [新增] > [專案]  。

3. 在左側的範本清單中，選取 [串流分析]  ，然後選取 [Azure 串流分析 Edge 應用程式]  或 [Azure 串流分析應用程式]  。

4.  輸入專案的 [名稱]  、[位置]  和 [解決方案名稱]  ，然後選取 [確定]  。

    ![在 Visual Studio 中建立 Azure 串流分析 Edge 專案](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>設定組件專案路徑

1. 開啟 Visual Studio 並瀏覽至 [方案總管]  。

2. 按兩下作業設定檔 `EdgeJobConfig.json`。

3. 展開 [使用者定義的程式碼設定]  區段，然後在設定中填入以下建議的值：

   |**設定**|**建議的值**|
   |-------|---------------|
   |全域儲存體設定資源|選擇目前帳戶中的資料來源|
   |全域儲存體設定訂用帳戶| < 您的訂用帳戶 >|
   |全域儲存體設定儲存體帳戶| < 您的儲存體帳戶 >|
   |自訂程式碼儲存體設定資源|選擇目前帳戶中的資料來源|
   |自訂程式碼儲存體設定儲存體帳戶|< 您的儲存體帳戶 >|
   |自訂程式碼儲存體設定容器|< 您的儲存體容器 >|


## <a name="write-a-c-udf-with-codebehind"></a>使用 CodeBehind 撰寫 C# UDF
CodeBehind 檔案是與單一 ASA 查詢指令碼相關聯的 C# 檔案。 Visual Studio 工具將會自動壓縮 CodeBehind 檔案，並在提交時將它上傳至您的 Azure 儲存體帳戶。 所有類別都必須定義為 *public*，而所有物件都必須定義為 *static public*。

1. 在 [方案總管]  中，展開 **Script.asql** 以尋找 **Script.asaql.cs** CodeBehind 檔案。

2. 使用下列範例來取代程式碼：

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>實作 UDF

1. 在 [方案總管]  中開啟 **Script.asaql** 檔案。

2. 使用下列內容來取代現有的查詢：

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>本機測試

1. 下載[溫度模擬器範例資料檔案](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json)。

2. 在 [方案總管]  中，展開 [輸入]  、以滑鼠右鍵按一下 [Input.json]  ，然後選取 [新增本機輸入]  。

   ![在 Visual Studio 中將本機輸入新增至串流分析作業](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. 為您下載的範例資料指定本機輸入檔案路徑並**儲存**。

    ![Visual Studio 中適用於串流分析作業的本機輸入設定](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. 在指令碼編輯器中，按一下 [在本機執行]  。 一旦本機執行已成功儲存輸出結果之後，按下任意鍵以查看表格格式的結果。 

    ![使用 Visual Studio 在本機執行 Azure 串流分析作業](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. 您也可以選取 [開啟結果資料夾]  來查看 JSON 與 CSV 格式的原始檔案。

    ![使用 Visual Studio 檢視本機 Azure 串流分析作業的結果](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>針對 UDF 進行偵錯
您可以透過您用來針對標準 C# 程式碼進行偵錯的相同方式，在本機針對 C# UDF 進行偵錯。 

1. 在 C# 函式中新增中斷點。

    ![將中斷點新增至 Visual Studio 中串流分析使用者定義的函式](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. 按 **F5** 開始偵錯。 程式將如預期般地在您的中斷點處停止。

    ![檢視串流分析使用者定義的函式偵錯結果](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>將您的作業發佈至 Azure
一旦在本機測試過您的查詢之後，請在指令碼編輯器中選取 [提交至 Azure]  ，以將作業發佈到 Azure。

![從 Visual Studio 將您的串流分析 Edge 作業提交至 Azure](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>部署到 IoT Edge 裝置
如果您選擇建置串流分析 Edge 作業，現在即可將其部署為 IoT Edge 模組。 請依照 [IoT Edge 快速入門](https://docs.microsoft.com/azure/iot-edge/quickstart)來建立 IoT 中樞、註冊 IoT Edge 裝置，以及在您的裝置上安裝並啟動 IoT Edge 執行階段。 接著，依照[部署作業](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job)教學課程，以將您的串流分析作業部署為 IoT Edge 模組。 

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已使用 CodeBehind 來建立簡單的 C# 使用者定義函式、將您的作業發佈至 Azure，並將該作業部署到 Azure 或 IoT Edge 裝置。 

若要深入了解針對串流分析作業使用 C# 使用者定義函式的不同方式，請繼續閱讀此文章：

> [!div class="nextstepaction"]
> [針對 Azure 串流分析撰寫 C# 函式](stream-analytics-edge-csharp-udf-methods.md)
