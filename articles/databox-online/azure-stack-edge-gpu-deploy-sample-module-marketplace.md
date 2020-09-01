---
title: 從 Azure Marketplace 在您的 Microsoft Azure Stack Edge 裝置上部署 GPU 模組 |Microsoft Docs
description: 說明如何啟用計算，並透過本機 UI 讓您的 Azure Stack Edge 裝置計算就緒。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 1532283be8521ad5c6cd5a9e2252e5ba839a4aa3
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083492"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-device"></a>從 Azure Stack Edge 裝置上的 Azure Marketplace 部署已啟用 GPU 的 IoT 模組

本文說明如何從 Azure Stack Edge 裝置上的 Azure Marketplace，將圖形處理單元部署 (GPU) 啟用的 IoT Edge 模組。 

在本文中，您將學會如何：
  - 準備 Azure Stack Edge 以執行 GPU 模組。
  - 從 Azure Marketplace 下載並部署已啟用 GPU 的 IoT 模組。
  - 監視模組輸出。

## <a name="about-sample-module"></a>關於範例模組

本文中的 GPU 範例模組包含適用于 GPU 之 CPU 的 PyTorch 和 TensorFlow 基準範例程式碼。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您擁有：

- 您可以存取已啟用 GPU 的1個節點 Azure Stack Edge 裝置。 此裝置會使用 Azure 中的資源來啟用。 
- 您已在此裝置上設定計算。 
    - 遵循 [教學課程：設定 Azure Stack Edge 裝置上的計算](azure-stack-edge-gpu-deploy-configure-compute.md)中的步驟。
    - 請務必 
- Windows 用戶端上的下列開發資源：
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Visual Studio Code 的 Azure IoT Edge 延伸](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)模組。   


## <a name="get-module-from-azure-marketplace"></a>從 Azure Marketplace 取得模組

1. 流覽 [Azure Marketplace 中的所有應用程式](https://azuremarketplace.microsoft.com/marketplace/apps)。

    ![流覽 Azure Marketplace 中的應用程式](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. 搜尋開始 **使用 gpu**。

    ![搜尋 GPU 範例模組](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. 選取 [立即取得]。

    ![取得範例模組](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. 選取 [ **繼續** ] 以確認提供者的使用規定和隱私權原則。 

    ![取得範例模組](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. 選擇您用來部署 Azure Stack Edge 裝置的訂用帳戶。

    ![選取訂閱](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. 輸入您在設定 Azure Stack Edge 裝置時所建立的 IoT 中樞服務名稱。 若要尋找此 IoT 中樞服務名稱，請在 Azure 入口網站中移至與您的裝置相關聯的 Azure Stack Edge 資源。 

    1. 在左窗格的功能表選項中，移至 [ **Edge 計算 > 開始**使用]。 

    1. 在 [ **設定 Edge 計算** ] 磚中，選取 [ **View config**]。 

        ![查看計算設定](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. 在 **Edge 計算** 設定分頁中：

        1. 記下您在 Azure Stack Edge 裝置上設定計算時所建立的 IoT 中樞服務。
        2. 請注意您在設定計算時所建立 IoT Edge 裝置的名稱。 您將在後續步驟中使用此名稱。

        ![Edge 計算設定](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. 選擇 [部署至裝置]****。

11. 輸入 IoT Edge 裝置的名稱，或選取 [ **尋找裝置**   ]，以在與中樞註冊的裝置之間流覽。

    ![尋找裝置](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. 選取 [ **建立**]   以繼續設定部署資訊清單的標準程式，包括視需要新增其他模組。 會預先定義新模組的詳細資料 (例如影像 URI、建立選項與必要屬性)，而且您可以視需要編輯它們。

    ![選取 [建立]](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. 確認模組已部署在您的 IoT 中樞的 Azure 入口網站中。 選取您的裝置，選取 [ **設定模組**]，   模組應該會列在 [ **IoT Edge 模組**]   區段中。

    ![選取 [建立]](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>監視模組  

1. 在 VS Code 命令選擇區中，執行 [Azure IoT 中樞：選取 IoT 中樞]****。

2. 選擇您要設定的 IoT Edge 裝置所屬的訂用帳戶和 IoT 中樞。 在此情況下，請選取用來部署 Azure Stack Edge 裝置的訂用帳戶，然後選取為 Azure Stack Edge 裝置所建立的 IoT Edge 裝置。 當您在先前的步驟中透過 Azure 入口網站設定計算時，就會發生這種情況。

3. 在 VS Code explorer 中，展開 [Azure IoT 中樞] 區段。 在 [ **裝置**] 下，您應該會看到對應至 Azure Stack Edge 裝置的 IoT Edge 裝置。 

    1. 選取該裝置，按一下滑鼠右鍵，然後選取 [ **開始監視內建事件端點**]。
  
        ![開始監視](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. 移至 [ **裝置] > 模組** ，您應該會看到您的 **GPU 模組** 正在執行。

    3. VS Code 終端機也應顯示 IoT 中樞事件，作為 Azure Stack Edge 裝置的監視輸出。

        ![監視輸出](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        您可以看到執行一組相同作業所花費的時間 (5000 的圖形轉換) 反復專案，與 CPU 的變化很小。

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何 [設定 GPU 以使用模組](azure-stack-edge-j-series-configure-gpu-modules.md)。
