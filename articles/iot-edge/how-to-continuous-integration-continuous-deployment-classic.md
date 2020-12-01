---
title: 持續整合和持續部署到 Azure IoT Edge 裝置 (傳統編輯器) -Azure IoT Edge
description: 使用傳統編輯器來設定持續整合和持續部署-Azure IoT Edge 與 Azure DevOps、Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b9b842b94d66cf91ad836b8ae61df1b3d3f34293
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435938"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Azure IoT Edge 裝置的持續整合和持續部署 (傳統編輯器) 

您可以使用 Azure Pipelines 中內建的 Azure IoT Edge 工作，輕鬆地搭配您的 Azure IoT Edge 應用程式採用 DevOps。 本文將示範如何使用 Azure Pipelines 的持續整合與持續部署功能，快速且有效率地使用傳統編輯器來建立、測試及部署應用程式，並將其部署至您的 Azure IoT Edge。 或者，您可以 [使用 YAML](how-to-continuous-integration-continuous-deployment.md)。

![圖表 - 開發和生產的 CI 和 CD 分支](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

在本文中，您將瞭解如何使用 Azure Pipelines 的內建 [Azure IoT Edge](/azure/devops/pipelines/tasks/build/azure-iot-edge) 工作，為您的 IoT Edge 解決方案建立組建和發行管線。 每個新增至管線的 Azure IoT Edge 工作都會執行下列四個動作的其中一個：

 | 動作 | 描述 |
 | --- | --- |
 | 組建模組映射 | 取得您的 IoT Edge 解決方案程式碼，並建立容器映射。|
 | 推送模組映射 | 將模組映射推送到您指定的容器登錄。 |
 | 產生部署資訊清單 | 會取得檔案和變數 deployment.template.js，然後產生最終的 IoT Edge 部署資訊清單檔。 |
 | 部署到 IoT Edge 裝置 | 建立 IoT Edge 部署至一或多個 IoT Edge 裝置。 |

除非另有說明，否則本文中的程式不會探索透過工作參數提供的所有功能。 如需詳細資訊，請參閱下列：

* [工作版本](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Advanced** -如果適用，請指定您不想要建立的模組。
* [控制項選項](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [環境變數](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [輸出變數](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>必要條件

* Azure Repos 存放庫。 如果您沒有存放庫，可以[在專案中建立新的 Git 存放庫](/azure/devops/repos/git/create-new-repo) \(英文\)。 針對本文，我們建立了名為 **IoTEdgeRepo** 的存放庫。
* 已認可並推送至您存放庫的 IoT Edge 解決方案。 若要建立用於測試本文的新範例解決方案，請遵循[在 Visual Studio Code 中針對模組進行開發與偵錯](how-to-vs-code-develop-module.md)，或[在 Visual Studio Code 中對 C# 模組進行開發與偵錯](./how-to-visual-studio-develop-module.md)中的步驟。 在本文中，我們在存放庫中建立了名為 **IoTEdgeSolution** 的解決方案，其中包含名為 **filtermodule** 的模組程式碼。

   針對本文，您只需要在 Visual Studio Code 或 Visual Studio 中由 IoT Edge 範本所建立的解決方案資料夾。 在進行之前，您並不需要針對此程式碼進行建置、推送、部署或偵錯。 您將在 Azure Pipelines 中設定這些處理常式。

   若您是建立新的解決方案，請先在本機複製您的存放庫。 然後，當您建立解決方案時，您可以選擇直接在存放庫資料夾中建立它。 您可以輕鬆地從該處對新檔案進行認可和推送。

* 可對它推送模組映像的容器登錄。 您可以使用 [Azure Container Registry](../container-registry/index.yml) 或協力廠商登錄。
* 使用中的 Azure [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md) ，至少有兩個 IoT Edge 裝置可用來測試個別的測試和生產部署階段。 您可以遵循快速入門文章來在 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 上建立 IoT Edge 裝置

## <a name="create-a-build-pipeline-for-continuous-integration"></a>建立持續整合的組建管線

在本節中，您會建立新的建置管線。 您可以將管線設定為當您將任何變更簽入範例 IoT Edge 方案，併發行組建記錄檔時自動執行。

1. 登入您的 Azure DevOps 組織 (`https://dev.azure.com/{your organization}`) 並開啟包含您 IoT Edge 解決方案存放庫的專案。

    ![開啟您的 DevOps 專案](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. 從專案的左窗格功能表中，選取 [ **管線**]。 選取頁面中央的 [ **建立管線** ]。 或者，如果您已經有組建管線，請選取右上方的 [ **新增管線** ] 按鈕。

    ![建立新的組建管線](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. 在 [您的程式 **代碼在哪裡？** ] 頁面的底部，選取 [ **使用傳統編輯器**]。 如果您想要使用 YAML 來建立專案的組建管線，請參閱 [YAML 指南](how-to-continuous-integration-continuous-deployment.md)。

    ![選取 [使用傳統編輯器]](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. 遵循提示以建立管線。

   1. 提供新建置管線的來源資訊。 選取 [Azure Repos Git] 作為來源，然後選取 IoT Edge 解決方案程式碼所在的專案、存放庫及分支。 然後選取 [ **繼續**]。

      ![選取您的管線來源](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. 選取 [空白作業]，而非範本。

      ![從您的組建管線的空白作業開始](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. 建立管線之後，系統會將您引導至管線編輯器。 在這裡，您可以變更管線的名稱、代理程式組件區和代理程式規格。

   您可以選取 Microsoft 主控的集區，或是您管理的自我裝載集區。

   在您的管線描述中，根據您的目標平臺選擇正確的代理程式規格：

   * 如果您想要在適用于 Linux 容器的 platform amd64 中建立模組，請選擇 **ubuntu-16.04**

   * 如果您想要在 amd64 平台中建置適用於 Windows 1809 容器的模組，您必須[在 Windows 上設定自我裝載的代理程式](/azure/devops/pipelines/agents/v2-windows) \(英文\)。

   * 如果您想要在適用于 Linux 容器的 platform arm32v7 或 arm64 中建立模組，您必須在 [linux 上設定自我裝載代理程式](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)。

    ![設定組建代理程式規格](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. 您的管線已預先設定名為 **代理程式作業 1** 的作業。 選取加號 () 將 **+** 四個工作新增至作業： **Azure IoT Edge** 兩次、只 **複製** 檔案一次，然後 **發行組建** 成品一次。 搜尋每項工作，並將滑鼠停留在工作名稱上方，以查看 [ **加入** ] 按鈕。

   ![新增 Azure IoT Edge 工作](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   新增全部四個工作時，您的代理程式作業看起來如下列範例所示：

   ![組建管線中的四個工作](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. 選取第一個 [Azure IoT Edge] 工作來編輯它。 此工作會使用您指定的目標平臺，建立解決方案中的所有模組。 使用下列值編輯工作：

    | 參數 | 說明 |
    | --- | --- |
    | 顯示名稱 | 當動作欄位變更時，顯示名稱會自動更新。 |
    | 動作 | 選取 **組建模組映射**。 |
    | 檔案上的 .template.js | 選取省略符號 (**...**) 並瀏覽至存放庫中的 **deployment.template.json** 檔案，其包含您的 IoT Edge 解決方案。 |
    | 預設平臺 | 根據您的目標 IoT Edge 裝置，為您的模組選取適當的作業系統。 |
    | 輸出變數 | 提供要與檔案 deployment.js路徑（例如 **edge**）產生關聯的參考名稱。 |

   這些設定會使用檔案中定義的映射存放庫和標籤， `module.json` 來命名和標記模組映射。 **組建模組映射** 也有助於將變數取代為您在檔案中定義的確切值 `module.json` 。 在 Visual Studio 或 Visual Studio Code 中，您會在檔案中指定實際值 `.env` 。 在 Azure Pipelines 中，您可以設定 [ **管線變數** ] 索引標籤上的值。選取 [管線編輯器] 功能表上的 [ **變數** ] 索引標籤，並設定名稱和值，如下所示：

    * **ACR_ADDRESS**：您的 Azure Container Registry **登入伺服器** 值。 您可以在 Azure 入口網站中，從容器登錄的概觀頁面擷取登入伺服器。

    如果您的專案中有其他變數，您可以在此索引標籤上指定名稱和值。 **組建模組映射** 只會辨識採用 `${VARIABLE}` 格式的變數。 請務必在您的檔案中使用此格式 `**/module.json` 。

8. 選取第二個 [Azure IoT Edge] 工作來編輯它。 此工作會將所有模組映像推送到您選取的容器登錄。

    | 參數 | 說明 |
    | --- | --- |
    | 顯示名稱 | 當動作欄位變更時，顯示名稱會自動更新。 |
    | 動作 | 選取 **推送模組映射**。 |
    | Container registry 類型 | 使用預設類型： `Azure Container Registry` 。 |
    | Azure 訂用帳戶 | 選擇您的訂用帳戶。 |
    | Azure Container Registry | 選取您用來儲存模組映像的容器登錄類型。 表單會根據您所選擇的登錄類型而變更。 如果您選擇 [Azure Container Registry]，請使用下拉式清單來選取 Azure 訂用帳戶和容器登錄的名稱。 如果您選擇 [泛型容器登錄]，請選取 [新增] 以建立登錄服務連線。 |
    | 檔案上的 .template.js | 選取省略符號 (**...**) 並瀏覽至存放庫中的 **deployment.template.json** 檔案，其包含您的 IoT Edge 解決方案。 |
    | 預設平臺 | 根據您的目標 IoT Edge 裝置，為您的模組選取適當的作業系統。 |
    | 將登錄認證新增至部署資訊清單 | 指定 true 以新增將 docker 映射推送至部署資訊清單的登錄認證。 |

   如果您有多個容器登錄來裝載模組映射，您需要複製此工作、選取不同的容器登錄，並使用 [ **Advanced** settings] 中的 [**略過模組 (s])** ，以略過不是此特定登錄的映射。

9. 選取 [ **複製** 檔案] 工作來編輯它。 使用此工作可將檔案複製到成品臨時目錄。

    | 參數 | 說明 |
    | --- | --- |
    | 顯示名稱 | 使用預設名稱或自訂 |
    | 源資料夾 | 包含要複製之檔案的資料夾。 |
    | 目錄 | 新增兩行： `deployment.template.json` 和 `**/module.json` 。 這兩個檔案可作為輸入，以產生 IoT Edge 部署資訊清單。 |
    | 目的檔案夾 | 指定變數 `$(Build.ArtifactStagingDirectory)` 。 請參閱 [組建變數](/azure/devops/pipelines/build/variables#build-variables) 以瞭解描述。 |

10. 選取 [發行組建成品] 工作來編輯它。 提供工作的構件臨時目錄路徑，讓路徑可以發佈至發行管線。

    | 參數 | 說明 |
    | --- | --- |
    | 顯示名稱 | 使用預設名稱或自訂。 |
    | 要發佈的路徑 | 指定變數 `$(Build.ArtifactStagingDirectory)` 。 若要深入瞭解，請參閱 [建立變數](/azure/devops/pipelines/build/variables#build-variables) 。 |
    | 成品名稱 | 使用預設名稱： **drop** |
    | 成品發行位置 | 使用預設位置： **Azure Pipelines** |

11. 開啟 [觸發程序] 索引標籤，然後核取 [啟用持續整合] 方塊。 請確定已納入包含程式碼的分支。

    ![開啟持續整合觸發程序](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. 從 [**儲存 & 佇列**] 下拉式清單中選取 [**儲存**]。

此管線現在已設定為會在將新程式碼推送至存放庫時自動執行。 最後一個工作「發行管線成品」則會觸發發行管線。 繼續進行下一節以建置發行管線。

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>如果您想要在管線中使用 **分層式部署** ，Azure DevOps 中的 Azure IoT Edge 工作尚未支援分層部署。
>
>不過，您可以使用 [Azure DevOps 中的 Azure CLI](/azure/devops/pipelines/tasks/deploy/azure-cli) 工作，將部署建立為分層部署。 針對 **內嵌腳本** 值，您可以使用 [az iot edge deployment create 命令](/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment)：
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>後續步驟

* [適用于 IoT Edge Azure DevOps Starter 中的](how-to-devops-starter.md)IoT Edge DevOps 最佳做法範例
* 在[了解單一裝置或大規模的 IoT Edge 部署](module-deployment-monitoring.md)中，了解 IoT Edge 部署。
* 逐步解說[大規模部署和監視 IoT Edge 模組](how-to-deploy-at-scale.md)中，用來建立、更新或刪除部署的步驟。