---
title: 在 Azure Stack Edge Pro GPU 裝置上部署 GPU 模組 |Microsoft Docs
description: 說明如何啟用計算，並透過本機 UI 讓您的 Azure Stack Edge Pro 裝置計算就緒。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 1f16ef0ede25f17acb915a7812ae5b15b45f78a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899725"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 裝置上部署已啟用 GPU 的 IoT 模組

本文說明如何在 Azure Stack Edge Pro GPU 裝置上部署已啟用的 GPU IoT Edge 模組。 

在本文中，您將學會如何：
  - 準備 Azure Stack Edge Pro 來執行 GPU 模組。
  - 從 Git 存放庫下載並安裝範例程式碼。
  - 建立解決方案並產生部署資訊清單。
  - 將解決方案部署到 Azure Stack Edge Pro 裝置。
  - 監視模組輸出。


## <a name="about-sample-module"></a>關於範例模組

本文中的 GPU 範例模組包含適用于 GPU 之 CPU 的 PyTorch 和 TensorFlow 基準範例程式碼。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您擁有：

- 您可以存取已啟用 GPU 的1個節點 Azure Stack Edge Pro 裝置。 此裝置會使用 Azure 中的資源來啟用。 請參閱 [啟用裝置](azure-stack-edge-gpu-deploy-activate.md)。
- 您已在此裝置上設定計算。 遵循 [教學課程：設定 Azure Stack Edge Pro 裝置上的計算](azure-stack-edge-gpu-deploy-configure-compute.md)中的步驟。
- Azure Container Registry (ACR) 。 移至 [ **存取金鑰** ] 分頁，並記下 [ACR 登入伺服器]、[使用者名稱] 和 [密碼]。 如需詳細資訊，請參閱 [快速入門：使用 Azure 入口網站建立私用容器](../container-registry/container-registry-get-started-portal.md#create-a-container-registry)登錄。
- Windows 用戶端上的下列開發資源：
    - [Azure CLI 2.0 或更新版本](https://aka.ms/installazurecliwindows)
    - [DOCKER CE](https://store.docker.com/editions/community/docker-ce-desktop-windows)。 您可能必須建立帳戶，才能下載並安裝軟體。
    - [Visual Studio Code](https://code.visualstudio.com/) \(英文\)  
    - [Visual Studio Code 的 Azure IoT Edge 延伸](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)模組。    
    - [適用於 Visual Studio Code 的 Python 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-python.python) \(英文\)    
    - [Python 3](https://www.python.org/) \(英文\)    
    - 安裝 Python 套件的 Pip (通常隨附于您的 Python 安裝) 

## <a name="get-the-sample-code"></a>取得範例程式碼

1. 前往 [azure 範例中的 Azure 智慧型邊緣模式](https://github.com/azure-samples/azure-intelligent-edge-patterns)。 複製或下載程式代碼的 zip 檔案。 

    ![下載 zip 檔](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    從 zip 解壓縮檔案。 您也可以複製範例。

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>組建和部署模組

1. 在 Visual Studio Code 中開啟 [ **GpuReferenceModules** ] 資料夾。

    ![在 VS Code 中開啟 GPUReferenceModules](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. 開啟 *deployment.template.js* ，並識別其為容器登錄所參考的參數。 在下列檔案中，會使用 CONTAINER_REGISTRY_USERNAME、CONTAINER_REGISTRY_PASSWORD 和 CONTAINER_REGISTRY_NAME。

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. 建立新檔案。 填寫您 container registry 參數的值， (使用先前步驟中所識別的值) 如下所示： 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    範例 *env* 檔案如下所示：
    
    ![建立並儲存 env 檔案](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. 將檔案儲存為**samplesolution.zip**資料夾中的*env。*

5. 若要登入 Docker，請在 Visual Studio Code 整合式終端機中輸入下列命令。 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    移至 Azure 入口網站中 container registry 的 [ **存取金鑰** ] 區段。 複製並使用登錄名稱、密碼和登入伺服器。

    ![存取容器登錄中的金鑰](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    提供認證之後，登入便會成功。

    ![成功登入](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. 將您的映射推送至 Azure container registry。 在 VS Code Explorer 中，選取並以滑鼠右鍵按一下檔案 **deployment.template.js** ，然後選取 [ **建立並推送 IoT Edge 方案**]。 

    ![建置並推送 IoT Edge 解決方案](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    如果未安裝 Python 和 Python 延伸模組，則會在您建立並推送解決方案時安裝這些延伸模組。 不過，這會導致較長的組建時間。 

    完成此步驟之後，您會在容器登錄中看到該模組。

    ![Container registry 中的模組](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. 若要建立部署資訊清單，請以滑鼠右鍵按一下 **deployment.template.js** ，然後選取 [ **產生 IoT Edge 部署資訊清單**]。 

    ![產生 IoT Edge 部署資訊清單](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    通知會通知您產生部署資訊清單的路徑。 資訊清單是 `deployment.amd64.json` 在 **config** 資料夾中產生的檔案。 

8. 選取 [ **config** ] 資料夾中的 [ **on** file]deployment.amd64.js，然後選擇 [**建立單一裝置的部署**]。 請勿使用 on file 的 **deployment.template.js** 。 

    ![建立單一裝置的部署](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    在 [ **輸出** ] 視窗中，您應該會看到部署成功的訊息。

    ![已成功在輸出中部署](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>監視模組  

1. 在 VS Code 命令選擇區中，執行 [Azure IoT 中樞：選取 IoT 中樞]****。

2. 選擇您要設定的 IoT Edge 裝置所屬的訂用帳戶和 IoT 中樞。 在此情況下，請選取用來部署 Azure Stack Edge Pro 裝置的訂用帳戶，然後選取為 Azure Stack Edge Pro 裝置所建立的 IoT Edge 裝置。 當您在先前的步驟中透過 Azure 入口網站設定計算時，就會發生這種情況。

3. 在 VS Code explorer 中，展開 [Azure IoT 中樞] 區段。 在 [ **裝置**] 下，您應該會看到對應至 Azure Stack Edge Pro 裝置的 IoT Edge 裝置。 

    1. 選取該裝置，按一下滑鼠右鍵，然後選取 [ **開始監視內建事件端點**]。
  
        ![開始監視](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. 移至 [ **裝置] > 模組** ，您應該會看到您的 **GPU 模組** 正在執行。

        ![IoT 中樞內的模組](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. VS Code 終端機也應顯示 IoT 中樞事件，作為 Azure Stack Edge Pro 裝置的監視輸出。

        ![監視輸出](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        您可以看到執行一組相同作業所花費的時間 (5000 的圖形轉換) 反復專案，與 CPU 的變化很小。

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何 [設定 GPU 以使用模組](azure-stack-edge-j-series-configure-gpu-modules.md)。
