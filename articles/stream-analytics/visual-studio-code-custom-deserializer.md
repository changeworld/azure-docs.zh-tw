---
title: 使用 Visual Studio Code 建立 Azure 串流分析雲端作業的自訂 .NET 還原序列化程式
description: 本教學課程示範如何使用 Visual Studio Code 建立 Azure 串流分析雲端作業的自訂 .NET 還原序列化程式。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 0f041910d1b02cc6d1fd48bfd773711c3361c0f5
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822459"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>在 Visual Studio Code 中建立適用于 Azure 串流分析的自訂 .NET 還原序列化程式

Azure 串流分析 [內建三種資料格式的支援](stream-analytics-parsing-json.md)： JSON、CSV 和 Avro。 使用自訂 .NET 還原序列化程式，您可以從其他格式讀取資料，例如 [通訊協定緩衝區](https://developers.google.com/protocol-buffers/)、 [證券紙](https://github.com/Microsoft/bond) ，以及其他使用者定義格式的雲端作業。

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Visual Studio Code 中的自訂 .NET 還原序列化程式

您可以使用 Visual Studio Code 來建立、測試及偵測 Azure 串流分析雲端作業的自訂 .NET 還原序列化程式。

### <a name="prerequisites"></a>必要條件

* 安裝 [.net CORE SDK](https://dotnet.microsoft.com/download) 並重新啟動 Visual Studio Code。

* 您可以使用本 [快速入門](quick-create-visual-studio-code.md) 來瞭解如何使用 Visual Studio Code 建立串流分析作業。

### <a name="create-a-custom-deserializer"></a>建立自訂還原序列化程式

1. 開啟終端機並執行下列命令，以在名為 **ProtobufDeserializer** 的自訂還原序列化程式的 Visual Studio Code 中建立 .net 類別庫。

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. 移至 **ProtobufDeserializer** 專案目錄，並安裝 [>mslearn-streamanalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) 和 [Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/) NuGet 套件。

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. 將 [MessageBodyProto 類別](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs)和 [MessageBodyDeserializer 類別](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs)新增至您的專案。

4. 建立 **ProtobufDeserializer** 專案。

### <a name="add-an-azure-stream-analytics-project"></a>新增 Azure 串流分析專案

1. 開啟 Visual Studio Code，然後選取 **Ctrl + Shift + P** 以開啟命令選擇區。 然後，輸入 ASA，並選取 **[ASA：建立新專案]** 。 將它命名為 **>protobufclouddeserializer**。

### <a name="configure-a-stream-analytics-job"></a>設定串流分析作業

1. 按兩下 **JobConfig.json**。 使用預設組態，但下列設定除外：

   |設定|建議的值|
   |-------|---------------|
   |全域儲存體設定資源|選擇目前帳戶中的資料來源|
   |全域儲存體設定訂用帳戶| < 您的訂用帳戶 >|
   |全域儲存體設定儲存體帳戶| < 您的儲存體帳戶 >|
   |CustomCodeStorage 設定儲存體帳戶|< 您的儲存體帳戶 >|
   |CustomCodeStorage 設定容器|< 您的儲存體容器 >|

2. 在 [ **輸入** 資料夾] 下，開啟 **input.js**]。 選取 [ **新增即時輸入** ]，然後從 Azure Data Lake Storage Gen2/Blob 儲存體新增輸入，選擇 **您的 Azure 訂用帳戶中** 的 [選取]。 使用預設組態，但下列設定除外：

   |設定|建議的值|
   |-------|---------------|
   |名稱|輸入|
   |訂用帳戶|< 您的訂用帳戶 >|
   |儲存體帳戶|< 您的儲存體帳戶 >|
   |容器|< 您的儲存體容器 >|
   |序列化類型|選擇 **自訂**|
   |SerializationProjectPath|選取 **[從 CodeLens 選擇程式庫專案路徑** ]，然後選取在上一節中建立的 **ProtobufDeserializer** 程式庫專案。 選取 **組建專案** 以建立專案|
   |SerializationClassName|選取 [從 CodeLens 選取還原序列化 **類別** ]，以自動填入類別名稱和 DLL 路徑|
   |類別名稱|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="新增自訂還原序列化輸入。":::

3. 將下列查詢加入至 **>protobufclouddeserializer. >script.asaql** 檔案。

   ```sql
   SELECT * FROM Input
   ```

4. 下載[範例 protobuf 輸入檔](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf)。 在 [ **輸入** ] 資料夾中，以滑鼠右鍵按一下 **input.js** ，然後選取 [ **新增本機輸入**]。 然後，按兩下 **local_input1.js** ，並使用預設設定，但下列設定除外。

   |設定|建議的值|
   |-------|---------------|
   |選取本機檔案路徑|按一下 [CodeLens]，以選取 < 下載的範例 protobuf 輸入檔的檔案路徑>|

### <a name="execute-the-stream-analytics-job"></a>執行串流分析作業

1. 開啟 **>protobufclouddeserializer >script.asaql** ，然後選取 [從 CodeLens **執行本機** ]，然後從下拉式清單中選擇 [ **使用本機輸入** ]。

2. 觀察右側 [作業圖表] 中 [ **結果** ] 索引標籤中的 [結果] 索引標籤。 您也可以按一下作業圖表中的步驟，以查看中繼結果。 如需詳細資料，請參閱 [使用作業圖表在本機進行調試](debug-locally-using-job-diagram-vs-code.md)。

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="檢查本機執行結果。":::

您已成功為您的串流分析作業執行自訂還原序列化程式！ 在本教學課程中，您已使用本機輸入資料在本機測試自訂還原序列化。 您也可以 [使用雲端中的即時資料輸入](visual-studio-code-local-run-live-input.md)來測試它。 若要在雲端中執行作業，您可以正確地設定輸入和輸出。 然後，從 Visual Studio Code 將作業提交至 Azure，以使用您剛剛執行的自訂還原序列化程式在雲端中執行您的作業。

### <a name="debug-your-deserializer"></a>對還原序列化程式進行偵錯

您可以透過用來對標準 .NET 程式碼進行偵錯的相同方式，在本機對 .NET 還原序列化程式進行偵錯。 

1. 在您的 .NET 函數中新增中斷點。

2. 按一下 [從 Visual Studio Code] 活動列中的 [ **執行** ]，然後選取 [ **建立 launch.js** 檔案]。
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="建立啟動檔案。":::

   從下拉式清單中選擇 [ **>protobufclouddeserializer** ]，然後選擇 [ **Azure 串流分析** ]。
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="建立開機檔案2。":::

   編輯檔案 **上的launch.js** ，將 <ASAScript> >script.asaql 取代為 >protobufclouddeserializer. >script.asaql。
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="設定啟動檔案。":::

3. 按 **F5** 開始偵錯。 程式將如預期般地在您的中斷點處停止。 這適用于本機輸入和即時輸入資料。

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="調試自訂還原序列化。":::

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、串流作業和所有相關資源。 刪除作業可避免因為作業使用串流單位而產生費用。 如果您計劃在未來使用該作業，您可以將其停止並在之後需要時重新啟動。 如果您不再繼續使用此作業，請使用下列步驟，刪除本教學課程所建立的所有資源：

1. 從 Azure 入口網站的左側功能表中，選取 [資源群組]  ，然後選取您所建立資源的名稱。  

2. 在資源群組頁面上，選取 [刪除]  ，在文字方塊中輸入要刪除的資源名稱，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何為通訊協定緩衝區輸入序列化實作自訂的 .NET 還原序列化程式。 若要深入了解如何建立自訂還原序列化程式，請繼續閱讀下列文章：

> [!div class="nextstepaction"]
> * [為 Azure 串流分析作業建立不同的 .NET 還原序列化程式](custom-deserializer-examples.md)
> * [使用 Visual Studio Code 在本機測試 Azure 串流分析作業與即時輸入](visual-studio-code-local-run-live-input.md)