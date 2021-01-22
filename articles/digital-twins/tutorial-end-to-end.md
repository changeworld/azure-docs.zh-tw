---
title: 教學課程：連線端對端解決方案
titleSuffix: Azure Digital Twins
description: 建置由裝置資料驅動的端對端 Azure Digital Twins 解決方案教學課程。
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 4f68eba8106a20d357fe6d3fb2baac1d1661aa1e
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660533"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>教學課程：建置端對端解決方案

若要設定由環境即時資料驅動的完整端對端解決方案，您可以將 Azure Digital Twins 執行個體連線到其他 Azure 服務，以管理裝置和資料。

在本教學課程中，您將...
> [!div class="checklist"]
> * 設定 Azure Digital Twins 執行個體
> * 了解範例建築案例以及具現化預先撰寫的元件
> * 使用 [Azure Functions](../azure-functions/functions-overview.md) 應用程式，將模擬的遙測從 [IoT 中樞](../iot-hub/about-iot-hub.md)裝置路由至數位分身屬性
> * 藉由使用 Azure Functions、端點和路由處理數位分身通知，透過 **對應項圖形** 傳播變更

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>設定 Cloud Shell 工作階段
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>開始使用建築案例

本教學課程中使用的範例專案代表真實世界的 **建築案例**，包含樓層、房間和溫控裝置。 這些元件會以數位方式呈現在 Azure Digital Twins 執行個體中，然後連線到 [IoT 中樞](../iot-hub/about-iot-hub.md)、[事件方格](../event-grid/overview.md)和兩個 [Azure 函式](../azure-functions/functions-overview.md)，以加速資料移動。

以下是表示完整案例的圖表。 

您會先建立 Azure Digital Twins 執行個體 (圖中的 **A 區**)，然後在數位分身中設定遙測資料流程 (**B 箭號**)，再透過對應項圖形 (**C 箭號**) 設定資料傳播。

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="完整建築案例的圖形。描繪資料從裝置流入 IoT 中樞的過程：透過 Azure 函式 (B 箭號) 到 Azure Digital Twins 執行個體 (A 區)，再透過事件方格到另一個 Azure 函式 (C 箭號) 處理":::

為一步步完成此案例，您要與之前下載之預先撰寫範例應用程式的元件互動。

