---
title: 教學課程：設定 Gen2 環境 - Azure 時間序列深入解析 Gen2 | Microsoft Docs
description: 教學課程：了解如何在 Azure 時間序列深入解析 Gen2 中設定環境。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 79a4fc048b8301d67206bf28b571f88f9e5ad024
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597672"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>教學課程：設定 Azure 時間序列深入解析 Gen2 環境

此教學課程會逐步引導您完成建立 Azure 時間序列深入解析 Gen2「隨用隨付」(PAYG) 環境的程序。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 建立 Azure 時間序列深入解析 Gen2 環境。
> * 將 Azure 時間序列深入解析 Gen2 環境連線至 IoT 中樞。
> * 執行解決方案加速器範例，以將資料串流處理至 Azure 時間序列深入解析 Gen2 環境。
> * 在資料上執行基本的分析。
> * 定義「時間序列模型」類型和階層，並將其與您的執行個體建立關聯。

>[!TIP]
> [IoT 解決方案加速器](https://www.azureiotsolutions.com/Accelerators)提供預先設定的企業級解決方案，可讓您用來加快自訂 IoT 解決方案的開發速度。

如果您沒有帳戶，請註冊[免費 Azure 訂用帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

* 您至少必須擁有 Azure 訂用帳戶的**參與者**角色。 如需詳細資訊，請閱讀[使用角色型存取控制和 Azure 入口網站管理存取權](../role-based-access-control/role-assignments-portal.md)。

## <a name="create-a-device-simulation"></a>建立裝置模擬

在本節中，您會建立三個將資料傳送至 Azure IoT 中樞執行個體的模擬裝置。

1. 移至 [Azure IoT 解決方案加速器頁面](https://www.azureiotsolutions.com/Accelerators)。 此頁面會顯示數個預先建置的範例。 請使用您的 Azure 帳戶來登入。 然後，選取 [裝置模擬]。

   [![Azure IoT 解決方案加速器頁面。](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. 輸入您要在其中建立裝置模擬的訂用帳戶。

   參數|描述
   ---|---
   **部署名稱** | 此唯一值會用來建立新的資源群組。 列出的 Azure 資源會在建立後指派給資源群組。
   **Azure 訂用帳戶** | 指定與上一節中用來建立 Azure 時間序列深入解析 Gen2 環境的相同訂用帳戶。
   **部署選項** | 選取 [佈建新的 IoT 中樞] 以建立本教學課程特定的新 IoT 中樞。
   **Azure 位置** | 指定與上一節中用來建立 Azure 時間序列深入解析 Gen2 環境的相同區域。

   完成後，請選取 [建立] 以佈建解決方案的 Azure 資源。 完成此程序約需要 20 分鐘的時間。

   [![佈建裝置模擬解決方案。](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. 佈建完成後會顯示兩項通知，宣告部署狀態已從 [佈建中] 變為 [就緒]。

   >[!IMPORTANT]
   > 請還不要輸入解決方案加速器！ 請將此網頁保持為開啟，因為您稍後將會回到這裡。

   [![裝置模擬解決方案佈建完成。](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. 現在，您可以在 Azure 入口網站中檢查新建立的資源。 在 [資源群組] 頁面中，您會看到已建立的新資源群組，其使用上一個步驟中提供的**解決方案名稱**。 記下針對裝置模擬所建立的資源。

   [![裝置模擬資源。](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>建立 Azure 時間序列深入解析 Gen2 環境

此節描述如何使用 [Azure 入口網站](https://portal.azure.com/)建立 Azure 時間序列深入解析 Gen2 環境，並將其連線至 IoT 解決方案加速器所建立的 IoT 中樞。

1. 使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左上方的 [+ 建立資源]。
1. 選取 [物聯網] 類別，然後選取 [時間序列深入解析]。

   [![選取時間序列深入解析環境資源。](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. 在 [建立時間序列深入解析環境] 窗格的 [基本資料] 索引標籤上，設定下列參數：

    | 參數 | 動作 |
    | --- | ---|
    | **環境名稱** | 為 Azure 時間序列深入解析 Gen2 環境輸入唯一名稱。 |
    | **訂用帳戶** | 輸入您要在其中建立 Azure 時間序列深入解析 Gen2 環境的訂用帳戶。 您使用的訂用帳戶最好和裝置模擬器所建立的其餘 IoT 資源相同。 |
    | **資源群組** | 為 Azure 時間序列深入解析 Gen2 環境資源選取現有的資源群組，或建立新的資源群組。 資源群組是 Azure 資源的容器。 您使用的資源群組最好與裝置模擬器所建立的其餘 IoT 資源相同。 |
    | **位置** | 為 Azure 時間序列深入解析 Gen2 環境選取資料中心區域。 為了避免增加額外的延遲，建議您將 Azure 時間序列深入解析 Gen2 環境建立於裝置模擬器所建立的 IoT 中樞所在區域。 |
    | **層級** |  選取 [Gen2(L1)]。 這是適用於 Azure 時間序列深入解析 Gen2 產品的 SKU。 |
    | **時間序列識別碼屬性名稱** | 輸入屬性的名稱，此屬性包含可唯一識別您時間序列執行個體的值。 您在 [屬性名稱] 方塊中輸入的值 (例如時間序列識別碼)，稍後均無法變更。 針對本教學課程，請輸入 ***iothub-connection-device-id***。若要深入了解時間序列識別碼 (包括複合時間序列識別碼)，請閱讀[選擇時間序列識別碼的最佳做法](./time-series-insights-update-how-to-id.md)。 |
    | **儲存體帳戶名稱** | 針對新儲存體帳戶輸入全域唯一的名稱。|
    | **儲存體帳戶種類** | 選取新儲存體帳戶的儲存體種類。 我們建議選取 StorageV2|
    | **儲存體帳戶複寫** | 選取新儲存體帳戶的儲存體種類。 根據您的位置選取項目，您可以從 LRS、GRS 和 ZRS 中進行選擇。 在此教學課程中，您可以選取 [LRS]。|
    | **階層式命名空間** |當您選取 StorageV2 作為儲存體種類之後，即可選取此選項。 預設會停用此選項。 在此教學課程中，您可以將其保留為預設「停用」狀態。|
    |**啟用溫存放區**|選取 [是] 以啟用溫存放區。 您也可以在建立環境之後，停用並重新啟用此設定。 |
    |**資料保留 (天數)**|選擇預設的 7 天選項。 |

    [![新的時間序列深入解析環境設定。](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

1. 完成時，選取 [下一步:事件來源]。

   [![設定環境的時間序列識別碼。](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. 在 [事件來源] 索引標籤上，設定下列參數：

   | 參數 | 動作 |
   | --- | --- |
   | **建立事件來源?** | 選取 [是]。|
   | **名稱** | 為事件來源名稱輸入唯一值。 |
   | **來源類型** | 選取 [IoT 中樞]。 |
   | **選取中樞** | 選擇 [選取現有的]。 |
   | **訂用帳戶** | 選取用於裝置模擬器的訂用帳戶。 |
   | **IoT 中樞名稱** | 選取您為裝置模擬器建立的 IoT 中樞名稱。 |
   | **IoT 中樞存取原則** | 選取 [iothubowner]。 |
   | **IoT 中樞取用者群組** | 選取 [新增]，輸入唯一名稱，然後再選取 [+ 新增]。 在 Azure 時間序列深入解析 Gen2 中，取用者群組必須是唯一值。 |
   | **時間戳記屬性** | 此值可用來識別傳入遙測資料中的**時間戳記**屬性。 在此教學課程中，將此方塊保留空白。 此模擬器會使用 Azure 時間序列深入解析 Gen2 預設的 IoT 中樞所傳入的時間戳記。 |

1. 選取 [檢閱 + 建立]。

   [![將建立的 IoT 中樞設定為事件來源。](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. 選取 [建立]。

    [![[檢閱 + 建立] 頁面，其中含有 [建立] 按鈕。](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    您可以檢視部署的狀態：

    [![部署已完成的通知。](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. 如果您是 Azure 訂用帳戶的擁有者，預設即可存取您的 Azure 時間序列深入解析 Gen2 環境。 確認您具有存取權：

   1. 搜尋資源群組，然後選取您新建的 Azure 時間序列深入解析 Gen2 環境。

      [![選取並檢視您的環境。](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. 在 Azure 時間序列深入解析 Gen2 頁面上，選取 [資料存取原則]：

      [![驗證資料存取原則。](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. 確認您的認證是否有列出：

      如果未列出您的認證，您就必須藉由選取 [新增] 並搜尋認證，來對自己授與環境的存取權限。 若要深入了解如何設定權限，請參閱[授與資料存取權](./time-series-insights-data-access.md)。

## <a name="stream-data"></a>串流資料

現在，您已部署 Azure 時間序列深入解析 Gen2 環境，接下來可以開始串流處理資料以進行分析。

1. 返回您的[解決方案加速器儀表板](https://www.azureiotsolutions.com/Accelerators#dashboard)。 如有必要，請使用您在本教學課程中使用的相同 Azure 帳戶重新登入。 選取您的「裝置解決方案」，然後**移至您的解決方案加速器**，以啟動您已部署的解決方案。

   [![解決方案加速器儀表板。](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. 裝置模擬 Web 應用程式會從提示您把「將您登入並讀取您的設定檔」的權限授與 Web 應用程式開始。 此權限可讓應用程式擷取支援應用程式運作所需的使用者設定檔資訊。

   [![裝置模擬 Web 應用程式同意。](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. 選取 [+ 新增模擬]。

    1. 在 [模擬設定] 頁面載入後，請輸入必要的參數。

        | 參數 | 動作 |
        | --- | --- |
        | **名稱** | 為模擬器輸入唯一的名稱。 |
        | **說明** | 輸入定義。 |
        | **模擬持續時間** | 設定為 [Run indefinitely] \(無限期執行\)。 |
        | **裝置型號** | 按一下 [+ 新增裝置類型] <br />**Name**：輸入「電梯」。 <br />**數量**：輸入 **3**。 <br /> 保留其餘預設值 |
        | **目標 IoT 中樞** | 設定為 [Use pre-provisioned IoT Hub] \(使用預先佈建的 IoT 中樞\)。 |

        [![設定參數和啟動。](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    1. 選取 [開始模擬]。 在裝置模擬儀表板中，會顯示 [使用中的裝置] 和 [訊息總計]。

        [![Azure IoT 模擬儀表板。](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>分析資料

在此節中，您會使用 [Azure 時間序列深入解析 Gen2 總管](./time-series-insights-update-explorer.md)，對時間序列資料執行基本分析。

1. 從 [Azure 入口網站](https://portal.azure.com/)的資源頁面選取 URL，以移至 Azure 時間序列深入解析 Gen2 總管。

    [![Azure 時間序列深入解析 Gen2 總管 URL。](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. 在 Azure 時間序列深入解析 Gen2 總管中，將會顯示橫跨畫面頂端的長條。 這是您的可用性選擇器。 請務必至少選取 2m，並在需要時，藉由將選取的選擇器控點向左和向右拖曳來擴展時間範圍。

1. 左側會顯示**時間序列執行個體**。

    [![無上層執行個體的清單。](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. 選取第一個時間序列執行個體。 然後選取 [顯示溫度]。

    [![已選取的時間序列執行個體，其中含有顯示平均溫度的功能表命令。](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    時間序列圖隨即出現。 將 [間隔] 變更為 **30 秒**。

1. 針對其他兩個時間序列執行個體重複上述步驟，以便您同時查看三個執行個體，如下圖所示：

    [![所有時間序列的圖表。](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. 選取右上方的時間範圍選擇器。 您可以在這裡選取特定的開始和結束時間 (最小單位為毫秒)，或選擇預先設定的選項，例如 [過去 30 分鐘]。 您也可以變更預設時區。

    [![將時間範圍設定為過去30分鐘。](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    解決方案加速器在**過去 30 分鐘**內的進度，現在會顯示於 Azure 時間序列深入解析 Gen2 總管中。

## <a name="define-and-apply-a-model"></a>定義並套用模型

在本節中，您會套用模型來建構資料。 為了完成此模型，您會定義類型、階層和執行個體。 若要深入了解資料模型化，請閱讀[時間序列模型](./concepts-model-overview.md)。

1. 在總管中，選取 [模型] 索引標籤：

   [![檢視總管中的 [模型] 索引標籤。](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   在 [類型] 索引標籤中選取 [+ 新增]。

1. 輸入下列參數：

    | 參數 | 動作 |
    | --- | ---|
    | **名稱** | 輸入「電梯」 |
    | **說明** | 輸入「這是電梯的類型定義」 |

1. 接下來，選取 [變數] 索引標籤。

    1. 選取 [+ 新增變數]，並針對電梯類型的第一個變數填入下列值。 您總共會撰寫三個變數。

        | 參數 | 動作 |
        | --- | --- |
        | **名稱** | 輸入**平均溫度**。 |
        | **種類** | 選取 [數值] |
        | **ReplTest1** | 從預設中選取：選取 [溫度 (雙精準數)]。 <br /> 注意:在 Azure 時間序列深入解析 Gen2 開始接收事件之後，可能需要幾分鐘的時間，才會自動填入**值**。|
        | **彙總作業** | 展開 [進階選項]。 <br /> 選取 [AVG]。 |

    1. 選取 [套用]。 接著，再次選取 [+ 新增變數]，然後設定下列值：

        | 參數 | 動作 |
        | --- | --- |
        | **名稱** | 輸入「平均震動」。 |
        | **種類** | 選取 [數值] |
        | **ReplTest1** | 從預設中選取：選取 [震動 (雙精確度)]。 <br /> 注意:在 Azure 時間序列深入解析 Gen2 開始接收事件之後，可能需要幾分鐘的時間，才會自動填入**值**。|
        | **彙總作業** | 展開 [進階選項]。 <br /> 選取 [AVG]。 |

    1. 選取 [套用]。 接著，再次選取 [+ 新增變數]，然後為第三個和最後一個變數設定下列值：

        | 參數 | 動作 |
        | --- | --- |
        | **名稱** | 輸入「樓層」。 |
        | **種類** | 選取 [類別] |
        | **ReplTest1** | 從預設中選取：選取 [樓層 (雙精確度)]。 <br /> 注意:在 Azure 時間序列深入解析 Gen2 開始接收事件之後，可能需要幾分鐘的時間，才會自動填入**值**。|
        | **類別** | <span style="text-decoration: underline">標籤</span>  - <span style="text-decoration: underline">值</span> <br /> 低：1、2、3、4 <br /> 中：5、6、7、8、9 <br /> 高：10、11、12、13、14、15 |
        | **預設類別** | 輸入「未知」 |

        [![新增類型變數。](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    1. 選取 [套用]。 
    1. 選取 [儲存]。 系統會建立並顯示三個變數。

        [![新增類別之後，請在 [模型] 檢視中加以檢視。](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. 選取 [階層] 索引標籤。然後選取 [+ 新增]。

   1. 在 [編輯階層] 窗格中，設定下列參數：

        | 參數 | 動作 |
        | --- | ---|
        | **名稱** | 輸入**位置階層**。 |
        |**層級**| 輸入「國家/地區」作為第一個層級的名稱 <br /> 選取 [+ 新增層級] <br /> 輸入「城市」作為第二個層級，然後選取 [+ 新增層級] <br /> 輸入「建築物」作為第三個和最後一個層級的名稱 |

   1. 選取 [儲存]。

        [![在模型檢視中顯示新階層。](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. 流覽至 [執行個體]。

    1. 在最右邊的 [動作] 下方選取鉛筆圖示，以下列值編輯第一個執行個體：

        | 參數 | 動作 |
        | --- | --- |
        | **型別** | 選取 [電梯]。 |
        | **名稱** | 輸入「電梯 1」|
        | **說明** | 輸入「電梯 1 的執行個體」。 |

    1. 瀏覽至 [執行個體欄位] 並輸入下列值：

        | 參數 | 動作 |
        | --- | --- |
        | **階層** | 選取 [位置階層] |
        | **國家/地區** | 輸入**美國** |
        | **城市** | 輸入**西雅圖** |
        | **建築物** | 輸入**太空針塔** |

    1. 選取 [儲存]。

1. 使用下列值時，以其他兩個執行個體重複上一個步驟：

    **電梯 2：** ：

    | 參數 | 動作 |
    | --- | --- |
    | **型別** | 選取 [電梯]。 |
    | **名稱** | 輸入「電梯 2」|
    | **說明** | 輸入「電梯 2 的執行個體」 |
    | **階層** | 選取 [位置階層] |
    | **國家/地區** | 輸入**美國** |
    | **城市** | 輸入**西雅圖** |
    | **建築物** | 輸入**太平洋科學中心** |

    **電梯 3：**

    | 參數 | 動作 |
    | --- | --- |
    | **型別** | 選取 [電梯]。 |
    | **名稱** | 輸入「電梯 3」|
    | **說明** | 輸入「電梯 3 的執行個體」 |
    | **階層** | 選取 [位置階層] |
    | **國家/地區** | 輸入**美國** |
    | **城市** | 輸入**紐約** |
    | **建築物** | 輸入**帝國大廈** |

    [![檢視更新後的執行個體。](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. 往回瀏覽至 [分析] 索引標籤，以查看圖表窗格。 在 [位置階層] 底下，展開所有階層層級來顯示時間序列執行個體：

    [![在圖表檢視中檢視所有階層。](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. 在 [太平洋科學中心] 底下選取時間序列執行個體「電梯 2」，然後選取 [顯示平均溫度]。

1. 針對相同的執行個體「電梯 2」，選取 [顯示樓層]。

    透過您的類別變數，您就可以判斷電梯在高樓層、低樓層和中間樓層所花費的時間。

    [![使用階層和資料將電梯 2 視覺化。](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>清除資源

您現在已完成本教學課程，請清除您所建立的資源：

1. 從 [Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [所有資源]，找出 Azure 時間序列深入解析 Gen2 資源群組。
1. 選取 [刪除] 以刪除整個資源群組 (和其中包含的所有資源)，或個別移除每個資源。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：  

* 建立並使用裝置模擬加速器。
* 建立 Azure 時間序列深入解析 Gen2 PAYG 環境。
* 將 Azure 時間序列深入解析 Gen2 環境連線至 IoT 中樞。
* 執行解決方案加速器範例，以將資料串流處理至 Azure 時間序列深入解析 Gen2 環境。
* 執行資料的基本分析。
* 定義「時間序列模型」類型和階層，並將其與您的執行個體建立關聯。

現在您已經知道如何建立自己的 Azure 時間序列深入解析 Gen2 環境，接下來可深入了解 Azure 時間序列深入解析 Gen2 中的重要概念。

閱讀 Azure 時間序列深入解析 Gen2 擷取：

> [!div class="nextstepaction"]
> [Azure 時間序列深入解析 Gen2 資料擷取概觀](./concepts-ingestion-overview.md)

閱讀 Azure 時間序列深入解析 Gen2 儲存體：

> [!div class="nextstepaction"]
> [Azure 時間序列深入解析 Gen2 資料儲存體](./concepts-storage.md)

深入了解時間序列模型：

> [!div class="nextstepaction"]
> [Azure 時間序列深入解析 Gen2 資料模型](./concepts-model-overview.md)

深入了解如何將環境連線至 Power BI：

> [!div class="nextstepaction"]
> [在 Power BI 中從 Azure 時間序列深入解析 Gen2 將資料視覺化](./how-to-connect-power-bi.md)
