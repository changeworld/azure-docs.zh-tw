---
title: 教學課程 - 使用 Azure IoT Edge 以 SQL 模組儲存資料
description: 本教學課程說明如何使用 SQL Server 模組將資料儲存在 IoT Edge 裝置的本機上
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2cee92ad03bb96ee7553876912a14af33af72271
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808740"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>教學課程：使用 SQL Server 資料庫在邊緣儲存資料

部署 SQL Server 模組，以將資料儲存在執行 Azure IoT Edge 的 Linux 裝置上。

使用 Azure IoT Edge 和 SQL Server 儲存和查詢 Edge 上的資料。 Azure IoT Edge 有基本儲存體功能，可在裝置離線時快取訊息，然後在連線重新建立時轉送訊息。 不過，您可能想要有更進階的儲存體功能，例如能夠在本機查詢資料。 您的 IoT Edge 裝置可使用本機資料庫來執行更複雜的計算，而無須保有 IoT 中樞的連線。

本文提供如何將 SQL Server 資料庫部署到 IoT Edge 裝置的指示。 在 IoT Edge 裝置上執行的 Azure Functions 會將傳入的資料結構化，然後傳送到資料庫。 本文中的步驟也可套用到會在 MySQL 或 PostgreSQL 等容器中工作的其他資料庫。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 使用 Visual Studio Code 來建立 Azure Function
> * 將 SQL 資料庫部署至您的 IoT Edge 裝置
> * 使用 Visual Studio Code 建置模組，並將其部署至您的 IoT Edge 裝置
> * 檢視產生的資料

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

在開始本教學課程之前，您應該已經完成先前的教學課程，以針對 Linux 容器開發設定您的開發環境：[開發適用於 Linux 裝置的 IoT Edge 模組](tutorial-develop-for-linux.md)。 完成該教學課程之後，您應該會具備下列必要條件：

* Azure 中的免費或標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* [執行 Azure IoT Edge 的 AMD64 Linux 裝置](quickstart-linux.md)。
  * ARM 裝置 (例如 Raspberry Pi) 無法執行 SQL Server。 如果您想要在 ARM 裝置上使用 SQL，您可以註冊試用 [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/) 預覽版。
