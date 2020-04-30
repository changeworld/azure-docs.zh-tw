---
title: 持續整合 & 持續部署-Azure IoT Edge
description: 設定持續整合和持續部署 - Azure IoT Edge 搭配 Azure DevOps、Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9a653d13137a3067bfaf51c64c09454a08783e31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131403"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Azure IoT Edge 的持續整合與持續部署

您可以使用 Azure Pipelines 中內建的 Azure IoT Edge 工作，輕鬆地搭配您的 Azure IoT Edge 應用程式採用 DevOps。 此文章示範如何使用 Azure Pipelines 的持續整合與持續部署功能，快速且有效率地建置、測試應用程式，並且將其部署到您的 Azure IoT Edge。

![圖表 - 開發和生產的 CI 和 CD 分支](./media/how-to-ci-cd/cd.png)

在本文中，您會了解如何使用 Azure Pipelines 的內建 Azure IoT Edge 工作，為您的 IoT Edge 解決方案建立兩個管線。 Azure IoT Edge 工作中可以使用四個動作。

* **Azure IoT Edge 組建模組映射**會取得您的 IoT Edge 解決方案程式碼，並建立容器映射。
* **Azure IoT Edge-推送模組映射**會將模組映射推送至您指定的容器登錄。
* **Azure IoT Edge-產生部署資訊清單**會接受 deployment. template json 檔案和變數，然後產生最後的 IoT Edge 部署資訊清單檔案。
* **Azure IoT Edge 部署至 IoT Edge 裝置**有助於建立單一/多個 IoT Edge 裝置的 IoT Edge 部署。

## <a name="prerequisites"></a>先決條件

* Azure Repos 存放庫。 如果您沒有存放庫，可以[在專案中建立新的 Git 存放庫](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav) \(英文\)。
* 已認可並推送至您存放庫的 IoT Edge 解決方案。 若要建立用於測試本文的新範例解決方案，請遵循[在 Visual Studio Code 中針對模組進行開發與偵錯](how-to-vs-code-develop-module.md)，或[在 Visual Studio Code 中對 C# 模組進行開發與偵錯](how-to-visual-studio-develop-csharp-module.md)中的步驟。

   針對本文，您只需要在 Visual Studio Code 或 Visual Studio 中由 IoT Edge 範本所建立的解決方案資料夾。 在進行之前，您並不需要針對此程式碼進行建置、推送、部署或偵錯。 您將在 Azure Pipelines 中設定那些程序。

   若您是建立新的解決方案，請先在本機複製您的存放庫。 然後，當您建立解決方案時，您可以選擇直接在存放庫資料夾中建立它。 您可以輕鬆地從該處對新檔案進行認可和推送。

* 可對它推送模組映像的容器登錄。 您可以使用 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 或協力廠商登錄。
* 至少具有 IoT Edge 裝置的作用中 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)，以用來測試個別的測試和生產部署階段。 您可以遵循快速入門文章來在 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 上建立 IoT Edge 裝置

