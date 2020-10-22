---
title: 教學課程：搭配 Azure Cosmos DB 和事件中樞來使用 Java 函式
description: 本教學課程會說明如何從事件中樞取用事件，以在 Azure Cosmos DB 中使用以 Java 撰寫的函式進行更新。
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 8ba15047d04d3af25518e8c55a50c55bcacf9037
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103929"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>教學課程：使用事件中樞觸發程序和 Azure Cosmos DB 輸出繫結來建立以 Java 撰寫的函式

本教學課程會說明如何使用 Azure Functions 來建立 Java 函式，以分析溫度和壓力資料的連續資料流。 代表感應器讀數的事件中樞事件會觸發函式。 函式會處理事件資料，然後將狀態項目新增至 Azure Cosmos DB。

在本教學課程中，您將會：

> [!div class="checklist"]
> * 使用 Azure CLI 來建立和設定 Azure 資源。
> * 建立和測試會與這些資源互動的 Java 函式。
> * 將函式部署到 Azure，並使用 Application Insights 加以監視。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您必須安裝下列項目：

* [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) 第 8 版
* [Apache Maven](https://maven.apache.org) 3.0 版或更新版本
* [Azure CLI](/cli/azure/install-azure-cli) (如果您不想要使用 Cloud Shell 的話)
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) 2.6.666 版或更新版本

> [!IMPORTANT]
> `JAVA_HOME` 環境變數必須設定為 JDK 的安裝位置，才能完成本教學課程。

如果您想要直接使用本教學課程的程式碼，請參閱 [java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) 範例存放庫。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>建立 Azure 資源

在本教學課程中，您將需要下列資源：

* 資源群組，以便包含其他資源
* 事件中樞的命名空間、事件中樞和授權規則
* Cosmos DB 帳戶、資料庫和集合
* 函式應用程式和用來裝載函式應用程式的儲存體帳戶

下列各節會說明如何使用 Azure CLI 來建立這些資源。

### <a name="log-in-to-azure"></a>登入 Azure

如果您不使用 Cloud Shell，就必須在本機使用 Azure CLI 來存取您的帳戶。 從 Bash 提示字元使用 `az login` 命令，啟動以瀏覽器為基礎的登入體驗。 如果您可以存取多個 Azure 訂用帳戶，請使用 `az account set --subscription` 並於後面加上訂用帳戶識別碼來設定預設的 Azure 訂用帳戶。

### <a name="set-environment-variables"></a>設定環境變數

接下來，針對您將建立的資源，為其名稱和位置建立一些環境變數。 使用下列命令，並將 `<value>` 預留位置取代為您選擇的值。 所選擇的這些值應符合 [Azure 資源的命名規則和限制](/azure/architecture/best-practices/resource-naming)。 針對 `LOCATION` 變數，請使用 `az functionapp list-consumption-locations` 命令所產生的其中一個值。

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

本教學課程的其餘部分會使用這些變數。 請注意，這些變數只會在目前 Azure CLI 或 Cloud Shell 工作階段的持續期間內存在。 如果您使用不同的本機終端機視窗，抑或是 Cloud Shell 工作階段逾時，您就必須再次執行這些命令。

### <a name="create-a-resource-group"></a>建立資源群組

Azure 會使用資源群組來收集您帳戶中的所有相關資源。 如此一來，您就可以進行整體檢視，並在用完這些資源時，透過單一命令即可將其全部刪除。

請使用下列命令來建立資源群組：

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>建立事件中樞

接下來，請使用下列命令建立 Azure 事件中樞的命名空間、事件中樞和授權規則：

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

事件中樞的命名空間包含實際的事件中樞和其授權規則。 授權規則可讓您的函式將訊息傳送給中樞，並接聽對應的事件。 一個函式會傳送代表遙測資料的訊息。 另一個函式則會接聽事件、分析事件資料，並將結果儲存在 Azure Cosmos DB 中。

### <a name="create-an-azure-cosmos-db"></a>建立 Azure Cosmos DB

接下來，請使用下列命令建立 Azure Cosmos DB 帳戶、資料庫和集合：

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

`partition-key-path` 值會根據每個項目的 `temperatureStatus` 值來分割您的資料。 分割區索引鍵可讓 Cosmos DB 將您的資料分成可供其獨立存取的不同子集，藉以提升效能。

### <a name="create-a-storage-account-and-function-app"></a>建立儲存體帳戶和函式應用程式

接下來，請建立 Azure Functions 所需的 Azure 儲存體帳戶，然後建立函式應用程式。 使用下列命令：

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

`az functionapp create` 命令在建立您的函式應用程式時，也會一併建立具有相同名稱的 Application Insights 資源。 函式應用程式則會使用名為 `APPINSIGHTS_INSTRUMENTATIONKEY` 的設定 (可供其連線至 Application Insights) 自動進行設定。 將函式部署至 Azure 之後，您就可以檢視應用程式遙測資料，如本教學課程稍後所述。

## <a name="configure-your-function-app"></a>設定函式應用程式

函式應用程式必須存取其他資源才能正常運作。 下列各節會說明如何設定函式應用程式，使其可在您的本機電腦上執行。

### <a name="retrieve-resource-connection-strings"></a>擷取資源的連接字串

請使用下列命令來擷取儲存體、事件中樞和 Cosmos DB 的連接字串，並將這些連接字串儲存在環境變數中：

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

這些變數會設定為透過 Azure CLI 命令所擷取到的值。 每個命令都會使用 JMESPath 查詢，從傳回的 JSON 承載中擷取連接字串。 連接字串也會使用 `echo` 加以顯示，以便讓您確認是否已成功擷取連接字串。

