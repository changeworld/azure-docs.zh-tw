---
title: 使用 Azure Stack Edge Pro 上的計算功能來篩選、分析資料以進行進階部署的教學課程 | Microsoft Docs
description: 了解如何設定 Azure Stack Edge Pro 上的計算角色，並在資料傳送至 Azure 之前用它來轉換資料以進行進階部署流程。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 8946dfca9a416009effb45cad1e81348dd900f98
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968110"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro-for-advanced-deployment-flow"></a>教學課程：使用 Azure Stack Edge Pro 轉換資料以進行進階部署

本教學課程說明如何在 Azure Stack Edge Pro 裝置上設定計算角色，以進行進階部署流程。 當您設定計算角色之後，Azure Stack Edge Pro 就能先轉換資料，然後再將資料傳送至 Azure。

您可以為裝置上的簡單或進階部署流程設定計算功能。

| 準則 | 簡單部署                                | 進階部署                   |
|------------------|--------------------------------------------------|---------------------------------------|
| 適用對象     | IT 系統管理員                                | 開發人員                            |
| 類型             | 使用 Azure Stack Edge 服務來部署模組      | 使用 IoT 中樞服務部署模組 |
| 部署的模組 | Single                                           | 鏈結或多個模組           |


此程序大約需要 20 至 30 分鐘才能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定計算
> * 新增共用
> * 新增觸發程序
> * 新增計算模組
> * 確認資料轉換和傳輸

 
## <a name="prerequisites"></a>必要條件

在 Azure Stack Edge Pro 裝置上設定計算角色之前，請確定：

- 您已依照[連線、設定及啟用 Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md) 中的說明來啟動 Azure Stack Edge Pro 裝置。


## <a name="configure-compute"></a>設定計算

若要設定 Azure Stack Edge Pro 上的計算，您將建立 IoT 中樞資源。

