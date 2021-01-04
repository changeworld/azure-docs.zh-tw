---
title: 教學課程 - 在 Azure IoT Central 中自訂操作員儀表板
description: 此教學課程說明如何在 Azure IoT Central 應用程式中自訂操作員儀表板，以及如何管理裝置。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 5165f7d6e16e0d136dbf8d4507090c78372185cb
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97348045"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>教學課程：在 Azure IoT Central 中自訂操作員儀表板及管理裝置


在此教學課程中，您了解如何在 Azure IoT Central 店內分析應用程式中自訂操作員儀表板。 應用程式操作員可以使用自訂儀表板來執行應用程式及管理已連結的裝置。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 變更儀表板名稱
> * 在儀表板上自訂影像圖格
> * 排列圖格以修改版面配置
> * 新增遙測圖格以顯示狀況
> * 新增屬性圖格以顯示裝置詳細資料
> * 新增命令圖格以執行命令

## <a name="prerequisites"></a>Prerequisites

建置者應完成教學課程，以建立 Azure IoT Central 應用程式並新增裝置：

* [在 Azure IoT Central 中建立店內分析應用程式](./tutorial-in-store-analytics-create-app.md) (必要)

## <a name="change-the-dashboard-name"></a>變更儀表板名稱
若要自訂操作員儀表板，您必須編輯應用程式中的預設儀表板。 您也可以選擇建立額外的新儀表板。 在您應用程式中自訂儀表板的第一個步驟是變更名稱。

