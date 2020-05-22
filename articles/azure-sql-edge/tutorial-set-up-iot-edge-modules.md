---
title: 在 Azure SQL Edge 中設定 IoT Edge 模組
description: 在此 Azure SQL Edge 教學課程三部曲的第二部分中，若要預測鐵礦中的雜質含量，請設定 IoT Edge 模組和連線。
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: bbbbe09aac30165a2f9b7bbe54f58e0c09a6cf09
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593496"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>設定 IoT Edge 模組和連線

在此教學課程三部曲的第二部分中，若要在 Azure SQL Edge 中預測鐵礦中的雜質含量，請設定下列 IoT Edge 模組：

- Azure SQL Edge
- 資料產生器 IoT Edge 模組

## <a name="create-azure-stream-analytics-module"></a>建立 Azure 串流分析模組

建立將在本教學課程中使用的 Azure 串流分析模組。 若要深入了解如何搭配使用串流作業與 SQL Edge，請參閱[使用串流作業搭配 SQL Database Edge](https://docs.microsoft.com/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)。

建立 Azure 串流分析作業，並將裝載環境設定為 Edge 後，請設定教學課程的輸入和輸出。

1. 若要建立**輸入**，請按一下 [+新增串流輸入]。 使用下列資訊填入詳細資料區段：

   欄位|值
   -----|-----
   事件序列化格式|JSON
   編碼|UTF-8
   事件壓縮類型|None

2. 若要建立**輸出**，請按一下 [+新增]，然後選擇 [SQL Database]。 使用下列資訊填入詳細資料區段。

   > [!NOTE]
   > 在 **「部署 Azure SQL Edge 模組」** 一 節中部署 SQL Edge 模組時，必須針對 SQL SA 密碼指定此區段中所指定的密碼。

   欄位|值
   -----|-----
   資料庫|IronOreSilicaPrediction
   伺服器名稱|tcp:.,1433
   使用者名稱|sa
   密碼|指定強式密碼
   Table|IronOreMeasurements1

3. 瀏覽至**查詢**區段並設定查詢，如下所示：

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. 在**設定**底下，選取 [發佈]，然後選取 [發佈] 按鈕。 儲存用於 SQL Database Edge 模組的 SAS URI。

## <a name="specify-container-registry-credentials"></a>指定容器登錄認證

您可以對裝載模組映像的容器登錄指定認證。 您可在資源群組中建立的容器登錄找到這些認證。 瀏覽至**存取金鑰**區段。 記下下列欄位：

- 登錄名稱
- 登入伺服器
- 使用者名稱
- 密碼

現在，請在 IoT Edge 模組中指定容器認證。

1. 瀏覽至您在資源群組中建立的 IoT 中樞。

2. 在**自動裝置管理**下的 **IoT Edge** 區段中，按一下 [裝置識別碼]。 在本教學課程中，識別碼為 `IronOrePredictionDevice`。

3. 選取 [設定模組] 區段。

4. 在 [容器登錄認證] 下，輸入下列值：

   _欄位_|_ReplTest1_
   -------|-------
   名稱|登錄名稱
   位址|登入伺服器
   使用者名稱|使用者名稱
   密碼|密碼
  
## <a name="deploy-the-data-generator-module"></a>部署資料產生器模組

1. 在 [IoT Edge 模組] 區段中，按一下 [+新增]，然後選取 [IoT Edge 模組]。

2. 提供 IoT Edge 模組名稱和映像 URI。
   您可以在資源群組中的容器登錄找到映像 URI。 選取 [服務]底下的 [存放庫] 區段。 在本教學課程中，選擇名為 `silicaprediction` 的存放庫。 選取適當的標籤。 映像 URI 的格式會是：

   login server of the containerregistry/repository name:tag name

   例如：

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. 按一下 [新增]。

## <a name="deploy-the-azure-sql-edge-module"></a>部署 Azure SQL Edge 模組

1. 遵循[部署 Azure SQL Database Edge 預覽](https://docs.microsoft.com/azure/sql-database-edge/deploy-portal#deploy-sql-database-edge)中列出的步驟來部署 Azure SQL Edge 模組。

2. 在**設定模組**的**指定路由**頁面上，指定模組要與 IoT Edge 中樞通訊的路由，如下所示。 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   例如：

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. 在 [模組對應項] 設定中，確定已使用您稍早在本教學課程中儲存的相關 SAS URL 來更新 SQLPackage 和 ASAJonInfo。

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>後續步驟

- [使用 ONNX 在 Azure SQL Edge 上部署 ML 模型](tutorial-run-ml-model-on-sql-edge.md)