* 容器登錄，像是 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)。
* 已設定 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 的 [Visual Studio Code](https://code.visualstudio.com/)。
* [Docker CE](https://docs.docker.com/install/) 設定為執行 Linux 容器。

本教學課程會使用 Azure Functions 模組，將資料傳送至 SQL Server。 若要以 Azure Functions 開發 IoT Edge 模組，請在您的開發機器上安裝下列其他必要條件：

* [C# for Visual Studio Code (由 OmniSharp 提供技術支援) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) (適用於 Visual Studio Code)。
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。

## <a name="create-a-function-project"></a>建立函式專案

若要將資料傳送到資料庫中，您需要可將資料適當結構化，並將其儲存在資料表中的模組。

### <a name="create-a-new-project"></a>建立新專案

下列步驟會示範如何使用 Visual Studio Code 和 Azure IoT 工具來建立 IoT Edge 函式。

1. 開啟 Visual Studio Code。

2. 選取 [檢視] > [命令選擇區]，以開啟 VS Code 命令選擇區。

3. 在命令選擇區中，輸入並執行命令 Azure IoT Edge:**新增 IoT Edge 解決方案**。 在命令選擇區中提供下列資訊，以建立解決方案：

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取資料夾 | 選擇開發機器上可供 VS Code 建立解決方案檔案的位置。 |
   | 提供解決方案名稱 | 輸入解決方案的描述性名稱 (例如 **SqlSolution**)，或接受預設值。 |
   | 選取模組範本 | 選擇 [Azure Functions - C#]。 |
   | 提供模組名稱 | 將模組命名為 **sqlFunction**。 |
   | 提供模組的 Docker 映像存放庫 | 映像存放庫包含容器登錄名稱和容器映像名稱。 系統會預先填入上一個步驟的容器映像。 將 **localhost:5000** 取代為 Azure Container Registry 的**登入伺服器**值。 您可以在 Azure 入口網站中，從容器登錄的概觀頁面擷取登入伺服器。 <br><br>最終字串看起來會類似於：\<registry name\>.azurecr.io/sqlfunction。 |

   VS Code 視窗會載入您的 IoT Edge 方案工作區。

### <a name="add-your-registry-credentials"></a>新增登錄認證

環境檔案會儲存容器登錄的認證，並與 IoT Edge 執行階段共用這些認證。 執行階段需要有這些認證才能將私人映像提取到 IoT Edge 裝置。

IoT Edge 擴充功能會嘗試從 Azure 提取您的容器登錄認證，並將這些認證填入到環境檔案中。 請查看您的認證是否已包含其中。 如果沒有，請立即新增：

1. 在 VS Code 總管中，開啟 .env 檔案。
2. 使用從 Azure Container Registry 複製過來的 [使用者名稱]  和 [密碼]  值來更新欄位。
3. 儲存這個檔案。

### <a name="select-your-target-architecture"></a>選取您的目標架構

目前，Visual Studio Code 可以開發適用於 Linux AMD64 和 Linux ARM32v7 裝置的 C 模組。 您必須為每個解決方案都選取要作為目標的架構，因為容器是針對每個架構類型，以不同方式建置和執行。 預設值為 Linux AMD64。

1. 開啟命令選擇區並搜尋 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或選取視窗底部側邊欄的捷徑圖示。

2. 在命令選擇區中，從選項清單中選取目標架構。 針對此教學課程，我們是使用 Ubuntu 虛擬機器作為 IoT Edge 裝置，因此會保留預設 **amd64**。

### <a name="update-the-module-with-custom-code"></a>使用自訂程式碼來更新模組

1. 在 VS Code 總管中，開啟 [模組] > [sqlFunction] > [sqlFunction.csproj]。

2. 尋找套件參考群組，然後新增一個新群組以包含 SqlClient。

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

3. 儲存 **sqlFunction.csproj** 檔案。

4. 開啟 **sqlFunction.cs** 檔案。

5. 將檔案的整個內容取代為下列程式碼：

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");";
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}
       }
   }
   ```

6. 在第 35 行中，將字串 **\<sql connection string\>** 取代為下列字串。 [資料來源] 屬性會參考尚不存在的 SQL Server 容器。 在下一節中，您將使用 **SQL** 這個名稱來建立容器。

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. 儲存 **sqlFunction.cs** 檔案。

## <a name="add-the-sql-server-container"></a>新增 SQL Server 容器

[部署資訊清單](module-composition.md)會宣告 IoT Edge 執行階段會在 IoT Edge 裝置上安裝哪些模組。 您在上一節中提供了用來建立自訂函式模組的程式碼，但該 SQL Server 模組早已建置完成並可在 Azure Marketplace 中取得。 您只需要指示 IoT Edge 執行階段納入該模組，然後在裝置上加以設定即可。

1. 在 Visual Studio Code 中，選取 [檢視] > [命令選擇區]，以開啟命令選擇區。

2. 在命令選擇區中，輸入並執行命令 [Azure IoT Edge:新增 IoT Edge 模組]。 在命令選擇區中，提供下列資訊以新增模組：

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取部署範本檔案 | 命令選擇區會醒目提示您目前解決方案資料夾中的 **deployment.template.json** 檔案。 選取該檔案。  |
   | 選取模組範本 | 選取 [來自 Azure Marketplace 的模組]。 |

3. 在 Azure IoT Edge 模組市集中搜尋並選取 [SQL Server 模組]。

4. 將模組名稱變更為 **sql** (全部小寫)。 此名稱符合 sqlFunction.cs 檔案中的連接字串所宣告的容器名稱。

5. 選取 [匯入] 將模組新增至您的解決方案。

6. 在您的解決方案資料夾中，開啟 **deployment.template.json** 檔案。

7. 尋找 [模組] 區段。 您應該會看到三個模組。 SimulatedTemperatureSensor 模組預設會包含在新的解決方案內，並提供測試資料供您與其他模組搭配使用。 sqlFunction 模組就是您一開始所建立，並使用新程式碼加以更新的模組。 最後，sql 模組則是從 Azure Marketplace 匯入的。

   >[!Tip]
   >SQL Server 模組在推出時已在部署資訊清單的環境變數中設定有預設密碼。 每當您在生產環境中建立了 SQL Server 容器時，就應該[變更預設的系統管理員密碼](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)。

8. 關閉 **deployment.template.json** 檔案。

## <a name="build-your-iot-edge-solution"></a>建置 IoT Edge 解決方案

在前幾節中，您以一個模組建立了解決方案，然後將另一個模組新增至部署資訊清單範本。 SQL Server 模組會由 Microsoft 公開裝載，但您必須在 Functions 模組中將該程式碼容器化。 在本節中，您會建置解決方案、建立 sqlFunction 模組的容器映像，並將其推送至容器登錄。

1. 在 Visual Studio Code 中，選取 [檢視] > [終端機]，以開啟整合式終端機。  

1. 在 Visual Studio Code 中登入您的容器登錄，以便將您的映像推送到登錄。 請使用您新增至 .env 檔案中的同一個 Azure Container Registry (ACR) 認證。 在整合式終端機中輸入下列命令：

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    您也可能看到安全性警告，建議您使用 --password-stdin 參數。 其使用超出本文的範圍時，建議您遵循此最佳做法。 如需詳細資訊，請參閱 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 命令參考。

1. 在 VS Code 總管中，以滑鼠右鍵按一下 **deployment.template.json** 檔案，然後選取 [建置並推送 IoT Edge 解決方案]。

   建置和推送命令會啟動三項作業。 首先，它會在解決方案中建立名為 **config** 的新資料夾，以保存完整部署資訊清單 (根據部署範本中的資訊建立)，以及其他解決方案檔案。 接著，它會執行 `docker build`，以根據目標架構的適當 dockerfile 建置容器映像。 然後，它會執行 `docker push` 以將映像存放庫推送至您的容器登錄。

   此程序第一次進行時可能需要幾分鐘的時間，但下一次執行命令時速度就會變快。

   您可以確認 sqlFunction 模組是否已成功推送至容器登錄。 在 Azure 入口網站中，瀏覽到您的容器登錄。 選取 [存放庫] 並搜尋 **sqlFunction**。 另外兩個模組 (SimulatedTemperatureSensor 和 sql) 將不會推送至容器登錄，因為其存放庫已經在 Microsoft 登錄中。

## <a name="deploy-the-solution-to-a-device"></a>將解決方案部署至裝置

您可以透過 IoT 中樞在裝置上設定模組，但您也可以透過 Visual Studio Code 存取 IoT 中樞和裝置。 在本節中，您會設定對 IoT 中樞的存取，然後使用 VS Code 將解決方案部署至您的 IoT Edge 裝置。

1.  在 Visual Studio Code 總管中，展開 [Azure IoT 中樞] 區段下的 [裝置] 來查看您的 IoT 裝置清單。

2. 以滑鼠右鍵按一下部署的目標裝置，然後選取 [建立單一裝置的部署]。

3. 選取 **config** 資料夾中的 **deployment.amd64.json** 檔案，然後按一下 [選取 Edge 部署資訊清單]。 請勿使用 deployment.template.json 檔案。

4. 請展開裝置下的**模組**，以查看已部署且執行中的模組清單。 按一下 [重新整理] 按鈕。 您應該會看到新的 **sql** 和 **sqlFunction** 正在與 **SimulatedTemperatureSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。

    您也可以查看並確認所有模組均已在裝置上啟動並執行。 在 IoT Edge 裝置上執行下列命令，以查看模組的狀態。

   ```cmd/sh
   iotedge list
   ```

    模組可能需要幾分鐘才會啟動。 IoT Edge 執行階段需要接收其新的部署資訊清單、從容器執行階段提取模組映像，然後啟動每個新的模組。

## <a name="create-the-sql-database"></a>建立 SQL 資料庫

當您將部署資訊清單套用至裝置時，您會有三個執行中的模組。 SimulatedTemperatureSensor 模組會產生模擬的環境資料。 sqlFunction 模組會取用資料，並針對資料庫加以格式化。 本節會引導您設定 SQL 資料庫，以便儲存溫度資料。

在您的 IoT Edge 裝置上執行下列命令。 這些命令會連線到在您裝置上執行的 **sql** 模組，並建立資料庫和資料表，以保存正傳送給它的溫度資料。

1. 在 IoT Edge 裝置的命令列工具中，連線到您的資料庫。

      ```bash
      sudo docker exec -it sql bash
      ```

2. 開啟 SQL 命令工具。

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. 建立您的資料庫：

      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. 定義您的資料表。

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

您可以自訂 SQL Server Docker 檔案，以便自動設定您要部署在多個 IoT Edge 裝置上的 SQL Server。 如需詳細資訊，請參閱 [Microsoft SQL Server 容器示範專案](https://github.com/twright-msft/mssql-node-docker-demo-app)。

## <a name="view-the-local-data"></a>檢視本機資料

在您的資料表建立後，sqlFunction 模組即會開始將資料儲存在 IoT Edge 裝置上的本機 SQL Server 2017 資料庫中。

在 SQL 命令工具中執行下列命令，以檢視您格式化的資料表資料：

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![檢視本機資料庫的內容](./media/tutorial-store-data-sql-server/view-data.png)

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。 您可以也繼續使用相同的 IoT Edge 裝置作為測試裝置。

否則，可以刪除您在本文中建立的本機組態和 Azure 資源，以避免產生費用。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 Azure Functions 模組，用以篩選您的 IoT Edge 裝置所產生的原始資料。 當您準備好要建置自己的模組時，您可以進一步了解如何[使用適用於 Visual Studio Code 的 Azure IoT Edge 開發 Azure Functions](how-to-develop-csharp-function.md)。

如果您想要在邊緣嘗試另一個儲存體方法，請閱讀如何在 IoT Edge 上使用 Azure Blob 儲存體。

> [!div class="nextstepaction"]
> [在 IoT Edge 使用 Azure Blob 儲存體，以便在邊緣儲存資料](how-to-store-data-blob.md)
