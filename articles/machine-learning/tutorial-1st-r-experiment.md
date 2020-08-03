---
title: 教學課程：使用 R 建立機器學習模型 (預覽)
titleSuffix: Azure Machine Learning
description: 在本教學課程中，您將使用 Azure Machine Learning R SDK 建立羅吉斯迴歸模型，以預測車禍致命的可能性。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 53b1c22e93c342373cae2bbb14958f4810a79630
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092315"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model-preview"></a>教學課程：使用 R 建立機器學習模型 (預覽)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!IMPORTANT]
> Azure Machine Learning R SDK 目前處於公開預覽狀態。
> 此預覽版本會在沒有服務等級協定的情況下提供，不建議用於實際執行工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在本教學課程中，您將使用 Azure Machine Learning R SDK (預覽) 建立羅吉斯迴歸模型，以預測車禍致命的可能性。 您將了解 Azure Machine Learning 雲端資源如何與 R 搭配使用以提供可調整的環境，以進行模型的定型和部署。  

在本教學課程中，您會執行下列工作：
> [!div class="checklist"]
> * 建立 Azure Machine Learning 工作區
> * 使用執行本教學課程所需的檔案，將筆記本資料夾複製到您的工作區
> * 從您的工作區開啟 RStudio
> * 載入資料並準備進行訓練
> * 將資料上傳至資料存放區，使其可用於遠端定型
> * 建立計算資源，以便從遠端訓練模型
> * 訓練 `caret` 模型以預測致命的可能性
> * 部署預測端點
> * 以 R 測試模型

如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。


## <a name="create-a-workspace"></a>建立工作區

Azure Machine Learning 工作區是雲端中您用來實驗、定型及部署機器學習模型的基礎資源。 工作區可將您的 Azure 訂用帳戶和資源群組與服務中容易使用的物件結合。 

您透過 Azure 入口網站建立工作區 (管理 Azure 資源的 Web 型主控台)。 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> 記下您的**工作區**和**訂用帳戶**。 您會需要這些項目，以確保您在正確位置建立實驗。 


## <a name="clone-a-notebook-folder"></a><a name="azure"></a>複製 Notebook 資料夾

此範例會使用您工作區中的雲端 Notebook 伺服器，讓您擁有免安裝和預先設定的體驗。 如果您想要控制您的環境、套件和相依性，[請使用您自己的環境](https://azure.github.io/azureml-sdk-for-r/articles/installation.html)。

您在 Azure Machine Learning Studio 中完成下列實驗設定及執行步驟，該頁面是統一的介面，為所有技能等級的資料科學從業人員，提供執行資料科學情節的機器學習工具。

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)。

1. 選取訂用帳戶與您建立的工作區。

1. 選取左側的 [筆記本]。

1. 開啟 **samples** 資料夾。

1. 開啟 [R] 資料夾。

1. 開啟其上有版本號碼的資料夾。  此號碼代表 R SDK 的目前版本。

1. 選取 **vignettes** 資料夾右側的 **"..."** ，然後選取 [複製]。

    ![複製資料夾](media/tutorial-1st-r-experiment/clone-folder.png)

1. 資料夾清單隨即顯示，其中顯示每位存取工作區的使用者。  選取您的資料夾，以將 **vignettes** 資料夾複製到該處。

## <a name="open-rstudio"></a><a name="open"></a>開啟 RStudio

在計算執行個體或 Notebook VM 上使用 RStudio 來執行本教學課程。  

1. 選取左側的 [計算]。

1. 新增計算資源 (如果尚未存在的話)。

1. 計算執行後，請使用 [RStudio] 連結來開啟 RStudio。

1. 在 RStudio 中，您的 [vignettes] 資料夾會在 [使用者] 的下面幾層，位於右下方的 [檔案] 區段中。  在 [vignettes] 底下選取 [train-and-deploy-to-aci] 資料夾，以尋找本教學課程中所需的檔案。