以下是建築案例 *AdtSampleApp* 範例應用程式實作的元件：
* 裝置驗證 
* [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 使用範例 (請參閱 *CommandLoop.cs*)
* 呼叫 Azure Digital Twins API 的主控台介面
* *SampleClientApp*：範例 Azure Digital Twins 解決方案
* *SampleFunctionsApp*：Azure Functions 應用程式，可將 Azure Digital Twins 圖形更新為來自 IoT 中樞和 Azure Digital Twins 事件的遙測結果

### <a name="instantiate-the-pre-created-twin-graph"></a>具現化預先建立的對應項圖形

首先，您要使用範例專案的 *AdtSampleApp* 解決方案，建置端對端案例的 Azure Digital Twins 片段 (**A 區**)：

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="完整建築案例圖形的摘錄，並醒目提示 A 區，亦即 Azure Digital Twins 執行個體":::

在開啟 _**AdtE2ESample**_ 專案的 Visual Studio 視窗中，使用工具列中的這個按鈕執行專案：

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Visual Studio 的啟動按鈕 (SampleClientApp 專案)":::

主控台視窗隨即開啟、執行驗證，然後等待命令。 在此主控台中，執行下一個命令以具現化範例 Azure Digital Twins 解決方案。

> [!IMPORTANT]
> 如果您的 Azure Digital Twins 執行個體中已經有數位分身和關聯性，執行此命令將會刪除這些項目，並替換為範例案例的對應項和關聯性。

```cmd/sh
SetupBuildingScenario
```

此命令的輸出是一系列的確認訊息，因為三個 [**數位分身**](concepts-twins-graph.md)是在您的 Azure Digital Twins 執行個體中建立和連線：名為 *floor1* 的樓層、名為 *room21* 的房間，以及名為 *thermostat67* 的溫度感應器。 這些數位分身代表真實環境中可能存在的實體。

其會透過關聯性連線到下列 [**對應項圖形**](concepts-twins-graph.md)。 對應項圖形代表整個環境，包括實體彼此之間的互動方式及關聯性。

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="顯示包含 room21 的 floor1，且 room21 包含 thermostat67 的圖形" border="false":::

您可以執行下列命令以驗證所建立的對應項，這會查詢已連線的 Azure Digital Twins 執行個體，找出其包含的所有數位分身：

```cmd/sh
Query
```

>[!TIP]
> 這個簡化的方法是 _**AdtE2ESample**_ 專案的一部分。 在此範例程式碼的內容以外，您可以隨時使用[查詢 API](/rest/api/digital-twins/dataplane/query) 或 [CLI 命令](how-to-use-cli.md)來查詢執行個體中的所有對應項。
>
> 以下是用來取得執行個體中所有數位對應項的完整查詢主體：
> 
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

在此之後，您就可以停止執行專案。 但請在 Visual Studio 中保持開啟此解決方案，因為您在本教學課程中還要繼續使用。

## <a name="set-up-the-sample-function-app"></a>設定範例函數應用程式

下一步是設定將在本教學課程中用來處理資料的 [Azure Functions 應用程式](../azure-functions/functions-overview.md)。 函數應用程式 *SampleFunctionsApp* 包含兩個函式：
* *ProcessHubToDTEvents*：處理傳入的 IoT 中樞資料，並據以更新 Azure Digital Twins
* *ProcessDTRoutedData*：處理來自數位分身的資料，並據以更新 Azure Digital Twins 中的父系對應項

在本節中，您要發佈預先撰寫的函數應用程式，並確保此函數應用程式可以藉由指派 Azure Active Directory (Azure AD) 身分識別來存取 Azure Digital Twins。 完成這些步驟，即可讓教學課程的其餘部分使用函數應用程式內的函式。 

回到 Visual Studio 視窗，其中 _**AdtE2ESample**_ 專案已開啟，且函式應用程式位於 _**SampleFunctionsApp**_ 專案檔中。 您可以在 [方案總管] 窗格中進行檢視。

### <a name="update-dependencies"></a>更新相依性

在發佈應用程式之前，最好先確定您有最新的相依性，並確定您已包含的所有套件都是最新版本。

在 [方案總管] 窗格中，展開 [SampleFunctionsApp] > [相依性]。 以滑鼠右鍵選取 [套件]，然後選擇 [管理 NuGet 套件...]。

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Visual Studio：管理 SampleFunctionsApp 專案的 NuGet 套件" border="false":::

這會開啟 NuGet 套件管理員。 選取 [更新] 索引標籤，如果有任何套件要更新，請核取方塊以 [選取所有套件]。 然後點擊 [更新]。

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Visual Studio：選取要更新 NuGet 套件管理員中的所有套件":::

### <a name="publish-the-app"></a>發佈應用程式

回到開啟 _**AdtE2ESample**_ 專案的 Visual Studio 視窗中，從 [方案總管] 窗格中，按一下滑鼠右鍵選取 _**SampleFunctionsApp**_ 專案檔案，然後按 [發佈]。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio：發佈專案":::

在接下來的 [發佈] 頁面中，保留選取預設目標 **Azure**，然後按 [下一步]。 

針對特定的目標，選擇 [Azure 函數應用程式 (Windows)]，然後按 [下一步]。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="在 Visual Studio 中發佈 Azure 函式：特定的目標":::

在 [Functions 執行個體] 頁面中，選擇您的訂用帳戶。 這應該會用您訂用帳戶的「資源群組」填入方塊。

選取您執行個體的資源群組，然後按 *+* 以建立新的 Azure 函式。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="在 Visual Studio 中發佈 Azure 函式：Functions 執行個體 (在函數應用程式之前)":::

在 [函數應用程式 (Windows) - 新建] 視窗中，填寫下列欄位：
* [名稱] 是 Azure 將用來裝載 Azure Functions 應用程式的使用情況方案名稱。 這也會成為裝載您實際函式的函數應用程式名稱。 您可以選擇您自己的唯一值，或保留預設建議。
* [訂用帳戶] 請務必與您要使用的訂用帳戶一致 
* [資源群組] 一定要是您希望使用的資源群組
* 將 [方案類型] 保留為 [使用]
* 在 [位置] 中，選擇符合您資源群組的位置
* 使用 [新增...] 連結，建立新的 **Azure 儲存體** 資源。 設定符合您資源群組的 [位置]，使用其他預設值，然後按 [確定]。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="在 Visual Studio 中發佈 Azure 函式：函數應用程式 (Windows) - 新建":::

然後，選取 [Create] \(建立\)。

這應該會帶您回到 [Functions 執行個體] 頁面，此時您的新函數應用程式會出現在您的資源群組底下。 按 [完成]。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="在 Visual Studio 中發佈 Azure 函式：Functions 執行個體 (在函數應用程式之後)":::

回到在主 Visual Studio 視窗中開啟的「發佈」 窗格中，確認所有資訊都正確無誤，然後選取 [發佈]。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="在 Visual Studio 中發佈 Azure 函式：發佈":::

> [!NOTE]
> 如果您看到如下的快顯：:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="在 Visual Studio 中發佈 Azure 函式：發佈認證" border="false":::
> 請選取 [嘗試從 Azure 擷取認證] 和 [儲存]。
>
> 如果您看到關於 *升級 Azure 上的 Azure Functions 版本* 或 *您的函式執行階段版本不符合 Azure 中執行的版本* 的警告：
>
> 請遵循提示來升級至最新的 Azure Functions 執行階段版本。 如果您使用的 Visual Studio 版本比本教學課程開頭的 *必要條件* 一節中所建議的版本還舊，就可能會發生此問題。

### <a name="assign-permissions-to-the-function-app"></a>將權限指派給函數應用程式

若要讓函數應用程式能夠存取 Azure Digital Twins，下一個步驟是設定應用程式設定，為應用程式指派系統管理的 Azure AD 身分識別，並授與此身分識別 Azure Digital Twins 執行個體中的「Azure Digital Twins 資料擁有者」 角色。 想要在執行個體上執行許多資料平面活動的使用者或函式都必須有此角色。 如需安全性和角色指派的詳細資訊，請參閱 [*概念：Azure Digital Twins 解決方案的安全性*](concepts-security.md)。

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

在 Azure Cloud Shell 中，使用下列命令，設定函式應用程式將用以參考 Azure Digital Twins 執行個體的應用程式設定。 在預留位置中填入資源的詳細資料 (請記住，您的 Azure 數位 Twins 實例 URL 是其主機名稱，之前是 *HTTPs://*) 。

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-Azure-Digital-Twins-instance-URL>"
```

輸出是 Azure 函式的設定清單，現在應該會包含稱為 *ADT_SERVICE_URL* 的專案。

使用下列命令建立系統管理的身分識別。 記下輸出中的 *principalId* 欄位。

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

在下列命令中，使用輸出中的 *principalId* 值將函式應用程式的身分識別指派給 Azure Digital Twins 執行個體的「Azure Digital Twins 資料擁有者」角色：

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```

此命令的結果是您所建立角色指派的輸出資訊。 函數應用程式現在有權存取您的 Azure Digital Twins 執行個體。

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>處理來自 IoT 中樞裝置的模擬遙測

Azure Digital Twins 圖形應由實際裝置的遙測所驅動。 

在這個步驟中，您要將在 [IoT 中樞](../iot-hub/about-iot-hub.md)中註冊的模擬溫控裝置，連線到在 Azure Digital Twins 中的數位分身。 當模擬裝置發出遙測時，此資料就會透過觸發數位分身對應更新的 *ProcessHubToDTEvents* Azure 函式導向。 如此一來，數位分身的資料就會和實際裝置保持一致。 在 Azure Digital Twins 中，將事件資料從某個位置導向至另一個位置的過程，稱為 [**路由事件**](concepts-route-events.md)。

這發生在此端對端案例的這個部分 (**B 箭號**)：

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="完整建築案例圖形的摘錄，並醒目提示 B 箭號，亦即 Azure Digital Twins 執行個體：裝置、IoT 中樞和第一個 Azure 函式":::

請完成下列動作以設定此裝置連線：
1. 建立將要管理模擬裝置的 IoT 中樞
2. 設定事件訂用帳戶，將 IoT 中樞連線至適當的 Azure 函式
3. 在 IoT 中樞中註冊模擬裝置
4. 執行模擬裝置並產生遙測
5. 查詢 Azure Digital Twins 以查看即時結果

### <a name="create-an-iot-hub-instance"></a>建立 IoT 中樞執行個體

Azure Digital Twins 的設計指在搭配 [IoT 中樞](../iot-hub/about-iot-hub.md)使用，後者是用於管理裝置及其資料的 Azure 服務。 在這個步驟中，您要設定在本教學課程中管理範例裝置的 IoT 中樞。

在 Azure Cloud Shell 中，使用此命令建立新的 IoT 中樞：

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

此命令的輸出是有關已建立的 IoT 中樞資訊。

儲存您提供給 IoT 中樞的名稱。 稍後您將會用到此資訊。

### <a name="connect-the-iot-hub-to-the-azure-function"></a>將 IoT 中樞連線至 Azure 函式

接下來，將您的 IoT 中樞連線至您稍早在函數應用程式中發佈的 *ProcessHubToDTEvents* Azure 函式，讓資料可以透過函式從裝置流入 IoT 中樞，這會更新 Azure Digital Twins。

若要這麼做，您要在 IoT 中樞上建立 **事件訂用帳戶**，並以 Azure 函式為端點。 這會「訂閱」在 IoT 中樞中發生的事件函式。

在 [Azure 入口網站](https://portal.azure.com/)中，導覽至您新建立的 IoT 中樞，方法是在頂端的搜尋列中搜尋其名稱。 從 [中樞] 功能表中選取 [事件]，然後選取 [+ 事件訂用帳戶]。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure 入口網站：IoT 中樞事件訂用帳戶":::

這會帶出「建立事件訂用帳戶」 頁面。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure 入口網站：建立事件訂用帳戶":::

填寫下列欄位 (不會提及依預設填入的欄位)：
* 「事件訂用帳戶詳細資料」 > [名稱]：命名事件訂用帳戶。
* *主題詳細資料* > **系統主題名稱**：提供要用於系統主題的名稱。 
* 「事件類型」 > [篩選至事件類型]：從功能表選項中選取「裝置遙測」。
* 「端點詳細資料」 > [端點類型]：從功能表選項中選取「Azure 函式」。
* 「端點詳細資料」 > [端點]：按「選取端點」 連結。 這會開啟「選取 Azure 函式」 視窗：:::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure 入口網站事件訂用帳戶：選取 Azure 函式" border="false":::
    - 填入您的 [訂用帳戶]、[資源群組]、[函數應用程式] 和 [函式] (*ProcessHubToDTEvents*)。 在您選取訂用帳戶後，其中有些可能會自動填入。
    - 按 [確認選取項目]。

回到「建立事件訂用帳戶」 頁面，按 [建立]。

### <a name="register-the-simulated-device-with-iot-hub"></a>向 IoT 中樞註冊模擬裝置 

本節會在識別碼為 *thermostat67* 的 IoT 中樞中建立裝置表示。 模擬裝置會連線到此，這也是遙測事件從裝置前往 IoT 中樞的方式，上個步驟中訂閱的 Azure 函式正在接聽、準備挑選事件並繼續處理。

在 Azure Cloud Shell 中，使用下列命令在 IoT 中樞中建立裝置：

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

輸出是有關已建立的裝置資訊。

### <a name="configure-and-run-the-simulation"></a>設定並執行模擬

接下來，設定裝置模擬器以將資料傳送至您的 IoT 中樞執行個體。

就從使用此命令取得「IoT 中樞連接字串」開始：

```azurecli-interactive
az iot hub connection-string show -n <your-IoT-hub-name>
```

然後，使用此命令取得「裝置連接字串」：

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

您要將這些值插入本機專案的裝置模擬器程式碼中，將模擬器連線到此 IoT 中樞和 IoT 中樞裝置。

在新的 Visual Studio 視窗中，(從下載的解決方案資料夾) 開啟裝置模擬器 > _**DeviceSimulator.sln**_。

>[!NOTE]
> 您現在應該有兩個 Visual Studio 視窗，一個是 _**DeviceSimulator.sln**_，另一個則是稍早的 _**AdtE2ESample.sln**_。

從這個新 Visual Studio 視窗的方案總管窗格中，選取 _DeviceSimulator/**AzureIoTHub.cs**_，使用編輯視窗開啟。 將下列連接字串值變更為您在前面收集到的值：

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

儲存檔案。

現在，若要查看已設定的資料模擬結果，請使用工具列中的這個按鈕執行 **DeviceSimulator** 專案：

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Visual Studio 的啟動按鈕 (DeviceSimulator 專案)":::

主控台視窗會開啟並顯示模擬的溫度遙測訊息。 這些訊息會傳送至 IoT 中樞，由 Azure 函式挑取並處理。

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="顯示正在傳送溫度遙測的裝置模擬器主控台輸出":::

您在此主控台中不必執行任何其他動作，但在完成後續步驟時，請保持其執行狀態。

### <a name="see-the-results-in-azure-digital-twins"></a>查看 Azure Digital Twins 中的結果

您稍早發佈的 *ProcessHubToDTEvents* 函式會接聽 IoT 中樞資料，呼叫 Azure Digital Twins API 更新 *thermostat67* 對應項的「溫度」 屬性。

若要查看 Azure Digital Twins 端的資料，請移至開啟 _**AdtE2ESample**_ 專案的 Visual Studio 視窗，然後執行專案。

在開啟的專案主控台視窗中，執行下列命令以取得數位分身 *thermostat67* 回報的溫度：

```cmd
ObserveProperties thermostat67 Temperature
```

您應該會看到「來自 Azure Digital Twins 執行個體」的即時更新溫度，每隔 10 秒就會記錄到主控台中。

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="顯示數位分身 thermostat67 溫度訊息記錄的主控台輸出":::

一旦驗證這項作業成功，您就可以停止執行這兩個專案。 讓這些 Visual Studio 視窗保持開啟，因為您在本教學課程的其餘部分還要繼續使用。

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>透過圖形傳播 Azure Digital Twins 事件

截至目前為止，您已在本教學課程中了解如何利用外部裝置資料更新 Azure Digital Twins。 接下來，您將會看到如何透過 Azure Digital Twins 圖形傳播以變更一個數位分身，換句話說，如何利用服務 - 內部資料更新對應項。

若要這樣做，在更新連線的 *Thermostat* 對應項時，您要使用 *ProcessDTRoutedData* Azure 函式來更新 *Room* 對應項。 這發生在此端對端案例的這個部分 (**C 箭號**)：

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="完整建築案例圖形的摘錄，並醒目提示 C 箭號，亦即 Azure Digital Twins 執行個體：事件方格和第二個 Azure 函式":::

請完成下列動作以設定此資料流程：
1. 在 Azure Digital Twins 中建立將執行個體連線至事件方格的端點
2. 設定 Azure Digital Twins 內的路由，將對應項屬性變更事件傳送至端點
3. 部署會在端點上接聽 (透過[事件方格](../event-grid/overview.md)) 的 Azure Functions 應用程式，並據此更新其他對應項
4. 執行模擬的裝置並查詢 Azure Digital Twins，以查看即時結果

### <a name="set-up-endpoint"></a>設定端點

[事件方格](../event-grid/overview.md)是一項 Azure 服務，可協助您將來自 Azure 服務的事件路由及傳遞至 Azure 內的其他位置。 您可以建立[事件方格主題](../event-grid/concepts.md)，從來源收集特定事件，然後訂閱者可以接聽該主題，以在事件送達時接收事件。

在本節中，您會建立事件方格主題，然後在 Azure Digital Twins 內建立端點，以指向 (傳送事件) 至該主題。 

在 Azure Cloud Shell 中執行下列命令，以建立事件方格主題：

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> 若要輸出可傳遞至 Azure CLI 命令中的 Azure 區域名稱清單，請執行此命令：
> ```azurecli-interactive
> az account list-locations -o table
> ```

此命令的輸出是您所建立事件方格主題的相關資訊。

接著，在 Azure Digital Twins 中建立事件方格端點，將您的執行個體連線至事件方格主題。 使用下列命令，視需要填寫預留位置欄位：

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

此命令的輸出是您所建立端點的相關資訊。

您也可以執行下列命令，在 Azure Digital Twins 執行個體中查詢此端點，以驗證是否成功建立端點：

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

查閱輸出的 `provisioningState` 欄位，檢查其值是否為 "Succeeded"。 其也可能指出「佈建中」，這表示端點仍在建立中。 在此情況下，請等候幾秒鐘，然後再次執行命令，以檢查其是否已順利完成。

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="端點查詢的結果，顯示 provisioningState 為 Succeeded 的端點":::

儲存您為事件方格主題和 Azure Digital Twins 中的事件方格端點提供的名稱。 稍後會用到這些。

### <a name="set-up-route"></a>設定路由

接下來，建立 Azure Digital Twins 路由，將事件傳送至您剛才建立的事件方格端點。

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

此命令的輸出是您所建立路由的部分資訊。

>[!NOTE]
>端點 (來自上一個步驟) 必須先完成佈建，您才能設定使用這些端點的事件路由。 如果因為端點尚未就緒而導致路由建立失敗，請等候幾分鐘，然後再試一次。

#### <a name="connect-the-function-to-event-grid"></a>將函式連線至事件方格

接下來，請訂閱您稍早建立之事件方格主題的 *ProcessDTRoutedData* Azure 函式，讓遙測資料可以透過事件方格主題，從 *thermostat67* 對應項流入函式，回到 Azure Digital Twins，據此更新 *room21* 對應項。

若要這麼做，您要在事件方格主題中，建立以 *ProcessDTRoutedData* Azure 函式為端點的 **事件方格訂用帳戶**。

在 [Azure 入口網站](https://portal.azure.com/)中，導覽至您的事件方格主題，方法是在頂端的搜尋列中搜尋其名稱。 選取 [+ 事件訂用帳戶]。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure 入口網站：事件方格事件訂用帳戶":::

建立此事件訂用帳戶的步驟，類似本教學課程稍早訂閱第一個 IoT 中樞 Azure 函式的步驟。 這次不需要指定「裝置遙測」作為接聽的事件類型，而且您會連線至不同的 Azure 函式。

在 [建立事件訂用帳戶] 頁面中，填寫下列欄位 (不會提及依預設填入的欄位)：
* 「事件訂用帳戶詳細資料」 > [名稱]：命名事件訂用帳戶。
* 「端點詳細資料」 > [端點類型]：從功能表選項中選取「Azure 函式」。
* 「端點詳細資料」 > [端點]：按「選取端點」 連結。 這會開啟「選取 Azure 函式」 視窗：
    - 填入您的 [訂用帳戶]、[資源群組]、[函數應用程式] 和 [函式] (*ProcessDTRoutedData*)。 在您選取訂用帳戶後，其中有些可能會自動填入。
    - 按 [確認選取項目]。

回到「建立事件訂用帳戶」 頁面，按 [建立]。

### <a name="run-the-simulation-and-see-the-results"></a>執行模擬並查看結果

現在您可以執行裝置模擬器，啟動您已設定的新事件流程。 移至開啟 _**DeviceSimulator**_ 專案的 Visual Studio 視窗，然後執行專案。

就像您稍早執行裝置模擬器一樣，主控台視窗會開啟並顯示模擬的溫度遙測訊息。 這些事件會通過您稍早設定的流程來更新 *thermostat67* 對應項，再通過您最近設定的流程來更新要比對的 *room21* 對應項。

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="顯示正在傳送溫度遙測的裝置模擬器主控台輸出":::

您在此主控台中不必執行任何其他動作，但在完成後續步驟時，請保持其執行狀態。

若要查看 Azure Digital Twins 端的資料，請移至開啟 _**AdtE2ESample**_ 專案的 Visual Studio 視窗，然後執行專案。

在開啟的專案主控台視窗中執行下列命令，以取得 *thermostat67* 和 *room21* 這 **兩個** 數位分身回報的溫度。

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

您應該會看到「來自 Azure Digital Twins 執行個體」的即時更新溫度，每隔 10 秒就會記錄到主控台中。 請注意，*room21* 的溫度正在更新，以符合 *thermostat67* 的更新。

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="顯示溫控器和房間溫度訊息記錄的主控台輸出":::

一旦驗證這項作業成功，您就可以停止執行這兩個專案。 您也可以關閉這些 Visual Studio 視窗，因為本教學課程已完成。

## <a name="review"></a>檢閱

以下是您在本教學課程中建立的案例回顧。

1. Azure Digital Twins 執行個體會以數位方式顯示樓層、房間和溫控器 (如下圖的 **A 區** 所示)
2. 模擬裝置遙測會傳送至 IoT 中樞，其中 *ProcessHubToDTEvents* Azure 函式正在接聽遙測事件。 *ProcessHubToDTEvents* Azure 函式會使用這些事件中的資訊，設定 *thermostat67* (圖中的 **B 箭號**) 的「溫度」屬性。
3. Azure Digital Twins 的屬性變更事件會路由至事件方格主題，其中 *ProcessDTRoutedData* Azure 函式正在接聽事件。 *ProcessHubToDTEvents* Azure 函式會使用這些事件中的資訊，設定 *room21* (圖中的 **C 箭號**) 的「溫度」屬性。

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="完整建築案例的圖形。描繪資料從裝置流入 IoT 中樞的過程：透過 Azure 函式 (B 箭號) 到 Azure Digital Twins 執行個體 (A 區)，再透過事件方格到另一個 Azure 函式 (C 箭號) 處理":::

## <a name="clean-up-resources"></a>清除資源

當您不再需要於本教學課程中建立的資源時，請遵循這些步驟加以刪除。 

使用 [Azure Cloud Shell](https://shell.azure.com)，您可以使用 [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) 命令刪除資源群組中的所有 Azure 資源。 這會移除資源群組、Azure Digital Twins 執行個體、IoT 中樞和中樞裝置註冊、事件方格主題和相關聯的訂用帳戶，以及 Azure Functions 應用程式，包括兩個函式和儲存體等相關聯的資源。

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作。 資源群組和其中包含的所有資源都將永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 

```azurecli-interactive
az group delete --name <your-resource-group>
```

最後，刪除下載到您本機電腦的專案範例資料夾。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您建立的端對端案例，顯示由即時裝置資料驅動的 Azure Digital Twins。

接下來，開始查看概念文件，以深入了解您在本教學課程中使用過的元素：

> [!div class="nextstepaction"]
> [概念：自訂模型](concepts-models.md)