1. 在 Azure Stack Edge 資源的 Azure 入口網站中，移至 [概觀]。 在右側窗格中，選取 [IoT Edge] 圖格。

    ![開始使用計算](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. 在 **啟用 IoT Edge 服務** 圖格中，選取 [新增]。 此動作會啟用 IoT Edge 服務，讓您在本機裝置上部署 IoT Edge 模組。

    ![開始使用計算 2](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. 在 **建立 IoT Edge 服務** 上，輸入下列內容：

   
    |欄位  |值  |
    |---------|---------|
    |訂用帳戶     |選取您 IoT 中樞資源的訂用帳戶。 您可以選取與 Azure Stack Edge 資源所用相同的訂用帳戶。        |
    |資源群組     |輸入 IoT 中樞資源資源群組的名稱。 您可以選取與 Azure Stack Edge 資源所用相同的資源群組。         |
    |IoT 中樞     | 選擇 [新增]  或 [現有]  。 <br> 根據預設，標準層 (S1) 用來建立 IoT 資源。 若要使用免費層 IoT 資源，請建立一個資源，然後選取現有的資源。      |
    |名稱     |接受預設值，或輸入 IoT 中樞資源的名稱。         |

    ![開始使用計算 3](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. 選取 [檢閱 + 建立]  。 建立 IoT 中樞資源需要幾分鐘的時間。 建立 IoT 中樞資源之後，**概觀** 更新會指出目前正在執行 IoT Edge 服務。 

    在 Edge 裝置上設定 IoT Edge 服務時，會建立兩個裝置：一個 IoT 裝置和一個 IoT Edge 裝置。 這兩個裝置都可以在 IoT 中樞資源中檢視。 IoT Edge 執行階段也是在此 IoT Edge 裝置上執行。 目前只有 Linux 平台適用於您的 IoT Edge 裝置。

    若要確認已設定 Edge 計算角色，請選取 [IoT Edge 服務] > [屬性] 並檢視 IoT 裝置和 IoT Edge 裝置。 

    ![開始使用計算 4](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-4.png)
    

## <a name="add-shares"></a>新增共用

對於本教學課程中的進階部署，您需要兩個共用：一個 Edge 共用和一個 Edge 本機共用。

1. 執行下列步驟，在裝置上新增 Edge 共用：

    1. 在您的 Azure Stack Edge 資源中，移至 [IoT Edge] > [共用]。
    2. 在 **共用** 頁面上，從命令列中選取 [+ 新增共用]。
    3. 在 [新增共用] 刀鋒視窗上，提供共用名稱並選取共用類型。
    4. 若要掛接 Edge 共用，請選取 [使用共用搭配 Edge 計算]  核取方塊。
    5. 選取 [儲存體帳戶]  、[儲存體服務]  現有使用者，然後選取 [建立]  。

        ![新增 Edge 共用](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    Edge 共用建立後，您會收到成功建立通知。 共用清單會更新以反映新的共用。

2. 重複上一步中的所有步驟並選取 [設定為 Edge 本機共用] 核取方塊，以在 Edge 裝置上新增 Edge 本機共用。 本機共用上的資料會留在裝置上。

    ![新增 Edge 本機共用](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. 在 [共用] 刀鋒視窗上，您會看到更新的共用清單。

    ![更新的共用清單](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. 若要檢視新建立之本機共用的屬性，請從清單中選取共用。 在 [用於 Edge 計算模組的本機掛接點] 方塊中，複製對應到此共用的值。

    當您部署模組時，將會使用這個本機掛接點。

    ![[用於 Edge 計算模組的本機掛接點] 方塊](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. 若要檢視您所建立之 Edge 共用的屬性，請從清單中選取共用。 在 [用於 Edge 計算模組的本機掛接點] 方塊中，複製對應到此共用的值。

    當您部署模組時，將會使用這個本機掛接點。

    ![新增自訂模組](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>新增觸發程序

1. 移至您的 Azure Stack Edge 資源，然後移至 [IoT Edge] > [觸發程序]。 選取 [+ 新增觸發程序]。

    ![新增觸發程序](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. 在 [新增觸發程序] 刀鋒視窗中，輸入下列值。

    |欄位  |值  |
    |---------|---------|
    |觸發程序名稱     | 觸發程序的唯一名稱。         |
    |觸發程序類型     | 選取 [檔案] 觸發程序。 每當發生檔案事件 (例如檔案寫入至輸入共用) 時，就會引發檔案觸發程序。 另一方面，排定的觸發程序會根據您所定義的排程引發。 在本例中，我們需要檔案觸發程序。    |
    |輸入共用     | 選取輸入共用。 在此情況下，Edge 本機共用是輸入共用。 此處使用的模組會將檔案從 Edge 本機共用移到 Edge 共用，以便上傳至雲端。        |

    ![新增觸發程序 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. 觸發程序建立後，您會收到通知。 觸發程序清單會更新，以顯示新建立的觸發程序。 選取您剛剛建立的觸發程序。

    ![新增觸發程序 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. 複製並儲存範例路由。 您將修改此範例路由，且稍後會在 IoT 中樞加以使用。

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![新增觸發程序 4](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>新增模組

此 Edge 裝置上並沒有自訂模組。 您可以新增自訂或預先建置的模組。 若要了解如何建立自訂模組，請移至[為您的 Azure Stack Edge Pro 裝置開發 C# 模組](azure-stack-edge-create-iot-edge-module.md)。

在本節中，您會將自訂模組新增至您在[為您的 Azure Stack Edge Pro 開發 C# 模組](azure-stack-edge-create-iot-edge-module.md)中建立的 IoT Edge 裝置。 此自訂模組會從 Edge 裝置上的 Edge 本機共用中取用檔案，並將其移至裝置上的 Edge (雲端) 共用。 接著，雲端共用會將檔案推送至與該雲端共用相關聯的 Azure 儲存體帳戶。

1. 移至您的 Azure Stack Edge 資源，然後移至 [IoT Edge] > [概觀]。 在 **模組** 圖格上，選取 [移至 Azure IoT 中樞]。

    ![選取進階部署](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. 在您的 IoT 中樞資源中移至 **IoT Edge 裝置**，然後選取您的 IoT Edge 裝置。

    ![移至 IoT 中樞內的 IoT Edge 裝置](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-2.png)

3. 在 [裝置詳細資料] 上，選取 [設定模組]。

    ![[設定模組] 連結](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-3.png)

4. 在 [新增模組] 下方，執行下列步驟：

    1. 針對自訂模組的容器登錄設定，輸入名稱、位址、使用者名稱和密碼。
    名稱、位址和列出的認證會用來擷取具有相符 URL 的模組。 若要部署此模組，請在 [部署模組] 下方選取 [IoT Edge 模組]。 此 IoT Edge 模組是一個 Docker 容器，可供您部署至與您 Azure Stack Edge Pro 裝置相關聯的 IoT Edge 裝置。

        ![[設定模組] 頁面](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. 指定 IoT Edge 自訂模組的設定。 輸入下列值。
     
        |欄位  |值  |
        |---------|---------|
        |名稱     | 模組的唯一名稱。 此模組是一個 Docker 容器，可供您部署至與您 Azure Stack Edge Pro 裝置相關聯的 IoT Edge。        |
        |映像 URI     | 模組的對應容器映像的映像 URI。        |
        |需要認證     | 若已核取，使用者名稱和密碼會用來擷取具有相符 URL 的模組。        |
    
        在 [容器建立選項] 方塊中，輸入您在先前的步驟中為 Edge 共用和 Edge 本機共用複製之 Edge 模組的本機掛接點。

        > [!IMPORTANT]
        > 此處使用的路徑會掛接到您的容器中，必須與您容器中的功能所預期的路徑相符。 如果您依照[建立自訂模組](azure-stack-edge-create-iot-edge-module.md#update-the-module-with-custom-code)的指示操作，則指定於該模組中的程式碼會預期複製的路徑。 請勿修改這些路徑。
    
        在 [容器建立選項] 方塊中，您可以貼上下列範例：
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        請提供您的模組所使用的任何環境變數。 環境變數會提供選用資訊，以利定義您的模組執行所在的環境。

        ![[容器建立選項] 方塊](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. 視需要設定進階 Edge 執行階段設定，然後按 [下一步]。

        ![新增自訂模組 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5. 在 [指定路由] 下方，設定模組之間的路由。  
   
   ![指定路由](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-7.png)

    您可以將 *route* 取代為先前複製的下列路由字串。 在此範例中，輸入要將資料推送至雲端共用的本機共用名稱。 請將 `modulename` 取代為模組的名稱。 選取 [下一步] 。
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![[指定路由] 區段](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-8.png)

6. 在 [檢閱部署] 下方檢閱所有設定，然後選取 [提交] 以提交模組進行部署。

   ![設定模組頁面 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    此動作會啟動模組部署。 部署完成後，模組的 **執行階段狀態** 將是 **執行中**。

    ![新增自訂模組 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>確認資料轉換和傳輸

最後一個步驟是確定模組已如預期般連線並執行。 在 IoT 中樞資源中，模組應處於為您的 IoT Edge 裝置執行中的執行階段狀態。

執行下列步驟，以確認資料已轉換並傳輸至 Azure。
 
1. 在 [檔案總管] 中，連線到您先前建立的 Edge 本機共用和 Edge 共用。

   ![確認資料轉換](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1. 將資料新增至本機共用。

   ![確認資料轉換 2](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    資料會移至雲端共用。

    ![確認資料轉換 3](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    然後將資料從雲端共用推送至儲存體帳戶。 若要檢視資料，請移至您的儲存體帳戶，然後選取 [儲存體總管]。 您可以在儲存體帳戶中檢視已上傳的資料。

    ![確認資料轉換 4](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
您已完成驗證流程。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定計算
> * 新增共用
> * 新增觸發程序
> * 新增計算模組
> * 確認資料轉換和傳輸

若要了解如何管理您的 Azure Stack Edge Pro 裝置，請參閱：

> [!div class="nextstepaction"]
> [使用本機 Web UI 來管理 Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