> [!Important]
> 本文的其餘部分會包含您在 *train-and-deploy-to-aci.Rmd* 檔案中看到的相同內容。 如果您曾使用過 RMarkdown，則可以使用該檔案中的程式碼。  或者，您也可以從該處複製/貼上程式碼片段，或從本文複製/貼到 R 指令碼或命令列中。  


## <a name="set-up-your-development-environment"></a>設定開發環境
在本教學課程中，開發工作的設定包含下列動作：

* 安裝必要的套件
* 連線至工作區，以便讓計算執行個體能夠與遠端資源進行通訊
* 建立實驗以追蹤您的執行
* 建立要用於定型的遠端計算目標

### <a name="install-required-packages"></a>安裝必要的套件

 * 從 CRAN 安裝最新版本。

    ```R
    # install the latest version from CRAN
    install.packages("azuremlsdk")
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```
    
* 或從 GitHub 安裝開發版本。

    ```R
    # or install the development version from GitHub
    remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```

現在繼續操作並匯入 **azuremlsdk** 套件。

```R
library(azuremlsdk)
```

定型和評分指令碼 (`accidents.R` 和 `accident_predict.R`) 有一些額外的相依性。 如果您預計要在本機執行這些指令碼，請確定您也具有這些必要套件。

### <a name="load-your-workspace"></a>載入您的工作區
從現有的工作區具現化工作區物件。 下列程式碼會從 **config.json** 檔案載入工作區詳細資料。 您也可以使用 [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html) 來擷取工作區。

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>建立實驗
Azure ML 實驗會追蹤一組執行，這通常來自於相同的定型指令碼。 請建立一項實驗，用以追蹤以意外事故資料定型插入點模型的執行。

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>建立計算目標
資料科學家可藉由使用 Azure Machine Learning Compute (AmlCompute) 這項受控服務，在 Azure 虛擬機器叢集上將機器學習模型定型。 範例包括具有 GPU 支援的 VM。 在本教學課程中，您會建立單一節點 AmlCompute 叢集作為定型環境。 如果您的工作區中還沒有計算叢集，下列程式碼將會為您建立計算叢集。

