---
title: 教學課程 - Azure 串流分析雲端作業的自訂 .NET 還原序列化程式
description: 本教學課程示範如何使用 Visual Studio 建立 Azure 串流分析雲端作業的自訂 .NET 還原序列化程式。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/17/2020
ms.openlocfilehash: 2353d15707fe215bfcab7912f2a9c598c4af7e49
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822007"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>教學課程：Azure 串流分析的自訂 .NET 還原序列化程式

Azure 串流分析具有[三種資料格式的內建支援](stream-analytics-parsing-json.md)：JSON、CSV 和 Avro。 透過自訂 .NET 還原序列化程式，您可以讀取其他格式的資料，例如[通訊協定緩衝區](https://developers.google.com/protocol-buffers/)、[Bond](https://github.com/Microsoft/bond)，以及雲端和 Edge 作業的其他使用者定義格式。

本教學課程示範如何使用 Visual Studio 建立 Azure 串流分析雲端作業的自訂 .NET 還原序列化程式。 若要了解如何在 Visual Studio Code 中建立 .NET 還原序列化程式，請參閱[在 Visual Studio Code 中建立適用於 Azure 串流分析作業的 .NET 還原序列化程式](visual-studio-code-custom-deserializer.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立通訊協定緩衝區的自訂還原序列化程式。
> * 在 Visual Studio 中建立 Azure 串流分析作業。
> * 將您的串流分析作業設定為使用自訂還原序列化程式。
> * 在本機執行串流分析作業，以測試自訂還原序列化程式，並進行偵錯。


## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 安裝 [Visual Studio 2019 (建議)](https://www.visualstudio.com/downloads/) 或 [Visual Studio 2017](https://www.visualstudio.com/vs/older-downloads/)。 支援 Enterprise (Ultimate/Premium)、Professional 和 Community 版本。 不支援 Express 版本。 

* [安裝適用於 Visual Studio 的串流分析工具](stream-analytics-tools-for-visual-studio-install.md)或更新至最新版本。 

* 在 Visual Studio 中開啟 **Cloud Explorer**，然後登入您的 Azure 訂用帳戶。

* 在 Azure 儲存體帳戶中建立容器。
您建立的容器將用來儲存與串流分析作業有關的資產。 如果您的儲存體帳戶已經內含現有容器，則您可以使用它們。 如果沒有，您必須[建立新的容器](../storage/blobs/storage-quickstart-blobs-portal.md)。

## <a name="create-a-custom-deserializer"></a>建立自訂還原序列化程式

1. 開啟 Visual Studio，然後選取 [檔案] > [新增] > [專案]。 搜尋 **串流分析**，並選取 [Azure 串流分析自訂還原序列化程式專案 (.NET)]。 指定專案的名稱，例如 **Protobuf 還原序列化程式**。

   ![建立 Visual Studio dotnet 標準類別庫專案](./media/custom-deserializer/create-dotnet-library-project.png)

2. 在方案總管中，以滑鼠右鍵按一下 **Protobuf 還原序列化程式** 專案，然後從功能表中選取 [管理 NuGet 套件]。 然後，安裝 **Microsoft.Azure.StreamAnalytics** 和 **Google.Protobuf** NuGet 套件。

3. 將 [MessageBodyProto 類別](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs)和 [MessageBodyDeserializer 類別](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs)新增至您的專案。

4. 建置 **Protobuf 還原序列化程式** 專案。

## <a name="add-an-azure-stream-analytics-project"></a>新增 Azure 串流分析專案

1. 在方案總管中，以滑鼠右鍵按一下 **Protobuf 還原序列化程式** 方案，然後選取 [新增] > [新增專案]。 在 [Azure 串流分析] > [串流分析] 底下，選擇 [Azure 串流分析應用程式]。 將其命名 **ProtobufCloudDeserializer**，然後選取 [確定]。 

2. 以滑鼠右鍵按一下 **ProtobufCloudDeserializer** Azure 串流分析專案下的 [參考]。 在 [專案] 底下，新增 **Protobuf 還原序列化程式**。 此項目應該會自動填入。

## <a name="configure-a-stream-analytics-job"></a>設定串流分析作業

1. 按兩下 **JobConfig.json**。 使用預設組態，但下列設定除外：

   |設定|建議的值|
   |-------|---------------|
   |全域儲存體設定資源|選擇目前帳戶中的資料來源|
   |全域儲存體設定訂用帳戶| < 您的訂用帳戶 >|
   |全域儲存體設定儲存體帳戶| < 您的儲存體帳戶 >|
   |自訂程式碼儲存體設定資源|選擇目前帳戶中的資料來源|
   |自訂程式碼儲存體設定儲存體帳戶|< 您的儲存體帳戶 >|
   |自訂程式碼儲存體設定容器|< 您的儲存體容器 >|

2. 在 [輸入] 底下，按兩下 **Input.json**。 使用預設組態，但下列設定除外：

   |設定|建議的值|
   |-------|---------------|
   |來源|Blob 儲存體|
   |資源|選擇目前帳戶中的資料來源|
   |訂用帳戶|< 您的訂用帳戶 >|
   |儲存體帳戶|< 您的儲存體帳戶 >|
   |容器|< 您的儲存體容器 >|
   |事件序列化格式|其他 (Protobuf、XML、專屬...)|
   |資源|從 ASA 專案參考或 CodeBehind 載入|
   |CSharp 組件名稱|ProtobufDeserializer.dll|
   |類別名稱|MessageBodyProto.MessageBodyDeserializer|
   |事件壓縮類型|無|

3. 將下列查詢新增至 **script.asaql** 檔案。

   ```sql
   SELECT * FROM Input
   ```

4. 下載[範例 protobuf 輸入檔](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf)。 在 [輸入] 資料夾中，以滑鼠右鍵按一下 [Input.json]，然後選取 [新增本機輸入]。 然後，按兩下 **local_Input.json**，並設定下列設定：

   |設定|建議的值|
   |-------|---------------|
   |輸入別名|輸入|
   |來源類型|資料流|
   |事件序列化格式|其他 (Protobuf、XML、專屬...)|
   |CSharp 組件名稱|ProtobufDeserializer.dll|
   |類別名稱|MessageBodyProto.MessageBodyDeserializer|
   |本機輸入檔案路徑|< 已下載的範例 protobuf 輸入檔的檔案路徑 >|

## <a name="execute-the-stream-analytics-job"></a>執行串流分析作業

1. 開啟 **script.asaql**，並選取 [在本機執行]。

2. 觀察 [串流分析本機執行結果] 中的結果。

您已成功為您的串流分析作業執行自訂還原序列化程式！ 在本教學課程中，您在本機測試了自訂還原序列化程式。 針對您的實際資料，您應適當設定輸入和輸出。 然後，從 Visual Studio 將作業提交至 Azure，以使用您剛剛實作的自訂還原序列化程式在雲端中執行您的作業。

## <a name="debug-your-deserializer"></a>對還原序列化程式進行偵錯

您可以透過用來對標準 .NET 程式碼進行偵錯的相同方式，在本機對 .NET 還原序列化程式進行偵錯。

1. 以滑鼠右鍵按一下 **ProtobufCloudDeserializer** 專案名稱，並將其設定為啟始專案。

2. 在函式中新增中斷點。

3. 按 **F5** 開始偵錯。 程式將如預期般地在您的中斷點處停止。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、串流作業和所有相關資源。 刪除作業可避免因為作業使用串流單位而產生費用。 如果您計劃在未來使用該作業，您可以將其停止並在之後需要時重新啟動。 如果您不再繼續使用此作業，請使用下列步驟，刪除本教學課程所建立的所有資源：

1. 從 Azure 入口網站的左側功能表中，選取 [資源群組]  ，然後選取您所建立資源的名稱。  

2. 在資源群組頁面上，選取 [刪除]  ，在文字方塊中輸入要刪除的資源名稱，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何為通訊協定緩衝區輸入序列化實作自訂的 .NET 還原序列化程式。 若要深入了解如何建立自訂還原序列化程式，請繼續閱讀下列文章：

> [!div class="nextstepaction"]
> [為 Azure 串流分析作業建立不同的 .NET 還原序列化程式](custom-deserializer-examples.md)