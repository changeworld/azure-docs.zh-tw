---
title: 持續整合和持續部署到 Azure IoT Edge 裝置-Azure IoT Edge
description: 使用 YAML-Azure IoT Edge 搭配 Azure DevOps 來設定持續整合和持續部署 Azure Pipelines
author: shizn
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d29a5a6d0d4745655ce5b6d0cead3eaba77ed423
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91281621"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>持續整合和持續部署到 Azure IoT Edge 裝置

您可以使用 Azure Pipelines 中內建的 Azure IoT Edge 工作，輕鬆地搭配您的 Azure IoT Edge 應用程式採用 DevOps。 本文將示範如何使用 Azure Pipelines 的持續整合與持續部署功能，快速且有效率地使用 YAML 來建立、測試及部署應用程式，並將其部署至您的 Azure IoT Edge。 或者，您也可以 [使用傳統編輯器](how-to-continuous-integration-continuous-deployment-classic.md)。

![圖表 - 開發和生產的 CI 和 CD 分支](./media/how-to-continuous-integration-continuous-deployment/model.png)

在本文中，您將瞭解如何使用 Azure Pipelines 的內建 [Azure IoT Edge](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/azure-iot-edge) 工作，為您的 IoT Edge 解決方案建立組建和發行管線。 每個新增至管線的 Azure IoT Edge 工作都會執行下列四個動作的其中一個：

 | 動作 | 描述 |
 | --- | --- |
 | 組建模組映射 | 取得您的 IoT Edge 解決方案程式碼，並建立容器映射。|
 | 推送模組映射 | 將模組映射推送到您指定的容器登錄。 |
 | 產生部署資訊清單 | 會取得檔案和變數 deployment.template.js，然後產生最終的 IoT Edge 部署資訊清單檔。 |
 | 部署到 IoT Edge 裝置 | 建立 IoT Edge 部署至一或多個 IoT Edge 裝置。 |

除非另有說明，否則本文中的程式不會探索透過工作參數提供的所有功能。 如需詳細資訊，請參閱下列：