1. 瀏覽至 [Azure IoT Central 的應用程式管理員](https://aka.ms/iotcentral)網站。

1. 開啟您於[在 Azure IoT Central 中建立店內分析應用程式](./tutorial-in-store-analytics-create-app.md)教學課程中建立的狀況監視應用程式。

1. 在儀表板工具列上，選取 [編輯]  。 在編輯模式中，您可以自訂儀表板的外觀、版面配置與內容。

    ![Azure IoT Central 編輯儀表板](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-edit.png)

1. 您也可以選擇隱藏左窗格。 隱藏左窗格讓您有更大的工作區域來編輯儀表板。

1. 在 [儀表板名稱]  中，為您的儀表板輸入易記名稱。 此教學課程使用名為 Contoso 的虛構公司，而範例儀表板名稱為「Contoso 儀表板」  。 

1. 選取 [儲存]  。 您的變更便會儲存到儀表板，並停用編輯模式。

    ![Azure IoT Central 變更儀表板名稱](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>在儀表板上自訂影像圖格
Azure IoT Central 應用程式儀表板是由一或多個圖格組成的。 圖格是在儀表板上顯示內容的矩形容器。 您可以將各種類型的內容與圖格建立關聯，並拖曳、置放及調整圖格的大小，以自訂儀表板版面配置。 有數種類型的圖格可以顯示內容。 影像圖格包含影像，而且您可以新增 URL，讓使用者可以按一下影像。 標籤圖格可顯示純文字。 Markdown 圖格包含格式化內容，可讓您設定影像、URL、標題，以及轉譯為 HTML 的 Markdown 程式碼。 遙測、屬性或命令圖格可顯示裝置特定資料。 

在此節中，您將了解如何在儀表板上自訂影像圖格。

自訂在儀表板上顯示品牌影像的影像圖格：

1. 在儀表板工具列上，選取 [編輯]  。 

1. 在顯示 Northwind 品牌影像的影像圖格上，選取 [設定]  。 

    ![Azure IoT Central 編輯品牌影像](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-edit.png)

1. 變更 [標題]  。 當使用者將滑鼠停留在影像上時，標題會出現。

1. 選取 [影像]  。 對話方塊隨即開啟，讓您上傳自訂影像。 

1. 您也可以選擇指定影像的 URL。

1. 選取 [更新組態]  。 [更新組態]  按鈕會儲存對儀表板的變更，並維持啟用編輯模式。

    ![Azure IoT Central 儲存品牌影像](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-save.png)

1. 您也可以選擇在標題是 [文件]  的圖格上，選取 [設定]  ，並指定支援內容的 URL。 

自訂顯示商店內感應器區域地圖的影像圖格：

1. 在顯示預設商店區域地圖的影像圖格上，選取 [設定]  。 

1. 選取 [影像]  ，然後使用對話方塊來上傳商店區域地圖的自訂影像。 

1. 選取 [更新組態]  。

    ![Azure IoT Central 儲存商店地圖](./media/tutorial-in-store-analytics-customize-dashboard/store-map-save.png)

    範例 Contoso 商店地圖顯示四個區域：兩個結帳區、服飾與個人保健區，以及雜貨與熟食區。 在此教學課程中，您會將感應器與這些區域建立關聯，以提供遙測。

    ![Azure IoT Central 商店區域](./media/tutorial-in-store-analytics-customize-dashboard/store-zones.png)

1. 選取 [儲存]  。 

## <a name="arrange-tiles-to-modify-the-layout"></a>排列圖格以修改版面配置
自訂儀表板的關鍵步驟是重新排列圖格，以建立實用的檢視。 應用程式操作員會使用儀表板來視覺化裝置遙測、管理裝置，以及監視商店中的狀況。 Azure IoT Central 可簡化應用程式建置人員建立儀表板的工作。 儀表板編輯模式可讓您快速新增、移動、調整大小及刪除圖格。 **店內分析 - 結帳** 應用程式範本也簡化建立儀表板的工作。 它提供可運作的儀表板版面配置 (已連接感應器)，以及顯示結帳隊伍計數與環境狀況的圖格。

在此節中，您會重新排列 **店內分析 - 結帳** 應用程式範本中的儀表板，以建立自訂版面配置。

移除您不打算在應用程式中使用的圖格：

1. 在儀表板工具列上，選取 [編輯]  。 

1. 選取 [X 刪除]  以移除下列圖格：[返回所有區域]  、[瀏覽商店儀表板]  、[等候時間]  ，以及與 [結帳 3]  相關聯的所有三個圖格。 Contoso 商店儀表板不會使用這些圖格。 

    ![Azure IoT Central 刪除圖格](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles.png)

1. 捲動來顯示其餘儀表板圖格。

1. 選取 [X 刪除]  以移除下列圖格：[結帳區增溫]  、[結帳區降溫]  、[占用率感應器設定]  、[控溫器感應器設定]  ，以及 [環境狀況]  。 

   ![Azure IoT Central 刪除其餘圖格](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles-2.png)

1. 選取 [儲存]  。 移除未使用的圖格可釋放編輯頁面中的空間，並簡化操作員的儀表板檢視。

1. 檢視您對儀表板所做的變更。

   ![刪除圖格之後的 Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/after-delete-tiles.png)

移除未使用的圖格之後，請重新排列其餘圖格，以建立整齊的版面配置。 新版面配置包含您在後續步驟中加入圖格所需的空間。

重新排列其餘圖格：

1. 選取 [編輯]  。

1. 選取 [占用率韌體]  圖格，並將它拖曳到 [占用率]  電池圖格的右邊。

1. 選取 [控溫器韌體]  圖格，並將它拖曳到 [控溫器]  電池圖格的右邊。

1. 選取 [儲存]  。

1. 檢視您的版面配置變更。 

    ![Azure IoT Central 韌體電池圖格](./media/tutorial-in-store-analytics-customize-dashboard/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>新增遙測圖格以顯示狀況
自訂儀表板版面配置之後，您就可以新增圖格以顯示遙測。 若要建立遙測圖格，請選取裝置範本與裝置執行個體，然後選取要在圖格中顯示的裝置特定遙測。 **店內分析 - 結帳** 應用程式範本在儀表板中包含數個遙測圖格。 兩個結帳區中的四個圖格顯示來自模擬占用率感應器的遙測。 [人員流量]  圖格顯示兩個結帳區中的計數。 

在此節中，您會新增兩個遙測圖格，以顯示您於[在 Azure IoT Central 中建立店內分析應用程式](./tutorial-in-store-analytics-create-app.md)教學課程中所新增 RuuviTag 感應器的環境遙測。 

新增圖格以顯示 RuuviTag 感應器的環境資料：

1. 選取 [編輯]  。

1. 在 [裝置範本]  清單中，選取 `RuuviTag`。 

1. 選取兩個 RuuviTag 感應器其中一個的 [裝置例項]  。 在範例 Contoso 商店中，選取 `Zone 1 Ruuvi` 以建立區域 1 的遙測圖格。 

1. 在 [遙測]  清單中，選取 `Relative humidity` 與 `temperature`。 這些是圖格上針對每個區域顯示的遙測項目。

1. 選取 [合併]  。 

    ![Azure IoT Central 新增 RuuviTag 圖格 1](./media/tutorial-in-store-analytics-customize-dashboard/add-zone1-ruuvi.png)

    新圖格隨即出現，顯示所選感應器已合併的濕度與溫度遙測。 

1. 在 RuuviTag 感應器的新圖格上，選取 [設定]  。 

1. 將 [標題]  變更為 [區域 1 環境]  。 

1. 選取 [更新組態]  。

1. 重複上述步驟來建立第二個感應器執行個體的圖格。 將 [標題]  設定為 [區域 2 環境]  ，然後選取 [更新組態]  。

1. 將標題為 [區域 2 環境]  的圖格拖曳到 [控溫器韌體]  圖格底下。 

1. 將標題為 [區域 1 環境]  的圖格拖曳到 [人員流量]  圖格底下。 

1. 選取 [儲存]  。 儀表板會在兩個新的圖格中顯示區域遙測。

    ![Azure IoT Central 所有 RuuviTag 圖格](./media/tutorial-in-store-analytics-customize-dashboard/all-ruuvitag-tiles.png)

編輯 [人員流量]  圖格，只顯示兩個結帳區的遙測：

1. 選取 [編輯]  。 

1. 在 [人員流量]  圖格上，選取 [設定]  。

1. 在 [遙測]  中，選取 [計數 1]  、[計數 2]  與 [計數 3]  。 

1. 選取 [更新組態]  。 這會清除圖格上的現有設定。 

1. 在 [人員流量]  圖格上，再次選取 [設定]  。

1. 在 [遙測]  中，選取 [計數 1]  與 [計數 2]  。 

1. 選取 [更新組態]  。 

1. 選取 [儲存]  。  已更新的儀表板只顯示兩個結帳區的計數，這是以模擬的占用率感應器為基礎。

    ![Azure IoT Central 人員流量兩個線道](./media/tutorial-in-store-analytics-customize-dashboard/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>新增屬性圖格以顯示裝置詳細資料
應用程式操作員會使用儀表板來管理裝置及監視狀態。 為每個 RuuviTag 新增圖格，讓操作員能夠檢視軟體版本。 

新增每個 RuuviTag 的屬性圖格：

1. 選取 [編輯]  。

1. 在 [裝置範本]  清單中，選取 `RuuviTag`。 

1. 選取兩個 RuuviTag 感應器其中一個的 [裝置例項]  。 在範例 Contoso 商店中，選取 `Zone 1 Ruuvi` 以建立區域 1 的遙測圖格。 

1. 選取 [屬性] > [軟體版本]  。

1. 選取 [合併]  。 

1. 在新建立標題為 [軟體版本]  的圖格上，選取 [設定]  。 

1. 將 [標題]  變更為 [Ruuvi 1 軟體版本]  。

1. 選取 [更新組態]  。 

1. 將標題為 [Ruuv 1 軟體版本]  的圖格拖曳到 [區域 1 環境]  圖格底下。

1. 重複上述步驟來建立第二個 RuuviTag 的軟體版本屬性圖格。 

1. 選取 [儲存]  。  

    ![Azure IoT Central RuuviTag 屬性圖格](./media/tutorial-in-store-analytics-customize-dashboard/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>新增命令圖格以執行命令
應用程式操作員也會藉由執行命令來使用儀表板管理裝置。 您可以將命令圖格新增至儀表板，以在裝置上執行預先定義的命令。 在此節中，您會新增命令圖格，讓操作員可以將 Rigado 閘道重新開機。 

新增命令圖格以將閘道重新開機：

1. 選取 [編輯]  。 

1. 在 [裝置範本]  清單中，選取 `C500`。 這是 Rigado C500 閘道器的範本。 

1. 在 [裝置例項]  中，選取閘道執行個體。

1. 選取 [命令] > [重新開機]  ，並將它拖曳到儀表板內，放在商店地圖旁邊。 

1. 選取 [儲存]  。 

1. 檢視已完成的 Contoso 儀表板。 

    ![Azure IoT Central 完成儀表板自訂](./media/tutorial-in-store-analytics-customize-dashboard/completed-dashboard.png)

1. 您也可以選擇選取 [重新開機]  圖格，以在閘道上執行重新啟動命令。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

* 變更儀表板名稱
* 在儀表板上自訂影像圖格
* 排列圖格以修改版面配置
* 新增遙測圖格以顯示狀況
* 新增屬性圖格以顯示裝置詳細資料
* 新增命令圖格以執行命令

您現在已在 Azure IoT Central 應用程式中自訂儀表板，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [匯出資料並以視覺化方式呈現見解](./tutorial-in-store-analytics-export-data-visualize-insights.md)
