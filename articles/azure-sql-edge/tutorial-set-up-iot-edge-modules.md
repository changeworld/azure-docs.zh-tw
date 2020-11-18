---
title: 在 Azure SQL Edge 中設定 IoT Edge 模組
description: 在此 Azure SQL Edge 教學課程三部曲的第二部分中，若要預測鐵礦中的雜質含量，請設定 IoT Edge 模組和連線。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 75e6ebaea4c5ba883820d2309212b35fed128142
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422122"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>設定 IoT Edge 模組和連線

在此教學課程三部曲的第二部分中，若要在 Azure SQL Edge 中預測鐵礦中的雜質含量，請設定下列 IoT Edge 模組：

- Azure SQL Edge
- 資料產生器 IoT Edge 模組

## <a name="specify-container-registry-credentials"></a>指定容器登錄認證

您可以對裝載模組映像的容器登錄指定認證。 您可在資源群組中建立的容器登錄找到這些認證。 瀏覽至 **存取金鑰** 區段。 記下下列欄位：

- 登錄名稱
- 登入伺服器
- 使用者名稱
- 密碼

現在，請在 IoT Edge 模組中指定容器認證。

1. 瀏覽至您在資源群組中建立的 IoT 中樞。

2. 在 **自動裝置管理** 下的 **IoT Edge** 區段中，按一下 [裝置識別碼]。 在本教學課程中，識別碼為 `IronOrePredictionDevice`。

3. 選取 [設定模組] 區段。

4. 在 [容器登錄認證] 下，輸入下列值：

   _欄位_|_ReplTest1_
   -------|-------
   名稱|登錄名稱
   位址|登入伺服器
   使用者名稱|使用者名稱
   密碼|密碼
  
## <a name="build-push-and-deploy-the-data-generator-module"></a>建置、推送及部署資料產生器模組

1. 將[專案檔案](https://github.com/microsoft/sqlsourabh/tree/main/SQLEdgeSamples/IoTEdgeSamples/IronOreSilica)複製到您的電腦。
2. 使用 Visual Studio 2019 開啟 **IronOre_Silica_Predict.sln**
3. 更新 **deployment.template.json** 中的容器登錄詳細資料 
   ```json
   "registryCredentials":{
        "RegistryName":{
            "username":"",
            "password":""
            "address":""
        }
    }
   ```
4. 更新 **modules.json** 檔案以指定目標容器登錄 (或模組的存放庫)
   ```json
   "image":{
        "repository":"samplerepo.azurecr.io/ironoresilicapercent",
        "tag":
    }
   ```
5. 在偵錯或發行模式中執行專案，以確保專案執行時不會發生任何問題 
6. 以滑鼠右鍵按一下專案名稱然後選取 [建置和推送 IoT Edge 模組]，將專案推送至您的容器登錄。
7. 將資料產生器模組作為 IoT Edge 模組部署到您的 Edge 裝置。 

## <a name="deploy-the-azure-sql-edge-module"></a>部署 Azure SQL Edge 模組

1. 依序按一下 [+ 新增] 和 [Marketplace 模組]，以部署 Azure SQL Edge 模組。 

2. 在 [IoT Edge 模組 Marketplace] 刀鋒視窗上，搜尋 *Azure SQL Edge*，然後挑選 [Azure SQL Edge 開發人員]。 

3. 按一下 [IoT Edge 模組] 底下新增的 [Azure SQL Edge] 模組，以設定 Azure SQL Edge 模組。 如需設定選項的詳細資訊，請參閱[部署 Azure SQL Edge](./deploy-portal.md)。

4. 將 `MSSQL_PACKAGE` 環境變數新增至 *Azure SQL Edge* 模組部署，並指定資料庫 dacpac 檔案的 SAS URL (您在本教學課程 [第一部份](tutorial-deploy-azure-resources.md)的步驟 8 中建立了這個檔案)。

5. 按一下 [更新]

6. 在 [在裝置上設定模組] 頁面上，按 [下一步：路由 >]。

7. 在 [在裝置上設定模組] 頁面上的 [路由] 窗格中，指定要供模組與 IoT Edge 中樞通訊的路由，如下所述。 請務必更新下列路由定義中的模組名稱。

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   例如：

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. 在 [在裝置上設定模組] 頁面上，按 [下一步：檢閱 + 建立 >]

8. 在 [在裝置上設定模組] 頁面上，按一下 [建立]

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>在 Azure SQL Edge 中建立並啟動 T-SQL 串流作業。

1. 開啟 Azure Data Studio。

2. 在 **歡迎使用** 索引標籤中，使用下列詳細資料開始新的連線：

   |_欄位_|_ReplTest1_|
   |-------|-------|
   |連線類型| Microsoft SQL Server|
   |伺服器|在此示範中建立的 VM 中提到的公用 IP 位址|
   |使用者名稱|sa|
   |密碼|建立 Azure SQL Edge 執行個體時所使用的強式密碼|
   |資料庫|預設|
   |伺服器群組|預設|
   |名稱 (選用)|提供選用的名稱|

3. 按一下 [連接] 

4. 在 [檔案] 功能表索引標籤中開啟新的筆記本，或使用鍵盤快速鍵 Ctrl + N。

5. 在新的 [查詢] 視窗中，執行下列指令碼來建立 T-SQL 串流作業。 執行指令碼之前，請務必變更下列變數。 
   - *SQL_SA_Password：* 部署 Azure SQL Edge 模組時所指定的 MSSQL_SA_PASSWORD 值。 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. 使用下列查詢來確認資料產生模組中的資料正在串流到資料庫。 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


在本教學課程中，我們已部署資料產生器模組和 SQL Edge 模組。 然後，我們建立了串流作業，以將資料產生模組所產生的資料串流至 SQL。 

## <a name="next-steps"></a>後續步驟

- [使用 ONNX 在 Azure SQL Edge 上部署 ML 模型](tutorial-run-ml-model-on-sql-edge.md)