* [工作版本](https://docs.microsoft.com/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=classic#task-versions)
* **Advanced** -如果適用，請指定您不想要建立的模組。
* [控制項選項](https://docs.microsoft.com/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=classic#task-control-options)
* [環境變數](https://docs.microsoft.com/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#environment-variables)
* [輸出變數](https://docs.microsoft.com/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#use-output-variables-from-tasks)

## <a name="prerequisites"></a>必要條件

* Azure Repos 存放庫。 如果您沒有存放庫，可以[在專案中建立新的 Git 存放庫](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav) \(英文\)。 針對本文，我們建立了名為 **IoTEdgeRepo** 的存放庫。
* 已認可並推送至您存放庫的 IoT Edge 解決方案。 若要建立用於測試本文的新範例解決方案，請遵循[在 Visual Studio Code 中針對模組進行開發與偵錯](how-to-vs-code-develop-module.md)，或[在 Visual Studio Code 中對 C# 模組進行開發與偵錯](how-to-visual-studio-develop-csharp-module.md)中的步驟。 在本文中，我們在存放庫中建立了名為 **IoTEdgeSolution**的解決方案，其中包含名為 **filtermodule**的模組程式碼。

   針對本文，您只需要在 Visual Studio Code 或 Visual Studio 中由 IoT Edge 範本所建立的解決方案資料夾。 在進行之前，您並不需要針對此程式碼進行建置、推送、部署或偵錯。 您將在 Azure Pipelines 中設定這些處理常式。

   若您是建立新的解決方案，請先在本機複製您的存放庫。 然後，當您建立解決方案時，您可以選擇直接在存放庫資料夾中建立它。 您可以輕鬆地從該處對新檔案進行認可和推送。

* 可對它推送模組映像的容器登錄。 您可以使用 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 或協力廠商登錄。
* 使用中的 Azure [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md) ，至少有兩個 IoT Edge 裝置可用來測試個別的測試和生產部署階段。 您可以遵循快速入門文章來在 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 上建立 IoT Edge 裝置

如需使用 Azure Repos 的詳細資訊，請參閱 [Visual Studio 和 Azure Repos 共用您的程式碼](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>建立持續整合的組建管線

在本節中，您會建立新的建置管線。 您可以將管線設定為當您將任何變更簽入範例 IoT Edge 方案，併發行組建記錄檔時自動執行。

1. 登入您的 Azure DevOps 組織 (`https://dev.azure.com/{your organization}`) 並開啟包含您 IoT Edge 解決方案存放庫的專案。

   ![開啟您的 DevOps 專案](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. 從專案的左窗格功能表中，選取 [ **管線**]。 選取頁面中央的 [ **建立管線** ]。 或者，如果您已經有組建管線，請選取右上方的 [ **新增管線** ] 按鈕。

    ![使用 [新增管線] 按鈕建立新的組建管線](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. 在 [**您的程式碼在哪裡？** ] 頁面上，選取 [ **Azure Repos Git `YAML` **]。 如果您想要使用傳統編輯器來建立專案的組建管線，請參閱 [傳統編輯器指南](how-to-continuous-integration-continuous-deployment-classic.md)。

4. 選取您要建立管線的儲存機制。

    ![選取組建管線的儲存機制](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. 在 [ **設定您的管線** ] 頁面上，選取 [ **入門管線**]。 如果您有想要用來建立此管線的預先存在 Azure Pipelines YAML 檔，您可以選取 **現有的 AZURE PIPELINES YAML** 檔，並在存放庫中提供檔案的分支和路徑。

    ![選取入門管線或現有的 Azure Pipelines YAML 檔，以開始您的組建管線](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. 在 [ **審核您的管線 YAML** ] 頁面上，您可以按一下預設名稱 `azure-pipelines.yml` 來重新命名管線的設定檔。

   選取 [**顯示**小幫手] **Tasks**以開啟 [工作] 調色板。

    ![選取 [顯示小幫手] 以開啟 [工作] 選擇區](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. 若要加入工作，請將游標放在 YAML 的結尾，或您想要加入工作指示的任何位置。 搜尋並選取 [ **Azure IoT Edge**]。 填寫工作的參數，如下所示。 然後，選取 [ **新增**]。

   | 參數 | 描述 |
   | --- | --- |
   | 動作 | 選取 **組建模組映射**。 |
   | 檔案上的 .template.js | 提供存放庫中的 **deployment.template.js** 檔案路徑，其中包含您的 IoT Edge 解決方案。 |
   | 預設平臺 | 根據您的目標 IoT Edge 裝置，為您的模組選取適當的作業系統。 |

    ![使用工作面板將工作新增至管線](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > 加入每個工作之後，編輯器會自動反白顯示新增的行。 為了避免意外覆寫，請取消選取這些程式程式碼，並為下一個工作提供新的空間，然後再新增其他工作。

8. 重複此程式，使用下列參數新增三個工作：

   * 工作： **Azure IoT Edge**

       | 參數 | 描述 |
       | --- | --- |
       | 動作 | 選取 **推送模組映射**。 |
       | Container registry 類型 | 使用預設類型： **Azure Container Registry**。 |
       | Azure 訂用帳戶 | 選取您的訂用帳戶。 |
       | Azure Container Registry | 選擇您要用於管線的登錄。 |
       | 檔案上的 .template.js | 提供存放庫中的 **deployment.template.js** 檔案路徑，其中包含您的 IoT Edge 解決方案。 |
       | 預設平臺 | 根據您的目標 IoT Edge 裝置，為您的模組選取適當的作業系統。 |

   * 工作：**複製**檔案

       | 參數 | 描述 |
       | --- | --- |
       | 來源資料夾 | 要複製的來源資料夾。 空白是存放庫的根目錄。 如果檔案不在存放庫中，請使用變數。 範例： `$(agent.builddirectory)`.
       | 目錄 | 新增兩行： `deployment.template.json` 和 `**/module.json` 。 |
       | 目的檔案夾 | 指定變數 `$(Build.ArtifactStagingDirectory)` 。 請參閱 [組建變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) 以瞭解描述。 |

   * 工作：**發行組建**成品

       | 參數 | 描述 |
       | --- | --- |
       | 要發佈的路徑 | 指定變數 `$(Build.ArtifactStagingDirectory)` 。 請參閱 [組建變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) 以瞭解描述。 |
       | 成品名稱 | 指定預設名稱： `drop` |
       | 成品發行位置 | 使用預設位置： `Azure Pipelines` |

9. 從右上方的 [**儲存並執行**] 下拉式清單中選取 [**儲存**]。

10. 針對您的 YAML 管線，預設會啟用持續整合的觸發程式。 如果您想要編輯這些設定，請選取您的管線，然後按一下右上方的 [ **編輯** ]。 在右上方的 [**執行**] 按鈕旁邊選取 [**其他動作**]，然後移至 [**觸發**程式]。 **持續整合** 會在您的管線名稱下顯示為已啟用。 如果您想要查看觸發程式的詳細資料，請核取 [ **從這裡覆寫 YAML 持續整合觸發** 程式] 方塊。

    ![若要檢查管線的觸發程式設定，請在 [其他動作] 下查看觸發程式](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

繼續進行下一節以建置發行管線。

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>後續步驟

* [適用于 IoT Edge Azure DevOps Starter 中的](how-to-devops-starter.md)IoT Edge DevOps 最佳做法範例
* 在[了解單一裝置或大規模的 IoT Edge 部署](module-deployment-monitoring.md)中，了解 IoT Edge 部署。
* 逐步解說[大規模部署和監視 IoT Edge 模組](how-to-deploy-at-scale.md)中，用來建立、更新或刪除部署的步驟。
