---
title: 在 Microsoft Azure Stack Edge Pro GPU 裝置上執行 GPU 模組 |Microsoft Docs
description: 說明如何透過 Azure 入口網站，在 Azure Stack Edge Pro 裝置的 GPU 上設定和執行模組。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: d172ce98ba93360c621a91fb0e2a55d022470943
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935545"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>在 Azure Stack Edge Pro 裝置上的 GPU 上設定和執行模組

您 Azure Stack Edge Pro 裝置包含一或多個圖形處理單位 (GPU) 。 Gpu 是 AI 計算的常用選擇，因為它們提供平行處理功能，而影像轉譯的速度比中央處理單位 (Cpu) 更快。 如需 Azure Stack Edge Pro 裝置所含 GPU 的詳細資訊，請移至 [Azure Stack Edge Pro 裝置技術規格](azure-stack-edge-gpu-technical-specifications-compliance.md)。

本文說明如何在 Azure Stack Edge Pro 裝置上的 GPU 上設定和執行模組。 在本文中，您將使用針對 Nvidia T4 Gpu 撰寫的公開可用容器模組 **數位** 。 此程式可以用來設定 Nvidia 針對這些 Gpu 所發佈的任何其他模組。


## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：

1. 您可以存取已啟用 GPU 的1個節點 Azure Stack Edge Pro 裝置。 此裝置會使用 Azure 中的資源來啟動。  

## <a name="configure-module-to-use-gpu"></a>設定模組以使用 GPU

若要將模組設定為使用 Azure Stack Edge Pro 裝置上的 GPU 來執行模組，<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> 請遵循下列步驟。

1. 在 Azure 入口網站中，移至與您的裝置相關聯的資源。

2. 在 **[總覽**] 中，選取 [ **IoT Edge**]。

    ![將模組設定為使用 GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. 在 [ **啟用 IoT Edge 服務**] 中，選取 [ **新增**]。

   ![將模組設定為使用 GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. 在 [ **建立 IoT Edge 服務**] 中，輸入 IoT 中樞資源的設定：

   |欄位   |值    |
   |--------|---------|
   |訂用帳戶      | Azure Stack Edge 資源使用的訂用帳戶。 |
   |資源群組    | Azure Stack Edge 資源使用的資源群組。 |
   |IoT 中樞           | 選擇 [ **建立新** 的] 或 [ **使用現有** 的]。 <br> 根據預設，標準層 (S1) 用來建立 IoT 資源。 若要使用免費層 IoT 資源，請建立一個資源，然後選取現有的資源。 <br> 在每個案例中，IoT 中樞資源會使用 Azure Stack Edge 資源所用的相同訂用帳戶和資源群組。     |
   |名稱              | 如果您不想要使用為新的 IoT 中樞資源提供的預設名稱，請輸入不同的名稱。 |

   當您完成設定時，請選取 [ **審核 + 建立**]。 檢查 IoT 中樞資源的設定，然後選取 [ **建立**]。

   ![開始使用計算 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

   建立 IoT 中樞資源的資源需要幾分鐘的時間。 建立資源之後， **總覽** 會指出 IoT Edge 服務現在正在執行。

   ![開始使用計算 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. 若要確認已設定 Edge 計算角色，請選取 [ **屬性**]。

   ![開始使用計算 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

6. 在 [ **屬性**] 中，選取 **IoT Edge 裝置** 的連結。

   ![將模組設定為使用 GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

   在右窗格中，您會看到與您 Azure Stack Edge Pro 裝置相關聯的 IoT Edge 裝置。 此裝置會對應至您在建立 IoT 中樞資源時所建立的 IoT Edge 裝置。
 
7. 選取此 IoT Edge 裝置。

   ![將模組設定為使用 GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

8. 選取 [設定模組]。

   ![將模組設定為使用 GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

9. 選取 [ **+ 新增** ]，然後選取 [ **+ IoT Edge 模組**]。 

    ![將模組設定為使用 GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

10. 在 [ **新增 IoT Edge 模組** ] 索引標籤中：

    1. 提供 **映射 URI**。 您將在這裡使用公開可用的 Nvidia 模組 **數位** 。 
    
    2. 將 **重新開機原則** 設定為 **always**。
    
    3. 將 **需要的狀態** 設定為 [ **正在** 執行]。
    
    ![將模組設定為使用 GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

11. 在 [ **環境變數** ] 索引標籤中，提供變數的名稱和對應的值。 

    1. 若要讓目前的模組在此裝置上使用一個 GPU，請使用 NVIDIA_VISIBLE_DEVICES。 

    2. 將值設定為 0 或 1。 值為0或1，可確保此課程模組的裝置至少使用一個 GPU。 當您將值設定為0，1時，表示您裝置上的兩個 Gpu 都會由此模組使用。

       ![將模組設定為使用 GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

       如需可搭配 Nvidia GPU 使用之環境變數的詳細資訊，請移至 [nvidia 容器運行](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)時間。

    > [!NOTE]
    > GPU 只能對應到一個模組。 不過，模組可以使用其中一個，也可以使用 Gpu。

12. 輸入模組的名稱。 此時，您可以選擇提供容器建立選項和修改模組對應項設定，如果已完成，請選取 [ **新增**]。 

    ![將模組設定為使用 GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

13. 確定模組正在執行，然後選取 [ **審核 + 建立**]。

    ![將模組設定為使用 GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

14. 在 [ **審核 + 建立** ] 索引標籤中，會顯示您選取的部署選項。 查看選項，然後選取 [ **建立**]。
    
    ![將模組設定為使用 GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

15. 記下模組的 **執行時間狀態** 。
    
    ![將模組設定為使用 GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    部署模組需要幾分鐘的時間。 選取 **[** 重新整理]，您應該會看到執行 **中的****執行時間狀態** 更新。

    ![將模組設定為使用 GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>後續步驟

- 深入瞭解 [您可以搭配 NVIDIA GPU 使用的環境變數](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)。
