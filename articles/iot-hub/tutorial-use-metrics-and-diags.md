---
title: 搭配 Azure IoT 中樞來設定及使用計量和記錄
description: 了解如何搭配 Azure IoT 中樞來設定及使用計量和記錄。 這會提供要分析的資料，以協助診斷您中樞可能發生的問題。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: a16dbeedc2362f4a263d59a673dbb4358f7ba034
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436482"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>教學課程：搭配 IoT 中樞來設定及使用計量和記錄

您可以使用 Azure 監視器來收集 IoT 中樞的計量和記錄，協助您監視解決方案的作業，並在發生問題時進行疑難排解。 在本文中，您將了解如何根據計量建立圖表、如何建立會在計量上觸發的警示、如何將 IoT 中樞作業和錯誤傳送至 Azure 監視器記錄，以及如何檢查記錄是否有錯誤。

本教學課程使用從 [.NET 傳送遙測快速入門](quickstart-send-telemetry-dotnet.md)取得的 Azure 範例，以將訊息傳送至 IoT 中樞。 您一律可以使用裝置或其他範例來傳送訊息，但可能必須據以修改幾個步驟。

開始進行本教學課程之前，對 Azure 監視器概念有一定的熟悉程度會很有幫助。 若要深入了解，請參閱[監視 IoT 中樞](monitor-iot-hub.md)。 若要深入了解 IoT 中樞所發出的計量和資源記錄，請參閱[監視資料參考](monitor-iot-hub-reference.md)。

在本教學課程中，您會執行下列工作：

