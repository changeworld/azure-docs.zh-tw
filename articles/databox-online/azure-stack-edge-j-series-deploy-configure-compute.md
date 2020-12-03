---
title: 使用 Azure Stack Edge Pro 搭配 GPU 的計算功能來篩選、分析資料的教學課程 | Microsoft Docs
description: 了解如何設定 Azure Stack Edge Pro GPU 裝置上的計算角色，並使用它在資料傳送至 Azure 之前先轉換資料。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 75428b28095b0e425a1670caffcf960aa6ae58f6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185499"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro"></a>教學課程：使用 Azure Stack Edge Pro 轉換資料

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本教學課程說明如何在 Azure Stack Edge Pro 裝置上設定計算角色。 當您設定計算角色之後，Azure Stack Edge Pro 就能先轉換資料，然後再將資料傳送至 Azure。

此程序大約需要 10 至 15 分鐘才能完成。


在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定計算
> * 新增共用
> * 新增計算模組
> * 確認資料轉換和傳輸

 
## <a name="prerequisites"></a>必要條件

在 Azure Stack Edge Pro 裝置上設定計算角色之前，請確定：

- 您已依照[啟動您的 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) 中的說明來啟動 Azure Stack Edge Pro 裝置。


## <a name="configure-compute"></a>設定計算

若要設定 Azure Stack Edge Pro 上的計算，您將建立 IoT 中樞資源。

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


## <a name="add-shares"></a>新增共用

對於本教學課程中的簡單部署，您需要兩個共用：一個 Edge 共用和另一個 Edge 本機共用。

1. 執行下列步驟，在裝置上新增 Edge 共用：

    1. 在您的 Azure Stack Edge 資源中，移至 [Edge 計算] > [開始使用]。
    2. 在 [新增共用] 圖格上，選取 [新增]。

        ![新增共用圖格](./media/azure-stack-edge-j-series-deploy-configure-compute/add-share-1.png) 

    3. 在 [新增共用] 刀鋒視窗上，提供共用名稱並選取共用類型。
    4. 若要掛接 Edge 共用，請選取 [使用共用搭配 Edge 計算] 核取方塊。
    5. 選取 [儲存體帳戶]、[儲存體服務]現有使用者，然後選取 [建立]。

        ![新增 Edge 共用](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-1.png) 

    如果您已建立本機 NFS 共用，請使用下列遠端同步 (rsync) 命令選項來將檔案複製到共用上：

    `rsync <source file path> < destination file path>`

    如需 `rsync` 命令的詳細資訊，請移至 [Rsync 文件](https://www.computerhope.com/unix/rsync.htm)。

    > [!NOTE]
    > 若要將 NFS 共用掛接至計算，必須在與 NFS 虛擬 IP 位址相同的子網路上設定計算網路。 如需如何設定計算網路的詳細資訊，請移至[在您的 Azure Stack Edge Pro 上啟用計算網路](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。

    Edge 共用即會建立，而您將會收到成功建立通知。 共用清單可能會更新，但您必須等到共用建立完成。

2. 重複上一步中的所有步驟並選取 [設定為 Edge 本機共用] 核取方塊，以在 Edge 裝置上新增 Edge 本機共用。 本機共用上的資料會留在裝置上。

    ![新增 Edge 本機共用](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-2.png)

  
3. 選取 [新增共用] 以查看更新後的共用清單。

    ![更新的共用清單](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>新增模組

您可以新增自訂或預先建置的模組。 此 Edge 裝置上並沒有自訂模組。 若要了解如何建立自訂模組，請移至[為您的 Azure Stack Edge Pro 裝置開發 C# 模組](azure-stack-edge-j-series-create-iot-edge-module.md)。

在本節中，您會將自訂模組新增至您在[為您的 Azure Stack Edge Pro 開發 C# 模組](azure-stack-edge-j-series-create-iot-edge-module.md)中建立的 IoT Edge 裝置。 此自訂模組會從 Edge 裝置上的 Edge 本機共用中取用檔案，並將其移至裝置上的 Edge (雲端) 共用。 接著，雲端共用會將檔案推送至與該雲端共用相關聯的 Azure 儲存體帳戶。

1. 移至 [Edge 計算] > [開始使用]。 在 [新增模組] 圖格上，選取 [簡單] 案例類型。 選取 [新增]  。
2. 在 [設定和新增模組] 刀鋒視窗中，輸入下列值：

    
    |欄位  |值  |
    |---------|---------|
    |名稱     | 模組的唯一名稱。 此模組是一個 Docker 容器，可供您部署至與您 Azure Stack Edge Pro 裝置相關聯的 IoT Edge。        |
    |映像 URI     | 模組的對應容器映像的映像 URI。        |
    |需要認證     | 若已核取，使用者名稱和密碼會用來擷取具有相符 URL 的模組。        |
    |輸入共用     | 選取輸入共用。 在此情況下，Edge 本機共用是輸入共用。 此處使用的模組會將檔案從 Edge 本機共用移到 Edge 共用，以便上傳至雲端。        |
    |輸出共用     | 選取輸出共用。 在此情況下，Edge 共用是輸出共用。        |
    |觸發程序類型     | 選取 [檔案] 或 [排程]。 每當發生檔案事件 (例如檔案寫入至輸入共用) 時，就會引發檔案觸發程序。 排定的觸發程序會根據您所定義的排程引發。         |
    |觸發程序名稱     | 觸發程序的唯一名稱。         |
    |環境變數| 選用資訊，可協助定義您的模組將在其中執行的環境。   |

    ![新增和設定模組](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-1.png)

3. 選取 [新增]  。 模組便會新增。 移至 **概觀** 頁面。 **模組** 圖格會更新以指出已部署模組。 

    ![已部署模組](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>確認資料轉換和傳輸

最後一個步驟是確定模組已如預期般連線並執行。 在 IoT 中樞資源中，模組應處於為您的 IoT Edge 裝置執行中的執行階段狀態。

若要確認模組正在執行中，請執行下列步驟：

1. 選取 [新增模組] 圖格。 這會帶您前往 [模組] 刀鋒視窗。 在模組清單中，找出您所部署的模組。 您新增的模組應處於「執行中」的執行階段狀態。

    ![檢視已部署的模組](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-3.png)
 
1. 在 [檔案總管] 中，連線到您先前建立的 Edge 本機共用和 Edge 共用。

    ![確認資料轉換](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-2.png) 
 
1. 將資料新增至本機共用。

    ![確認資料轉換](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-3.png) 
 
   資料會移至雲端共用。

    ![確認資料轉換](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-4.png)  

   然後將資料從雲端共用推送至儲存體帳戶。 若要檢視資料，您可以使用儲存體總管。

    <!--![Verify data transform](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-5.png)-->
 
您已完成驗證流程。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定計算
> * 新增共用
> * 新增計算模組
> * 確認資料轉換和傳輸

若要了解如何管理您的 Azure Stack Edge Pro 裝置，請參閱：

> [!div class="nextstepaction"]
> [使用本機 Web UI 來管理 Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
