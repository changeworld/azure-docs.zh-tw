---
title: 使用時間序列深入解析將模擬的遙測資料視覺化-Azure |Microsoft Docs
description: 了解如何設定時間序列深入解析環境，來探索和分析裝置模擬解決方案加速器產生的的遙測資料。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 2bbd7911a40d6a256d478e2533ad2469b8fd6973
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73889336"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>使用時間序列深入分析將從裝置模擬解決方案加速器傳送的遙測資料視覺化

裝置模擬解決方案加速器可讓您從模擬裝置產生遙測資料，藉此測試您的 IoT 解決方案。 本操作指南會示範如何使用時間序列深入解析環境，來視覺化和分析模擬的遙測資料。

## <a name="prerequisites"></a>Prerequisites

若要遵循此操作指南中的步驟，您必須具備有效的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

此操作指南中的步驟假設您已將裝置模擬解決方案加速器部署至您的 Azure 訂用帳戶。 如果您尚未部署此解決方案加速器，請先遵循[部署與執行雲端式裝置模擬解決方案](quickstart-device-simulation-deploy.md)快速入門中的步驟。

本文假設您解決方案加速器的名稱是 **contoso-simulation**。 完成下列步驟時，將 **contoso-simulation** 取代成為您解決方案加速器的名稱。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>建立取用者群組

您必須在 IoT 中樞內建立專屬的取用者群組，以便將遙測資料串流至時間序列深入解析。 時間序列深入解析中的事件來源應該獨佔使用 IoT 中樞取用者群組。

下列步驟會在 Azure Cloud Shell 中使用 Azure CLI 來建立取用者群組：

1. IoT 中樞是您在部署裝置模擬解決方案加速器時所建立的其中一個資源。 請執行下列命令來尋找 IoT 中樞的名稱 - 請記得使用解決方案加速器的名稱：

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT 中樞是 **Microsoft.Devices/IotHubs** 類型的資源。

1. 在中樞內新增名為 **devicesimulationtsi** 的取用者群組。 在下列命令中，使用中樞和解決方案加速器的名稱：

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    您現在可以關閉 Azure Cloud Shell。

## <a name="create-a-new-time-series-insights-environment"></a>建立新的時間序列深入解析環境