您可能需要等候幾分鐘的時間，計算叢集才會佈建 (如果尚未存在的話)。

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>準備資料以進行定型
本教學課程使用的資料來自美國[國家公路交通安全管理局](https://cdan.nhtsa.gov/tsftables/tsfar.htm) (特別感謝 [Mary C. Meyer 和 Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm))。
此資料集包含美國境內超過 25.000 起車禍的資料，以及可供您用來預測傷亡可能性的變數。 首先，請將資料匯入 R 中，並將其轉換成新的資料框架 `accidents` 以進行分析，然後再將其匯出至 `Rdata` 檔案。

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>將資料上傳至資料存放區
將資料上傳至雲端，讓您的遠端定型環境可加以存取。 每個 Azure Machine Learning 工作區都附有預設的資料存放區，其中儲存連結至工作區的儲存體帳戶中佈建的 Azure Blob 容器所適用的連線資訊。 下列程式碼會將您先前建立的意外事故資料上傳至該資料存放區。

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>將模型定型

在本教學課程中，請使用您的遠端計算叢集，對您上傳的資料配適羅吉斯迴歸模型。 若要提交作業，您必須：

* 建立定型指令碼
* 建立估計工具
* 提交工作

### <a name="prepare-the-training-script"></a>建立定型指令碼
在與本教學課程相同的目錄中，提供了名為 `accidents.R` 的定型指令碼。 請留意下列在**定型指令碼**中完成以利用 Azure Machine Learning 進行定型的詳細操作：

* 定型指令碼會使用引數 `-d` 尋找包含定型資料的目錄。 您在稍後定義並提交作業時，會指向此引數的資料存放區。 Azure ML 會將儲存體資料夾掛接至遠端叢集，以進行定型作業。
* 定型指令碼會使用 `log_metric_to_run()`，將最終正確性以計量的形式記錄到 Azure ML 中的執行記錄。 Azure ML SDK 會提供一組記錄 API，用以記錄定型執行期間的各種計量。 這些計量會記錄並保存在實驗執行記錄中。 其後，您可以從 [Studio](https://ml.azure.com) 的執行詳細資料頁面中隨時存取或檢視這些計量。 如需完整的記錄方法集 `log_*()`，請參閱[參考](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation)。
* 定型指令碼會將模型儲存到名為 **outputs** 的目錄中。 Azure ML 會以特殊方式處理 `./outputs` 資料夾。 在定型期間，寫入至 `./outputs` 的檔案會由 Azure ML 自動上傳至您的執行記錄，並以成品的形式保存。 藉由將定型的模型儲存至 `./outputs`，即使執行已結束，且您無法再存取遠端定型環境，您還是可以存取和擷取模型檔案。

### <a name="create-an-estimator"></a>建立估計工具

Azure ML 估計工具會封裝在計算目標上執行定型指令碼所需的執行設定資訊。 Azure ML 執行會以容器化作業的形式在指定的計算目標上執行。 根據預設，為您的定型作業建置的 Docker 映像會包含 R、Azure ML SDK，和一組常用的 R 套件。 請參閱此處包含的預設套件完整清單。

若要建立估計工具，請定義：

* 定型所需的指令碼所在的目錄 (`source_directory`)。 此目錄中的所有檔案都會上傳至叢集節點以供執行。 此目錄必須包含您的定型指令碼和所需的任何其他指令碼。
* 將執行的定型指令碼 (`entry_script`)。
* 計算目標 (`compute_target`)，在此案例中為您先前建立的 AmlCompute 叢集。
* 定型指令碼中的必要參數 (`script_params`)。 Azure ML 會使用 `Rscript` 以命令列指令碼的形式執行您的定型指令碼。 在本教學課程中，您會指定一個指令碼引數，也就是可用 `ds$path(target_path)` 來存取的資料目錄掛接點。
* 定型所需的任何環境相依性。 為定型建置的預設 Docker 映像已包含定型指令碼所需的三個套件 (`caret`、`e1071` 和 `optparse`)。  因此，您不需要指定其他資訊。 如果您使用依預設未包含的 R 套件，請使用估計工具的 `cran_packages` 參數來新增其他 CRAN 套件。 如需完整的可設定選項集，請參閱 [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) 參考。

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>將作業提交至叢集

最後，提交要在您的叢集上執行的作業。 `submit_experiment()` 會傳回讓您後續用來與執行進行互動的執行物件。 第一次執行總計會花費**大約 10 分鐘的時間**。 但對於後續的執行，只要指令碼相依性不變，便會重複使用相同的 Docker 映像。  在此案例中會快取映像，因此容器啟動時間會快上許多。

```R
run <- submit_experiment(exp, est)
```

您可以在 RStudio 檢視器中檢視執行的詳細資料。 按一下隨附的 [Web 檢視] 連結，將會前往 Azure Machine Learning Studio，在此處您將可使用 UI 監視執行。

```R
view_run_details(run)
```

模型定型會在背景中執行。 請等到模型完成定型之後，再執行更多程式碼。

```R
wait_for_run_completion(run, show_output = TRUE)
```

您和可存取工作區的同事可以同時提交多個實驗，Azure ML 將會在計算叢集上排程工作。 您甚至可以將叢集設定為自動擴大至多個節點，並在佇列中沒有其他計算工作時再調整回來。 這種設定對共用計算資源的小組而言，將是符合成本效益的方式。

## <a name="retrieve-training-results"></a>擷取定型結果
模型完成定型後，您就可以存取已保存到執行記錄的作業成品，包括任何已記錄的計量和最終定型的模型。

### <a name="get-the-logged-metrics"></a>取得已記錄的計量
在定型指令碼 `accidents.R` 中，您記錄了來自模型的計量：定型資料中的預測正確性。 您可以在 [studio](https://ml.azure.com) 中查看計量，或將計量以 R 清單的形式擷取至本機工作階段，如下所示：

```R
metrics <- get_run_metrics(run)
metrics
```

如果您執行了多個實驗 (例如，使用不同的變數、演算法或超參數)，您可以使用每次執行的計量來比較及選擇您將在生產環境中使用的模型。

### <a name="get-the-trained-model"></a>取得定型的模型
您可以擷取定型的模型，並查看本機 R 工作階段中的結果。 下列程式碼會下載 `./outputs` 目錄的內容，其中包括模型檔案。

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

您會看到導致死亡預估機率上升的一些因素：

* 較高的撞擊速度 
* 男性駕駛
* 較年長的乘員
* 乘客

您會發現下列情況的死亡機率較低：

* 配備安全氣囊
* 使用安全帶
* 正面碰撞 

車輛的製造年份並無重大影響。

您可以使用此模型來進行新的預測：

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>部署成 Web 服務

在您的模型中，您可以預測碰撞致死的危險性。 使用 Azure ML 將您的模型部署為預測服務。 在本教學課程中，您將在 [Azure 容器執行個體 (ACI)](https://docs.microsoft.com/azure/container-instances/) 中部署 Web 服務。

### <a name="register-the-model"></a>註冊模型

首先，使用 [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html) 將您下載的模型註冊至您的工作區。 已註冊的模型可以是任何檔案集合，但在此案例中，R 模型物件即已足夠。 Azure ML 會使用已註冊的模型進行部署。

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>定義推斷相依性
若要建立模型的 Web 服務，您必須先建立評分指令碼 (`entry_script`)；這是一個 R 指令碼，將作為 JSON 格式的輸入變數值，並輸出模型的預測。 在本教學課程中，請使用提供的評分檔案 `accident_predict.R`。 評分指令碼必須包含 `init()` 方法以載入您的模型，並傳回使用該模型根據輸入資料進行預測的函式。 如需詳細資訊，請參閱[文件](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details)。

接著，請為指令碼的套件相依性定義 Azure ML **環境**。 針對環境，您可以指定執行指令碼所需的 R 套件 (來自 CRAN 或其他位置)。 您也可以提供可讓指令碼參考以修改其行為的環境變數值。 根據預設，Azure ML 會建置估計工具用於定型的相同預設 Docker 映像。 由於本教學課程沒有特殊需求，請建立不具特殊屬性的環境。

```R
r_env <- r_environment(name = "basic_env")
```

如果您想要改用自己的 Docker 映像進行部署，請指定 `custom_docker_image` 參數。 如需用來定義環境的完整可設定選項集，請參閱 [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) 參考。

現在，建立**推斷設定**以封裝評分指令碼和環境相依性所需的一切，都已準備就緒。

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>部署到 ACI
在本教學課程中，您會將服務部署至 ACI。 此程式碼會佈建單一容器以回應輸入要求，這適用於測試和輕量負載。 如需其他可設定的選項，請參閱 [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html)。 (針對生產級別部署，您也可以[部署至 Azure Kubernetes Service](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks.html))。

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

現在，您將模型部署為 Web 服務。 部署**可能需要數分鐘的時間**。 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>測試已部署的服務

現在，您的模型已部署為服務，您可以使用 [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html) 從 R 測試服務。  提供要預測的新資料集，並將其轉換為 JSON，然後傳送至服務。

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

您也可以取得 Web 服務的 HTTP 端點，該端點會接受 REST 用戶端呼叫。 您可以和任何想要測試 Web 服務，或想要將 Web 服務整合至應用程式的使用者共用此端點。

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>清除資源

不再需要資源時，請將其刪除。 請勿刪除您仍要使用的任何資源。 

刪除 Web 服務：
```R
delete_webservice(aci_service)
```

刪除已註冊的模型：
```R
delete_model(model)
```

刪除計算叢集：
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>刪除所有內容

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

您也可以保留資源群組，但刪除單一工作區。 顯示工作區屬性，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

* 現在，您已完成使用 R 的第一個 Azure Machine Learning 實驗，接著請深入了解[適用於 R 的 Azure Machine Learning SDK](https://azure.github.io/azureml-sdk-for-r/index.html)。

* 您可以從其他 vignettes 資料夾中的範例，深入了解使用 R 的 Azure Machine Learning。