> [!div class="checklist"]
>
> * 使用 Azure CLI 建立 IoT 中樞、註冊模擬裝置，並建立 Log Analytics 工作區。  
> * 將 IoT 中樞連線和裝置遙測資源記錄傳送到 Log Analytics 工作區中的 Azure 監視器記錄。
> * 使用計量瀏覽器建立以選取的計量為基礎的圖表，並將其釘選到您的儀表板。
> * 建立計量警示，以便您可以在發生重要狀況時收到電子郵件通知。
> * 下載並執行某個應用程式，以模擬 IoT 裝置傳送訊息給 IoT 中樞。
> * 當您的情況發生時，請檢視警示。
> * 在儀表板上檢視計量圖表。
> * 檢視 Azure 監視器記錄中的 IoT 中樞錯誤和作業。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 您的開發電腦上必須有 .NET Core SDK 2.1 或以上版本。 您可以從 [.NET](https://www.microsoft.com/net/download/all) 下載適用於多種平台的 .NET Core SDK。

  您可以使用下列命令，以確認開發電腦上目前的 C# 版本：

  ```cmd/sh
  dotnet --version
  ```

- 能夠接收郵件的電子郵件帳戶。

- 請確定您的防火牆已開啟連接埠 8883。 本教學課程中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>設定資源

在此教學課程中，您需要 IoT 中樞、Log Analytics 工作區和模擬的 IoT 裝置。 這些資源可使用 Azure CLI 或 Azure PowerShell 來建立。 請將所有資源建立在相同的資源群組和位置。 如此一來，當您完成教學課程時，可以透過刪除資源群組的單一步驟來移除所有項目。

必要步驟如下。

1. 建立[資源群組](../azure-resource-manager/management/overview.md)。

2. 建立 IoT 中樞。

3. 建立 Log Analytics 工作區。

4. 為傳送訊息至 IoT 中樞的模擬裝置註冊裝置身分識別。 儲存裝置連接字串，以便用來設定模擬裝置。

### <a name="set-up-resources-using-azure-cli"></a>使用 Azure CLI 設定資源

複製此指令碼並貼到 Cloud Shell。 如果您已登入，它會一次執行一行指令碼。 有些命令的執行可能需要一些時間。 新的資源會建立到資源群組「ContosoResources」中。

某些資源的名稱在整個 Azure 中必須是唯一的。 指令碼會使用 `$RANDOM` 函式產生隨機值，並將其儲存在變數中。 針對這些資源，指令碼會將此隨機值附加至資源的基底名稱，使資源名稱成為唯一的。

每個訂用帳戶只能有一個免費的 IoT 中樞。 如果您的訂用帳戶中已有免費的 IoT 中樞，請在執行指令碼之前先將其刪除，或修改指令碼以使用免費的 IoT 中樞，或使用標準或基本層的 IoT 中樞。

此指令碼會列印 IoT 中樞的名稱、Log Analytics 工作區的名稱，以及所註冊的裝置連接字串。 請務必記下這些項目，因為您在本文稍後需要用到。

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>在建立裝置身分識別時，您可能會收到下列錯誤：無法針對 IoT 中樞 ContosoTestHub 的原則 iothubowner 找到其金鑰  。 若要修正這個錯誤，請更新 Azure CLI IoT 擴充功能，然後再次執行指令碼中的最後兩個命令。 
>
>以下是用來更新擴充功能的命令。 請在 Cloud Shell 執行個體中執行此命令。
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>收集連線和裝置遙測的記錄

IoT 中樞會發出數個作業類別的資源記錄；不過，若要檢視這些記錄，您必須建立診斷設定，將其傳送至目的地。 其中一個此類目的地是在 Log Analytics 工作區中收集的 Azure 監視器記錄。 IoT 中樞資源記錄會分組成不同的類別。 您可以在診斷設定中選取要傳送至 Azure 監視器記錄的類別。 在本文中，我們將會收集與連線和裝置遙測有關的作業和錯誤記錄。 如需 IoT 中樞所支援類別的完整清單，請參閱 [IoT 中樞資源記錄](monitor-iot-hub-reference.md#resource-logs)。

若要建立診斷設定以將 IoT 中樞資源記錄傳送至 Azure 監視器記錄，請遵循下列步驟：

1. 首先，如果您還未進入您在入口網站中的中樞，請選取 [資源群組]，然後選取資源群組 ContosoResources。 從所顯示的資源清單中選取 IoT 中樞。

1. 在 IoT 中樞刀鋒視窗中找到 [監視]  區段。 選取 [診斷設定]。 然後選取 [新增診斷設定]。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="螢幕擷取畫面：醒目提示 [監視] 區段中的 [診斷設定]。":::

1. 在 [診斷設定] 窗格中，為您的設定提供描述性的名稱，例如「傳送連線和遙測至記錄」。

1. 在 [類別詳細資料] 中，選取 [連線]，然後選取 [裝置遙測]。

1. 在 [目的地詳細資料] 中，選取 [傳送至 Log Analytics]，然後使用 Log Analytics 工作區選擇器來選取您先前記下的工作區。 當您完成時，診斷設定看起來應該如以下螢幕擷取畫面所示：

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="螢幕擷取畫面，顯示最後的診斷記錄設定。":::

1. 選取 [儲存]  以儲存設定。 關閉 [診斷設定] 窗格。 您可以在診斷設定清單中看到新的設定。

## <a name="set-up-metrics"></a>設定計量

現在，我們將會使用計量瀏覽器來建立圖表，以顯示您想要追蹤的計量。您會將此圖表釘選到 Azure 入口網站中的預設儀表板。

1. 在 IoT 中樞左窗格中的 [監視] 區段選取 [計量]。

1. 在畫面頂端，選取 [過去 24 小時 (自動)]。 在出現的下拉式清單中，選取 [過去 4 小時] 作為 [時間範圍]，將 [時間細微性] 設定為 [1 分鐘]，並且選取 [當地] 作為 [顯示時間]。 選取 [套用] 來儲存這些設定。 設定現在應該會顯示 [當地時間：過去 4 小時 (1 分鐘)]。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="螢幕擷取畫面，顯示計量時間設定。":::

1. 在圖表上，有一個部分計量設定會顯示在 IoT 中樞的範圍內。 將 [範圍] 和 [計量命名空間] 值保留為其預設值。 選取 [計量] 設定並輸入「遙測」，然後從下拉式清單中選取 [遙測訊息傳送]。 [彙總] 會自動設定為 [總和]。 請注意，您的圖表標題也會變更。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="顯示將已傳送的遙測訊息計量新增至圖表的螢幕擷取畫面。":::

1. 現在，選取 [新增計量] 以在圖表中新增其他計量。 在 [計量]  底下，選取 [已使用的訊息總數]  。 [彙總] 會自動設定為 [平均]。同樣地請注意，圖表的標題已變更為包含此計量。

   現在，您的畫面中會顯示已最小化的 [已傳送的遙測訊息]  計量，以及用於 [已使用的訊息總數]  的新計量。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="顯示將已使用的訊息總數計量新增至圖表的螢幕擷取畫面。":::

1. 在圖表的右上方，選取 [釘選到儀表板]。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="醒目提示 [釘選到儀表板] 按鈕的螢幕擷取畫面。":::

1. 在 [釘選到儀表板] 窗格上，選取 [現有] 索引標籤。選取 [私人]，然後從 [儀表板] 下拉式清單中選取 [儀表板]。 最後，選取 [釘選] 以將此圖表釘選到 Azure 入口網站中的預設儀表板。 如果您未將圖表釘選到儀表板，當您結束計量瀏覽器時，不會保留您的設定。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="顯示釘選到儀表板設定的螢幕擷取畫面。":::

## <a name="set-up-metric-alerts"></a>設定計量警示

現在，我們將會設定警示，在「已傳送的遙測訊息」和「已使用的訊息總數」這兩個計量上觸發。

「已傳送的遙測訊息」是一個良好的計量，可供監視以追蹤訊息輸送量並避免進行節流。 針對免費層中的 IoT 中樞，節流限制為 100 個訊息/秒。使用單一裝置時，我們將無法達到此類輸送量，因此，我們會設定警示，以在 5 分鐘的期間內超過 1000 個訊息數目時觸發。 在生產環境中，您可以根據您 IoT 中樞的服務層級、版本和單位數，將訊號設定為更高的值。

「已使用的訊息總數」會追蹤每日使用的訊息數目。 此計量會在每天的 00:00 UTC 重設。 如果您超過每日配額一段特定閾值的時間，您的 IoT 中樞就不會再接受訊息。 針對免費層中的 IoT 中樞，每日訊息配額為 8000。 我們會設定警示，以在訊息總數超過 4000、50% 的配額時觸發。 在實務上，您可能會將此百分比設定為較高的值。 每日配額值取決於您的 IoT 中樞的服務層級、版本和單位數。

如需有關 IoT 中樞配額和節流限制的詳細資訊，請參閱[配額和節流](iot-hub-devguide-quotas-throttling.md)。

若要設定計量警示：

1. 請移至 Azure 入口網站中的 IoT 中樞。

1. 在 [監視] 下方，選取 [警示]。 然後選取 [新增警示規則]。  [建立警示規則] 窗格隨即開啟。

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="顯示 [建立警示規則] 窗格的螢幕擷取畫面。":::

    在 [建立警示規則] 窗格中，有四個區段：

    * **範圍** 已設定為您的 IoT 中樞，因此我們不會變動此區段。
    * **條件** 設定將會觸發警示的訊號和條件。
    * **動作** 設定當警示觸發時會發生什麼情形。
    * **警示規則詳細資料** 可讓您設定警示的名稱和描述。

1. 首先，設定會觸發警示的條件。

    1. 在 [條件] 底下，選取 [選取條件]。 在 [設定訊號邏輯] 窗格上，於搜尋方塊中輸入「遙測」，然後選取 [已傳送的遙測訊息]。

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="顯示選取計量的螢幕擷取畫面。":::

    1. 在 [設定訊號邏輯] 窗格上，設定或確認 [警示邏輯] 底下的下列欄位 (您可以忽略圖表)：

       **閾值**：靜態。

       **操作員**：*大於*。

       **彙總類型**：*總計*。

       **閾值**：1000。

       **彙總細微性 (期間)** ：*5 分鐘*。

       **評估頻率**：*每隔 1 分鐘*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="顯示警示條件設定的螢幕擷取畫面。":::

       這些設定會將訊號設定為在 5 分鐘期間內的訊息總數。 這個總計將會每分鐘評估一次，如果前 5 分鐘的總計超過 1000 個訊息，就會觸發警示。

       選取 [完成] 以儲存訊號邏輯。

1. 現在設定警示的動作。

    1. 回到 [建立警示規則] 窗格，在 [動作] 底下，選取 [選取動作群組]。 在 [選取要附加至此警示規則的動作群組] 窗格中，選取 [建立動作群組]。

    1. 在 [建立動作群組] 窗格的 [基本] 索引標籤底下，為您的動作群組提供名稱和顯示名稱。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="顯示 [建立動作群組] 窗格 [基本] 索引標籤的螢幕擷取畫面。":::

    1. 選取 [通知] 索引標籤。針對 [通知類型]，從下拉式清單中選取 [電子郵件/SMS 訊息/推播/語音]。 [電子郵件/SMS 訊息/推播/語音] 窗格隨即開啟。

    1. 在 [電子郵件/SMS 訊息/推播/語音] 窗格上，選取 [電子郵件] 並輸入您的電子郵件地址，然後選取 [確定]。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="顯示電子郵件地址設定的螢幕擷取畫面。":::

    1. 回到 [通知] 窗格，輸入通知的名稱。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="顯示已完成通知窗格的螢幕擷取畫面。":::

    1. (選擇性) 如果您選取 [動作] 索引標籤，然後選取 [動作類型] 下拉式清單，您可以看到可透過警示觸發的動作種類。 在本文中，我們只會使用通知，因此您可以忽略此索引標籤下的設定。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="顯示 [動作] 窗格上可用動作類型的螢幕擷取畫面。":::

    1. 選取 [檢閱與建立] 索引標籤，確認您的設定，然後選取 [建立]。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="顯示 [檢閱與建立] 窗格的螢幕擷取畫面。":::

    1. 回到 [建立警示規則] 窗格，請注意新的動作群組已新增至警示的動作。  

1. 最後，設定警示規則詳細資料並儲存警示規則。

    1. 在 [建立警示規則] 窗格的 [警示規則詳細資料] 底下，輸入警示的名稱和描述；例如，「如果 5 分鐘超過 1000 個訊息則發出警示」。 請確定已核取 [建立時啟用警示規則]。 已完成的警示規則看起來會類似此螢幕擷取畫面。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="顯示已完成 [建立警示規則] 窗格的螢幕擷取畫面。":::

    1. 選取 [建立警示規則] 以儲存您的新規則。

1. 現在，為 [已使用的訊息總數]  設定另一個警示。 如果您想要在所使用的訊息數目接近 IoT 中樞的每日配額時傳送警示，屆時 IoT 中樞就會開始拒絕訊息，此計量會很有用。 遵循您先前執行的步驟，但是有下列差異。

    * 針對 [設定訊號邏輯] 窗格上的訊號，選取 [已使用的訊息總數]。

    * 在 [設定訊號邏輯] 窗格上，設定或確認下列欄位 (您可以忽略圖表)：

       **閾值**：靜態。

       **操作員**：*大於*。

       **彙總類型**：最大值。

       **閾值**：4000。

       **彙總細微性 (期間)** ：1 分鐘。

       **評估頻率**：*每隔 1 分鐘*

       這些設定會設定當訊息數目到達 4000 個時要觸發的訊號。 計量會每分鐘評估一次。

    * 當您指定警示規則的動作時，請直接選取您先前建立的動作群組。

    * 針對警示詳細資料，請選擇與您先前所提供名稱和描述不同的名稱和描述。

1. 在 IoT 中樞左窗格的 [監視] 底下，選取 [警示]。 現在，在 [警示] 窗格頂端的功能表上，選取 [管理警示規則]。 [規則] 窗格隨即開啟。 您現在應該會看到兩個警示：

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="顯示 [規則] 窗格 (具有新的警示規則) 的螢幕擷取畫面。":::

1. 關閉 [規則] 窗格。

使用這些設定時，將會觸發警示，而在 5 分鐘時間範圍內傳送超過 1000 個訊息時，以及使用的訊息總數超過 4000 個 (免費層中 IoT 中樞每日配額的 50%) 時，您會收到電子郵件通知。

## <a name="run-the-simulated-device-app"></a>執行模擬裝置應用程式

在 [設定資源](#set-up-resources) 區段中，您已註冊裝置身分識別，以用來模擬使用 IoT 裝置。 在本節中，您會下載模擬裝置的 .NET 主控台應用程式，來將裝置到雲端的訊息傳送至 IoT 中樞，設定為將這些訊息傳送至您的 IoT 中樞，然後加以執行。 

> [!IMPORTANT]
>
> 最多可能需要 10 分鐘的時間，IoT 中樞才能完全設定及啟用警示。 在您設定最後一次警示和執行模擬裝置應用程式之間，請至少等候 10 分鐘。

下載 [IoT 裝置模擬](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)的解決方案。 此連結會下載包含數個應用程式的存放庫；您需要的項目位於 iot-hub/Quickstarts/simulated-device/。

1. 在本機終端機視窗中，瀏覽至解決方案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\simulated-device** 資料夾。

1. 在您選擇的文字編輯器中開啟 **SimulatedDevice.cs** 檔案。

    1. 將 `s_connectionString` 變數的值取代為您在執行指令碼以設定資源時所記下的裝置連接字串。

    1. 在 `SendDeviceToCloudMessagesAsync` 方法中，將 `Task.Delay` 從 1000 變更為 1，以將傳送訊息的間隔時間從 1 秒減為 0.001 秒。 縮短此延遲會增加所傳送的訊息數目。 (您可能不會收到每秒 100 個訊息速率的訊息)。

        ```csharp
        await Task.Delay(1);
        ```

    1. 將變更儲存到 **SimulatedDevice.cs**。

1. 在本機終端機視窗中，執行下列命令以安裝模擬裝置應用程式所需的套件：

    ```cmd/sh
    dotnet restore
    ```

1. 在本機終端機視窗中，執行下列命令以建置並執行模擬裝置應用程式：

    ```cmd/sh
    dotnet run
    ```

    下列螢幕擷取畫面顯示模擬裝置應用程式將遙測傳送到 IoT 中樞時的輸出：

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="顯示模擬裝置輸出的螢幕擷取畫面。":::

讓應用程式至少執行 10-15 分鐘。 在理想的情況下，讓應用程式執行直到停止傳送訊息 (大約 20-30 分鐘)。 當您已超過 IoT 中樞的每日訊息配額，而且其已停止接受其他訊息時，就會發生這種情況。

> [!NOTE]
> 如果您讓裝置應用程式在停止傳送訊息後繼續執行一段延長的時間，則可能會遇到例外狀況。 您可以放心地略過此例外狀況，並關閉應用程式視窗。

## <a name="view-metrics-chart-on-your-dashboard"></a>在儀表板上檢視計量圖表

1. 在 Azure 入口網站的左上角，開啟入口網站功能表，然後選取 [儀表板]。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="顯示如何選取儀表板的螢幕擷取畫面。":::

1. 尋找您稍早釘選的圖表，然後在圖表資料外部的圖格上按一下任何位置以將其展開。 會在圖表上顯示已傳送的遙測訊息和已使用的訊息總數。 最新的數量會顯示在圖表的底部。 您可以在圖表中移動資料指標，查看特定時間的計量值。 您也可以在圖表頂端變更時間值和細微性，將資料縮小或擴大到感興趣的時間週期。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="顯示計量圖表的螢幕擷取畫面。":::

   在此案例中，模擬裝置的訊息輸送量不夠大，而無法造成 IoT 中樞對其訊息進行節流。 在實際牽涉到節流的案例中，您可能看到已傳送的遙測訊息超過 IoT 中樞節流限制一段限制的時間。 這是為了容納高載流量。 如需詳細資訊，請參閱[流量成形](iot-hub-devguide-quotas-throttling.md#traffic-shaping)。

## <a name="view-the-alerts"></a>檢視警示

當已傳送的訊息數量超過您在警示規則中設定的限制時，您就會開始收到電子郵件警示。

若要查看是否有任何使用中警示，請在 IoT 中樞左窗格的 [監視] 底下，選取 [警示]。 [警示] 窗格會顯示針對指定時間範圍，依嚴重性排序的已引發警示數目。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="顯示警示摘要的螢幕擷取畫面。":::

選取嚴重性「Sev 3」的資料列。 [所有警示] 窗格隨即開啟，並列出已引發的「Sev 3」警示。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="顯示 [所有警示] 窗格的螢幕擷取畫面。":::

選取其中一個警示以查看警示詳細資料。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="顯示警示詳細資料的螢幕擷取畫面。":::

檢查您的收件匣中是否有來自 Microsoft Azure 的電子郵件。 [主旨] 行會描述已觸發的警示。 例如，*Azure：已啟用嚴重性：3 警示，如果在 5 分鐘超過 1000 個訊息*。 本文會如以下影像所示：

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="電子郵件的螢幕擷取畫面，顯示已引發警示。":::

## <a name="view-azure-monitor-logs"></a>檢視 Azure 監視器記錄

在 [收集連線和裝置遙測的記錄](#collect-logs-for-connections-and-device-telemetry) 區段中，您已建立診斷設定，以將 IoT 中樞針對連線和裝置遙測作業所發出的資源記錄傳送給 Azure 監視器記錄。 在本節中，您將會針對 Azure 監視器記錄執行 Kusto 查詢，以觀察發生的任何錯誤。

1. 在 Azure 入口網站中 IoT 中樞的左窗格，在 [監視] 底下選取 [記錄]。 關閉初始 [查詢] 視窗 (如果開啟)。

1. 在 [新增查詢] 窗格中，選取 [查詢] 索引標籤，然後展開 [IoT 中樞] 以查看預設查詢的清單。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="IoT 中樞預設查詢的螢幕擷取畫面。":::

1. 選取 [錯誤摘要] 查詢。 查詢會出現在 [查詢編輯器] 窗格中。 在編輯器窗格中選取 [執行]，並觀察查詢結果。 展開其中一個資料列以查看詳細資料。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="錯誤摘要查詢所傳回記錄的螢幕擷取畫面。":::

   > [!NOTE]
   > 如果您沒有看到任何錯誤，請嘗試執行「最近連線的裝置」查詢。 這應該會傳回模擬裝置的資料列。

## <a name="clean-up-resources"></a>清除資源

若要將您已針對本教學課程所建立的所有資源移除，請刪除資源群組。 此動作會同時刪除群組內含的所有資源。 在本例中，此動作會移除 IoT 中樞、Log Analytics 工作區和資源群組本身。 如果您已將計量圖表釘選到儀表板，則必須對每個計量圖表右上角的三個點按一下，然後選取 [移除]，來手動移除這些計量。 請務必在刪除圖表之後儲存您的變更。

若要移除資源群組，請使用 [az group delete](/cli/azure/group#az-group-delete) 命令。

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何藉由執行下列工作，來使用 IoT 中樞計量和記錄：

> [!div class="checklist"]
>
> * 使用 Azure CLI 建立 IoT 中樞、註冊模擬裝置，並建立 Log Analytics 工作區。  
> * 將 IoT 中樞連線和裝置遙測資源記錄傳送到 Log Analytics 工作區中的 Azure 監視器記錄。
> * 使用計量瀏覽器建立以選取的計量為基礎的圖表，並將其釘選到您的儀表板。
> * 建立計量警示，以便您可以在發生重要狀況時收到電子郵件通知。
> * 下載並執行某個應用程式，以模擬 IoT 裝置傳送訊息給 IoT 中樞。
> * 當您的情況發生時，請檢視警示。
> * 在儀表板上檢視計量圖表。
> * 檢視 Azure 監視器記錄中的 IoT 中樞錯誤和作業。

前進至下一個教學課程，以了解如何管理 IoT 裝置的狀態。 

> [!div class="nextstepaction"]
> [從後端服務設定您的裝置](tutorial-device-twins.md)