[Azure 時間序列深入解析](../../articles/time-series-insights/time-series-insights-overview.md)是完全受控的分析、儲存體及視覺效果服務，可在雲端中管理 IoT 規模的時間序列資料。 若要建立新的時間序列深入解析環境：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取 [**建立資源** > ]**物聯網** > **時間序列深入解析**：

    ![新增時間序列深入解析](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. 若要在和解決方案加速器相同的資源群組中建立時間序列深入解析環境，請使用下表中的值：

    | 設定 | 值 |
    | ------- | ----- |
    | 環境名稱 | 下列螢幕擷取畫面會使用名稱 **Contoso-TSI**。 當您完成此步驟時，請選擇您自己的唯一名稱。 |
    | 訂用帳戶 | 在下拉式清單中選取您的 Azure 訂用帳戶。 |
    | 資源群組 | **contoso-simulation**。 使用解決方案加速器的名稱。 |
    | Location | 此範例使用**美國東部**。 在和裝置模擬加速器相同的區域中建立環境。 |
    | SKU |**S1** |
    | Capacity | **1** |

    ![建立時間序列深入解析](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > 將時間序列深入解析環境新增至和解決方案加速器相同的資源群組中，表示當您刪除解決方案加速器時，該環境也會刪除。

1. 按一下頁面底部的 [新增]  。 可能需要幾分鐘的時間來建立環境。

## <a name="create-event-source"></a>建立事件來源

建立新的事件來源以連線到 IoT 中樞。 使用您在先前步驟中所建立的取用者群組。 時間序列深入解析事件來源需要其他服務未使用的專屬取用者群組。

1. 在 Azure 入口網站中，瀏覽至新的時間序列環境。

1. 按一下左側的 [事件來源]****：

    ![檢視事件來源](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. 按一下 [**新增**]：

    ![新增事件來源](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. 若要設定您的 IoT 中樞作為新的事件來源，請使用下表中的值：

    | 設定 | 值 |
    | ------- | ----- |
    | 事件來源名稱 | 下列螢幕擷取畫面會使用名稱 **contoso-iot-hub**。 當您完成此步驟時，請使用您自己的唯一名稱。 |
    | 來源 | **IoT 中樞** |
    | 匯入選項 | **從可用的訂用帳戶使用 IoT 中樞** |
    | 訂用帳戶識別碼 | 在下拉式清單中選取您的 Azure 訂用帳戶。 |
    | IoT 中樞名稱 | **contoso-simulation7d894**。 使用來自裝置模擬解決方案加速器的 IoT 中樞名稱。 |
    | IoT 中樞原則名稱 | **iothubowner** |
    | IoT 中樞原則金鑰 | 系統會自動填入此欄位。 |
    | IoT 中樞取用者群組 | **devicesimulationtsi** |
    | 事件序列化格式 | **JSON** |
    | 時間戳記屬性名稱 | 保留空白 |

    ![建立事件來源](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. 按一下頁面底部的 [新增]  。

> [!NOTE]
> 您可以[對其他使用者授與存取權](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access)，使其能夠存取時間序列深入解析總管。

## <a name="start-a-simulation"></a>啟動模擬

使用「時間序列深入解析」總管之前，請先設定裝置模擬解決方案加速器來產生一些遙測資料。 下列螢幕擷取畫面顯示 10 個執行中的冷卻裝置模擬：

![正在執行裝置模擬](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>時間序列深入解析總管

「時間序列深入解析」總管是一個 Web 應用程式，可供您用來視覺化遙測資料。

1. 在 Azure 入口網站中，選取「時間序列深入解析」的 [概觀]**** 索引標籤。

1. 若要開啟「時間序列深入解析」總管 Web 應用程式，請按一下 [移至環境]****：

    ![時間序列深入解析總管](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. 在時間選取範圍面板中，從快速時間功能表中選取 [過去 30 分鐘]****，然後按一下 [搜尋]****：

    ![時間序列深入解析總管搜尋](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. 在左側的字詞面板中，選取 [溫度]**** 作為 [量值]****，選取 [iothub-connection-device-id]**** 作為 [分割依據]**** 值：

    ![時間序列深入解析總管查詢](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. 以滑鼠右鍵按一下圖表，然後選取 [**探索事件**]：

    ![時間序列深入解析總管事件](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. 事件資料會顯示在方格中：

    ![時間序列深入解析總管表](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. 按一下檢視方塊檢視按鈕：

    ![時間序列深入解析總管檢視方塊](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. 按一下**+** 以將新的查詢加入至觀點：

    ![時間序列深入解析總管的新增查詢](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. 選取 [過去 30 分鐘]**** 作為時間範圍，選取 [濕度]**** 作為 [量值]****，選取 [iothub-connection-device-id]**** 作為 [分割依據]**** 值：

    ![時間序列深入解析總管查詢](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. 按一下檢視方塊檢視按鈕，以檢視裝置遙測儀表板：

    ![時間序列深入解析總管的儀表板](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>清除資源

如果您打算進一步探索，請讓解決方案加速器維持部署。

如果您不再需要解決方案加速器，請在 [已布[建的解決方案](https://www.azureiotsolutions.com/Accelerators#dashboard)] 頁面中選取它，然後按一下 [**刪除解決方案**]，將它刪除。

如果您已將時間序列深入解析環境新增至解決方案加速器的資源群組中，則當您刪除解決方案加速器時，該環境也會自動刪除。 否則，您必須從 Azure 入口網站手動移除時間序列深入解析環境。

## <a name="next-steps"></a>後續步驟

若要深入了解如何在時間序列深入解析中探索和查詢資料，請參閱 [Azure 時間序列深入解析總管](../time-series-insights/time-series-insights-explorer.md)。