如需使用 Azure Repos 的詳細資訊，請參閱[共用程式碼與 Visual Studio 和 Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>設定持續整合

在本節中，您會建立新的建置管線。 設定管線以在將任何變更簽入範例 IoT Edge 解決方案時自動執行，並發行組建記錄。

>[!NOTE]
>本文會使用 Azure DevOps 視覺化設計工具。 在遵循本節中的步驟之前，請關閉新的 YAML 管線建立體驗的預覽功能。
>
>1. 在 Azure DevOps 中，選取您的設定檔圖示，然後選取 [預覽功能]****。
>2. 將 [新的 YAML 管線建立體驗]**** 關閉。
>
>如需詳細資訊，請參閱[建立建置管線](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline) \(英文\)。

1. 登入您的 Azure DevOps 組織（**HTTPs\/：/dev.azure.com/{your 組織}/**），然後開啟包含您 IoT Edge 解決方案存放庫的專案。

   針對本文，我們建立了名為 **IoTEdgeRepo** 的存放庫。 該存放庫包含 **IoTEdgeSolution**，其中包含名為 **filtermodule** 的模組的程式碼。

   ![開啟您的 DevOps 專案](./media/how-to-ci-cd/init-project.png)

2. 瀏覽至專案中的 Azure Pipelines。 開啟 [組建]**** 索引標籤，然後選取 [新增管線]****。 或者，如果您已經有建置管線，請選取 [新增]**** 按鈕。 然後選擇 [新增建置管線]****。

    ![建立新的組建管線](./media/how-to-ci-cd/add-new-build.png)

3. 遵循提示以建立管線。

   1. 提供新建置管線的來源資訊。 選取 [Azure Repos Git]**** 作為來源，然後選取 IoT Edge 解決方案程式碼所在的專案、存放庫及分支。 然後選取 [**繼續**]。

      ![選取您的管線來源](./media/how-to-ci-cd/pipeline-source.png)

   2. 選取 [空白作業]****，而非範本。

      ![以空白的處理序開始](./media/how-to-ci-cd/start-with-empty.png)

4. 建立管線之後，系統會將您引導至管線編輯器。 在管線描述中，根據目標平台選擇正確的代理程式集區：

   * 如果您想要在 amd64 平台中建置適用於 Linux 容器的模組，請選擇 [裝載的 Ubuntu 1604]****

   * 如果您想要在 amd64 平台中建置適用於 Windows 1809 容器的模組，您必須[在 Windows 上設定自我裝載的代理程式](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts) \(英文\)。

   * 如果您想要在 platform arm32v7 或 arm64 for Linux 容器中建立模組，您必須在[linux 上設定自我裝載的代理程式](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/)。

     ![設定組建代理程式集區](./media/how-to-ci-cd/configure-env.png)

5. 您的管線已預先設定名為**代理程式作業 1** 的作業。 **+** 選取加號（），將三個工作新增至作業： **Azure IoT Edge**兩次、**複製**檔案一次，並**發行組建**成品一次。 (將滑鼠暫留在每個工作的名稱上以顯示 [新增]**** 按鈕)。

   ![新增 Azure IoT Edge 工作](./media/how-to-ci-cd/add-iot-edge-task.png)

   新增所有四個工作之後，您的代理程式作業看起來會像下列範例：

   ![建置管線中的三個工作](./media/how-to-ci-cd/add-tasks.png)

6. 選取第一個 [Azure IoT Edge]**** 工作來編輯它。 此工作會使用您指定的目標平臺，建立方案中的所有模組。

   * **顯示名稱**：接受預設的**Azure IoT Edge 組建模組映射**。
   * **動作**：接受預設的**組建模組映射**。
   * **. template. json**檔案：選取省略號（**...**），然後流覽至包含您 IoT Edge 解決方案的存放庫中的**deployment. template. json**檔案。
   * **預設平臺**：根據您的目標 IoT Edge 裝置，為您的模組選取適當的平臺。
   * **輸出變數**：輸出變數包含參考名稱，可讓您用來設定將產生部署 json 檔案的檔案路徑。 將參考名稱設定為易記的名稱，例如 **edge**。

7. 選取第二個 [Azure IoT Edge]**** 工作來編輯它。 此工作會將所有模組映像推送到您選取的容器登錄。

   * **顯示名稱**：當動作欄位變更時，顯示名稱會自動更新。
   * **動作**：使用下拉式清單來選取 [**推送模組映射**]。
   * **容器登錄類型**：選取您用來儲存模組映射的容器登錄類型。 表單會根據您所選擇的登錄類型而變更。 如果您選擇 [Azure Container Registry]****，請使用下拉式清單來選取 Azure 訂用帳戶和容器登錄的名稱。 如果您選擇 [泛型容器登錄]****，請選取 [新增]**** 以建立登錄服務連線。
   * **. template. json**檔案：選取省略號（**...**），然後流覽至包含您 IoT Edge 解決方案的存放庫中的**deployment. template. json**檔案。
   * **預設平臺**：選取與您的組建模組映射相同的平臺。

   如果您有多個容器登錄可裝載您的模組映像，您需要複製此工作、選取不同的容器登錄，然後使用進階設定中的 [略過模組]****，略過不適用於此特定登錄的映像。

8. 選取 [**複製**檔案] 工作來編輯它。 使用此工作可將檔案複製到成品臨時目錄。

   * **顯示名稱**：將檔案複製到： Drop folder。
   * **內容**：將兩行放在這個區段`deployment.template.json`中`**/module.json`，和。 這兩種類型的檔案是用來產生 IoT Edge 部署資訊清單的輸入。 需要複製到成品執行資料夾，並針對發行管線發行。
   * **目的檔案夾**：放入變數`$(Build.ArtifactStagingDirectory)`。 請參閱[組建變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables)以瞭解描述。

9. 選取 [發行組建成品]**** 工作來編輯它。 提供工作的成品臨時目錄路徑，以便將路徑發佈至發行管線。

   * **顯示名稱**： Publish 成品： drop。
   * **要發行的路徑**：放入`$(Build.ArtifactStagingDirectory)`變數。 請參閱[組建變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables)以瞭解描述。
   * 成品**名稱**： drop。
   * 成品**發佈位置**： Azure Pipelines。

10. 開啟 [觸發程序]**** 索引標籤，然後核取 [啟用持續整合]**** 方塊。 請確定已納入包含程式碼的分支。

    ![開啟持續整合觸發程序](./media/how-to-ci-cd/configure-trigger.png)

11. 使用 [儲存]**** 按鈕儲存新的組建管線。

此管線現在已設定為會在將新程式碼推送至存放庫時自動執行。 最後一個工作「發行管線成品」則會觸發發行管線。 繼續進行下一節以建置發行管線。

## <a name="configure-continuous-deployment"></a>設定連續部署

在本節中，您會建立發行管線，並將其設定為會在建置管線置放成品時自動執行，且會在 Azure Pipelines 中顯示部署記錄。

建立新的管線，並新增新的階段

1. 在 [發行]**** 索引標籤中，選擇 [+ 新增管線]****。 或者，如果您已經有發行管線，請選擇 [+ 新增]**** 按鈕，然後選取 [+ 新增發行管線]****。  

    ![新增發行管線](./media/how-to-ci-cd/add-release-pipeline.png)

2. 當系統提示您選取範本時，選擇以 [空白作業]**** 開始。

    ![從空白作業開始](./media/how-to-ci-cd/start-with-empty-job.png)

3. 您的新發行管線會以單一階段初始化，其名為**階段 1**。 將階段1重新命名為**dev** ，並將它視為測試環境。 通常，持續部署管線有多個階段，包括**開發**、**預備**和**生產**。您可以根據您的 DevOps 實務來建立更多。 重新命名後，請關閉階段詳細資料視窗。

4. 將版本連結到由建置管線發佈的組建成品。 在成品區域中按一下 [新增]****。

   ![新增成品](./media/how-to-ci-cd/add-artifacts.png)  

5. 在 [新增成品]**** 頁面中，選取來源類型 [組建]****。 接著選取您所建立的專案和建置管線。 然後選取 [**新增**]。

   ![新增組建成品](./media/how-to-ci-cd/add-an-artifact.png)

6. 開啟成品觸發程序，並選取切換以啟用持續部署觸發程序。 現在，每次有新的可用組建時，系統都會建立新的版本。

   ![設定連續部署觸發程序](./media/how-to-ci-cd/add-a-trigger.png)

7. **開發**階段已預先設定一個作業和零個工作。 從 [管線] 功能表選取 [工作] **，然後選擇**[**開發**] 階段。  選取作業和工作計數，以設定這個階段中的工作。

    ![設定開發工作](./media/how-to-ci-cd/view-stage-tasks.png)

8. 在**開發**階段中，您應該會看到預設的**代理程式作業**。 您可以設定有關代理程式作業的詳細資料，但部署工作並不會區分平台，因此您可以使用 [代理程式集區]**** 中的 [裝載的 VS2017]**** 或 [裝載的 Ubuntu 1604]**** (或您自己管理的任何其他代理程式)。

9. 選取加號（**+**）以加入兩個工作。 搜尋並新增**Azure IoT Edge**兩次。

    ![為 dev 新增工作](./media/how-to-ci-cd/add-task-qa.png)

10. 選取第一個**Azure IoT Edge**工作，並使用下列值進行設定：

    * **顯示名稱**：當動作欄位變更時，顯示名稱會自動更新。
    * **動作**：使用下拉式清單來選取 [**產生部署資訊清單**]。 變更動作值時，系統也會更新工作顯示名稱以使其相符。
    * **. template. json**檔案：放置路徑`$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`。 路徑是從組建管線發行。
    * **預設平臺**：建立模組映射時，請選擇相同的值。
    * **輸出路徑**：放置路徑`$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`。 此路徑是最後的 IoT Edge 部署資訊清單檔案。

    這些設定可協助取代檔案中`deployment.template.json`的模組映射 url。 **產生部署資訊清單**也有助於將變數取代為您在檔案中`deployment.template.json`定義的確切值。 在 VS/VS Code 中，您會在檔案中指定實際`.env`的值。 在 Azure Pipelines 中，您可以設定 [發行管線變數] 索引標籤中的值。移至 [變數] 索引標籤，並依照下列方式設定名稱和值。

    * **ACR_ADDRESS**：您的 Azure Container Registry 位址。
    * **ACR_PASSWORD**：您的 Azure Container Registry 密碼。
    * **ACR_USER**：您的 Azure Container Registry 使用者名稱。

    如果您的專案中有其他變數，您可以在此索引標籤中指定名稱和值。[**產生部署資訊清單**] 只能辨識 [類別] `${VARIABLE}`中的變數，請確定您在檔案中`*.template.json`使用此功能。

    ![設定發行管線的變數](./media/how-to-ci-cd/configure-variables.png)

11. 選取第二個**Azure IoT Edge**工作，並使用下列值進行設定：

    * **顯示名稱**：當動作欄位變更時，顯示名稱會自動更新。
    * **動作**：使用下拉式清單選取 [**部署至 IoT Edge 裝置**]。 變更動作值時，系統也會更新工作顯示名稱以使其相符。
    * **Azure 訂**用帳戶：選取包含您 IoT 中樞的訂用帳戶。
    * **IoT 中樞名稱**：選取您的 IoT 中樞。
    * **選擇單一/多個裝置**：選擇您要將發行管線部署到一個或多個裝置。
      * 如果部署到單一裝置，請輸入 **IoT Edge 裝置識別碼**。
      * 如果要部署到多個裝置，請指定裝置**目標條件**。 [目標條件] 是一個篩選器，以符合 IoT 中樞中的一組 IoT Edge 裝置。 如果您想使用裝置標記作為條件，您必須使用 IoT 中樞裝置對應項更新對應的裝置標記。 在進階設定中更新 [IoT Edge 部署識別碼]**** 和 [IoT Edge 部署優先順序]****。 如需為多個裝置建立部署的詳細資訊，請參閱[了解 IoT Edge 自動部署](module-deployment-monitoring.md)。
    * 展開 [高級設定]，選取 [ **IoT Edge 部署識別碼**] `$(System.TeamProject)-$(Release.EnvironmentName)`，放入變數。 這會將專案和發行名稱對應至您的 IoT Edge 部署識別碼。

12. 選取 [儲存]**** 以將變更儲存至新的發行管線。 從功能表中選取 [管線]**** 以返回管線檢視。

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>使用組建和發行管線驗證 IoT Edge CI/CD

若要觸發組建作業，您可以推送認可至原始程式碼存放庫，或是手動觸發。 在本節中，您會手動觸發 CI/CD 管線以測試它是否有效。 接著驗證部署是否成功。

1. 瀏覽至您在本文開頭所建立的建置管線。

2. 您可以選取 [佇列]**** 按鈕來觸發組建管線中的組建作業，如下列螢幕擷取畫面所示。

    ![手動觸發](./media/how-to-ci-cd/manual-trigger.png)

3. 選取建置作業以查看其進度。 如果組建管線已順利完成，就會觸發發行至**開發**階段。

    ![組建記錄](./media/how-to-ci-cd/build-logs.png)

4. 成功的**開發**版會建立以 IoT Edge 裝置為目標的 IoT Edge 部署。

    ![發行至 dev](./media/how-to-ci-cd/pending-approval.png)

5. 按一下 [**開發**階段] 以查看發行記錄。

    ![發行記錄](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>後續步驟

* IoT Edge[適用于 IoT Edge Azure DevOps 專案中的](how-to-devops-project.md)DevOps 最佳作法範例
* 在[了解單一裝置或大規模的 IoT Edge 部署](module-deployment-monitoring.md)中，了解 IoT Edge 部署。
* 逐步解說[大規模部署和監視 IoT Edge 模組](how-to-deploy-at-scale.md)中，用來建立、更新或刪除部署的步驟。