### <a name="update-your-function-app-settings"></a>更新函式應用程式的設定

接下來，請使用下列命令將連接字串值傳送至 Azure Functions 帳戶中的應用程式設定：

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

您的 Azure 資源現已建立完成，並已設定為可正常搭配運作。

## <a name="create-and-test-your-functions"></a>建立和測試函式

接下來，您會在本機電腦上建立專案、新增 Java 程式碼，並加以測試。 您會使用可與適用於 Maven 的 Azure Functions 外掛程式和 Azure Functions Core Tools 搭配運作的命令。 您的函式會在本機執行，但會使用您已建立的雲端式資源。 讓函式在本機運作之後，您就可以使用 Maven 將函式部署至雲端，並觀看資料和分析的累積。

如果您使用了 Cloud Shell 來建立資源，則不會在本機連線至 Azure。 在此情況下，請使用 `az login` 命令來啟動以瀏覽器為基礎的登入流程。 然後，如有必要，請使用 `az account set --subscription` 並於後面加上訂用帳戶識別碼來設定預設的訂用帳戶。 最後，請執行下列命令在本機電腦上重新建立某些環境變數。 將 `<value>` 預留位置取代為您先前使用的相同值。

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>建立本機 Functions 專案

請使用下列 Maven 命令來建立函式專案，並新增所需的相依性。

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

此命令會在 `telemetry-functions` 資料夾內產生數個檔案：

* `pom.xml` 檔案，用來與 Maven 搭配使用
* `local.settings.json` 檔案，用來保存應用程式設定以進行本機測試
* `host.json` 檔案，可啟用 Azure Functions 擴充功能套件組合，此為資料分析函式中的 Cosmos DB 輸出繫結所需項目
* `Function.java` 檔案，會包含預設的函式實作
* 本教學課程不需要的幾個測試檔案

為避免編譯錯誤，您必須刪除測試檔案。 請執行下列命令以瀏覽至新的專案資料夾，並刪除測試資料夾：

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>擷取函式應用程式設定以供本機使用

為了進行本機測試，函式專案會需要您稍早在本教學課程中新增至 Azure 中函式應用程式的連接字串。 請使用下列 Azure Functions Core Tools 命令，來擷取儲存在雲端中的所有函式應用程式設定，並將這些設定新增至 `local.settings.json` 檔案：

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>新增 Java 程式碼

接下來，請開啟 `Function.java` 檔案，並以下列程式碼取代內容。

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

如您所見，此檔案包含兩個函式，分別是 `generateSensorData` 和 `processSensorData`。 `generateSensorData` 函式會模擬感應器，以將溫度和壓力讀數傳送至事件中樞。 計時器觸發程序會每隔 10 秒執行函式一次，而事件中樞輸出繫結則會將傳回值傳送至事件中樞。

事件中樞會在收到訊息時產生事件。 `processSensorData` 函式會在收到事件時開始執行。 然後，此函式會處理事件資料，並使用 Azure Cosmos DB 輸出繫結將結果傳送至 Azure Cosmos DB。

這些函式所使用的資料會使用名為 `TelemetryItem` 的類別來加以儲存，而您必須加以實作。 在與 `Function.java` 相同的位置中建立名為 `TelemetryItem.java` 的新檔案，並新增下列程式碼：

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>在本機執行

您現在可以在本機建置和執行函式，並且會看到資料出現在 Azure Cosmos DB 中。

請使用下列 Maven 命令來建置和執行函式：

```bash
mvn clean package
mvn azure-functions:run
```

在某些建置和啟動訊息過後，您會在每次函式執行時，看到類似下列範例的輸出：

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

然後，您可以移至 [Azure 入口網站](https://portal.azure.com)，並瀏覽至 Azure Cosmos DB 帳戶。 選取 [資料總管]  ，展開 [TelemetryInfo]  ，然後選取 [項目]  以便在資料抵達時進行檢視。

![Cosmos DB 資料總管](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>部署至 Azure 並檢視應用程式遙測資料

最後，您可以將應用程式部署至 Azure，並確認其可以和在本機時一樣繼續以相同方式運作。

使用下列命令將專案部署至 Azure：

```bash
mvn azure-functions:deploy
```

您的函式現在會在 Azure 中執行，並繼續在 Azure Cosmos DB 中累積資料。 您可以在 Azure 入口網站中檢視已部署的函式應用程式，並透過已連線的 Application Insights 資源來檢視應用程式遙測資料，如下列螢幕擷取畫面所示：

**即時計量資料流：**

![Application Insights 即時計量資料流](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**效能：**

![Application Insights [效能] 刀鋒視窗](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>清除資源

當您完成使用本教學課程中建立的 Azure 資源時，您可以使用下列命令將其刪除：

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立 Azure 函式，以處理事件中樞的事件並更新 Cosmos DB。 如需詳細資訊，請參閱 [Azure Functions Java 開發人員指南](./functions-reference-java.md)。 如需所用註釋的相關資訊，請參閱 [com.microsoft.azure.functions.annotation](/java/api/com.microsoft.azure.functions.annotation) 參考資料。

本教學課程使用了環境變數和應用程式設定來儲存秘密 (例如連接字串)。 如需在 Azure Key Vault 中儲存這些秘密的相關資訊，請參閱[使用 App Service 和 Azure Functions 的 Key Vault 參考](../app-service/app-service-key-vault-references.md)。

接下來，請了解如何使用 Azure Pipelines CI/CD 來進行自動化部署：

> [!div class="nextstepaction"]
> [建置 Java 並將其部署至 Azure Functions](/azure/devops/pipelines/ecosystems/java-function)