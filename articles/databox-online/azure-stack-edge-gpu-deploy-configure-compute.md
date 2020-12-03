---
title: 使用 Azure Stack Edge Pro GPU 上計算功能來篩選、分析資料的教學課程 | Microsoft Docs
description: 了解如何設定 Azure Stack Edge Pro GPU 上的計算角色，並使用它在資料傳送至 Azure 之前先轉換資料。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 711da24b3edf08f4867109d0d70165955236c39a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184649"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>教學課程：在 Azure Stack Edge Pro GPU 裝置上設定計算

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本教學課程說明如何在 Azure Stack Edge Pro 裝置上設定計算角色並建立 Kubernetes 叢集。 

此程序大約需要 20 至 30 分鐘才能完成。


在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定計算
> * 取得 Kubernetes 端點

 
## <a name="prerequisites"></a>必要條件

在 Azure Stack Edge Pro 裝置上設定計算角色之前，請確定：

- 您已依照[啟動 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) 中的說明來啟動 Azure Stack Edge Pro 裝置。
- 請確定您已遵循[啟用計算網路](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)中的指示，以及：
    - 已啟用網路介面進行計算。
    - 指派的 Kubernetes 節點 IP 和 Kubernetes 外部服務 IP。

## <a name="configure-compute"></a>設定計算

若要設定 Azure Stack Edge Pro 上的計算，您會透過 Azure 入口網站建立 IoT 中樞資源。

1. 在 Azure Stack Edge 資源的 Azure 入口網站中，移至 [概觀]。 在右窗格的 [計算] 圖格上，選取 [開始使用]。

    ![開始使用計算](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. 在 [設定 Edge 計算] 圖格上，選取 [設定計算]。

    ![設定計算](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. 在 [設定 Edge 計算] 刀鋒視窗上，輸入下列資訊：

   
    |欄位  |值  |
    |---------|---------|
    |IoT 中樞     | 選擇 [新增] 或 [現有]。 <br> 根據預設，標準層 (S1) 用來建立 IoT 資源。 若要使用免費層 IoT 資源，請建立一個資源，然後選取現有的資源。 <br> 在每個案例中，IoT 中樞資源會使用 Azure Stack Edge 資源所用的相同訂用帳戶和資源群組。     |
    |名稱     |輸入 IoT 中樞資源的名稱。         |

    ![開始使用計算 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. 選取 [建立]。 建立 IoT 中樞資源需要幾分鐘的時間。 建立 IoT 中樞資源之後，[設定計算] 圖格會更新以顯示計算組態。 

    ![開始使用計算 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. 若要確認已設定 Edge 計算角色，請選取 [設定計算] 圖格上的 [檢視計算]。
    
    ![開始使用計算 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > 如果在 [設定計算] 對話方塊關閉後，IoT 中樞才與 Azure Stack Edge Pro 裝置相關聯，則會建立 IoT 中樞，但不會顯示在計算設定中。 
    
在 Edge 裝置上設定 Edge 計算角色時，其會建立兩個裝置：一個 IoT 裝置和一個 IoT Edge 裝置。 這兩個裝置都可以在 IoT 中樞資源中檢視。 IoT Edge 執行階段也是在此 IoT Edge 裝置上執行。 目前只有 Linux 平台適用於您的 IoT Edge 裝置。

設定計算可能需要 20-30 分鐘的時間，因為系統會在幕後作業並建立虛擬機器和 Kubernetes 叢集。 

在 Azure 入口網站中成功設定計算之後，就會存在與 IoT 命名空間 (由 Azure Stack Edge Pro 控制的系統命名空間) 相關聯的 Kubernetes 叢集和預設使用者。 

## <a name="get-kubernetes-endpoints"></a>取得 Kubernetes 端點

若要設定用戶端來存取 Kubernetes 叢集，您需要 Kubernetes 端點。 請遵循下列步驟，從 Azure Stack Edge Pro 裝置的本機 UI 取得 Kubernetes API 端點。

1. 在裝置的本機 Web UI 中，移至 **裝置** 頁面。
2. 在 **裝置端點** 下，複製 **Kubernetes API 服務** 端點。 此端點的字串為下列格式：`https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`。 

    ![本機 UI 中的裝置頁面](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. 儲存端點字串。 稍後設定用戶端以透過 kubectl 存取 Kubernetes 叢集時，將會用到此資訊。

4. 在本機 Web UI 中時，您可以：

    - 移至 Kubernetes API，選取 [進階設定] 並下載 Kubernetes 的進階設定檔。 

        ![本機 UI 1 中的裝置頁面](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        如果 Microsoft 已提供金鑰 (選取可能具備此金鑰的使用者)，則可以使用此設定檔。

        ![本機 UI 中的裝置頁面 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - 您也可以移至 **Kubernetes 儀表板** 端點，並下載 `aseuser` 設定檔。 
    
        ![本機 UI 中的裝置頁面 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        您可以使用此設定檔登入 Kubernetes 儀表板，或在 Kubernetes 叢集中針對所有問題進行偵錯。 如需詳細資訊，請參閱[存取 Kubernetes 儀表板](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard)。 


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定計算
> * 取得 Kubernetes 端點


若要了解如何管理您的 Azure Stack Edge Pro 裝置，請參閱：

> [!div class="nextstepaction"]
> [使用本機 Web UI 來管理 Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
