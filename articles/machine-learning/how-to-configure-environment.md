---
title: 設定 Python 開發環境
titleSuffix: Azure Machine Learning
description: 瞭解如何設定您的開發環境以進行 Azure Machine Learning。 使用 Conda 環境、建立設定檔，以及設定您自己的雲端式筆記本伺服器、Jupyter 筆記本、Azure Databricks、Ide、程式碼編輯器和資料科學虛擬機器。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 0fef7d6f59b8893ff400914f491f421cddf436b7
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842915"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>設定 Azure Machine Learning 的開發環境
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何設定開發環境來與 Azure Machine Learning 搭配使用。 Azure Machine Learning 與平臺無關。 您的開發環境唯一的硬性需求是 Python 3。 也建議使用隔離的環境，例如 Anaconda 或 Virtualenv。

下表顯示本文涵蓋的每個開發環境，以及優缺點。

| 環境 | 優點 | 缺點 |
| --- | --- | --- |
| [以雲端為基礎的 Azure Machine Learning 計算實例（預覽）](#compute-instance) | 最簡單的入門方式。 整個 SDK 已安裝在您的工作區 VM 中，且筆記本教學課程已預先複製並可供執行。 | 缺少開發環境和相依性的控制權。 Linux VM 所產生的額外成本（VM 可以在未使用時停止，以避免費用）。 請參閱[定價詳細資料](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。 |
| [本機環境](#local) | 完全控制您的開發環境和相依性。 使用您選擇的任何組建工具、環境或 IDE 來執行。 | 開始使用較長的時間。 必須安裝必要的 SDK 套件，如果您還沒有環境，也必須安裝它。 |
| [Azure Databricks](#aml-databricks) | 適用于在可調整的 Apache Spark 平臺上執行大規模的機器學習工作流程。 | 適用于實驗性機器學習服務的大材小用，或較小規模的實驗和工作流程。 Azure Databricks 所產生的額外成本。 請參閱[定價詳細資料](https://azure.microsoft.com/pricing/details/databricks/)。 |
| [資料科學虛擬機器（DSVM）](#dsvm) | 類似于雲端式計算實例（Python 和 SDK 已預先安裝），但已預先安裝其他常用的資料科學和機器學習工具。 易於調整，並與其他自訂工具和工作流程結合。 | 相較于雲端式計算實例，更慢的使用者入門體驗。 |


本文也提供下列工具的其他使用秘訣：

* [Jupyter 筆記本](#jupyter)：如果您已經在使用 JUPYTER NOTEBOOK，SDK 會有一些您應該安裝的額外專案。

* [Visual Studio Code](#vscode)：如果您使用 Visual Studio Code， [Azure Machine Learning 延伸](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)模組包含 Python 的廣泛語言支援，以及可讓您更方便且更有效率地使用 Azure Machine Learning 的功能。

## <a name="prerequisites"></a>必要條件

Azure Machine Learning 工作區。 若要建立工作區，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。 您只需要工作區，就能開始使用自己的[雲端式筆記本伺服器](#compute-instance)、 [DSVM](#dsvm)或[Azure Databricks](#aml-databricks)。

若要為您的[本機電腦](#local)安裝 SDK 環境， [Jupyter Notebook server](#jupyter)或[Visual Studio Code](#vscode)您也需要：

- 可能是[Anaconda](https://www.anaconda.com/download/)或[Miniconda](https://conda.io/miniconda.html)套件管理員。

- 在 Linux 或 macOS 上，您需要 bash 殼層。

    > [!TIP]
    > 如果您使用的是 Linux 或 macOS 並使用 bash 以外的殼層 (例如，zsh)，則在執行一些命令時可能會收到錯誤。 若要解決此問題，請使用 `bash` 命令啟動新的 bash 殼層，並在其中執行命令。

- 在 Windows 上，您需要命令提示字元或 Anaconda 提示字元 (由 Anaconda 和 Miniconda 安裝)。

## <a id="compute-instance"></a>您自己的雲端式計算實例

Azure Machine Learning[計算實例（預覽）](concept-compute-instance.md)是安全的雲端式 Azure 工作站，可為數據科學家提供 Jupyter 筆記本伺服器、JupyterLab 和完整備妥的 ML 環境。

> [!NOTE]
> 計算實例僅適用于具有**美國中北部**、* * 美國東部 2 * ***、北歐**或**英國南部**的工作區。
>如果您的工作區位於任何其他區域，您可繼續建立並使用 [Notebook VM](concept-compute-instance.md#notebookvm)。

不需要為計算實例安裝或設定任何內容。  從您的 Azure Machine Learning 工作區中隨時建立一個。 只提供名稱，並指定 Azure VM 類型。 請在本教學課程中立即試用[：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)。


深入瞭解[計算實例](concept-compute-instance.md)。

若要停止產生計算費用，請[停止計算實例](tutorial-1st-experiment-sdk-train.md#clean-up-resources)。

## <a id="dsvm"></a>資料科學虛擬機器

DSVM 是自訂的虛擬機器 (VM) 映像。 它是針對使用下列項目預先設定的資料科學工作而設計：

  - TensorFlow、PyTorch、Scikit-learn、XGBoost 及 Azure Machine Learning SDK 等套件
  - Spark 獨立版和 Drill 等常用的資料科學工具
  - Azure CLI、AzCopy 及儲存體總管等 Azure 工具
  - Visual Studio Code 和 PyCharm 等整合式開發環境 (IDE)
  - Jupyter Notebook 伺服器

Azure Machine Learning SDK 適用於 Ubuntu 或 Windows版本的 DSVM。 但如果您也打算使用 DSVM 作為計算目標，則僅支援 Ubuntu。

若要使用 DSVM 做為開發環境：

1. 在下列其中一個環境中建立 DSVM：

    * Azure 入口網站：

        * [建立 Ubuntu 資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [建立 Windows 資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI：

        > [!IMPORTANT]
        > * 使用 Azure CLI 時，您必須先使用 `az login` 命令來登入您的 Azure 訂用帳戶。
        >
        > * 使用此步驟中的命令時，您必須提供資源群組名稱、VM 名稱、使用者名稱及密碼。

        * 若要建立「Ubuntu 資料科學虛擬機器」，請使用下列命令：

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * 若要建立「Windows 資料科學虛擬機器」，請使用下列命令：

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Azure Machine Learning SDK 已安裝在 DSVM 上。 若要使用包含該 SDK 的 Conda 環境，請使用下列命令之一：

    * Ubuntu DSVM：

        ```shell
        conda activate py36
        ```

    * Windows DSVM：

        ```shell
        conda activate AzureML
        ```

1. 若要確認您是否可以存取 SDK 並檢查版本，請使用下列 Python 程式碼：

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. 若要將 DSVM 設定為使用您的 Azure Machine Learning 工作區，請參閱[建立工作區設定檔](#workspace)一節。

如需詳細資訊，請參閱[資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a id="local"></a>本機電腦

當您使用本機電腦（也可能是遠端虛擬機器）時，請建立 Anaconda 環境並安裝 SDK。 以下是範例：

1. 如果您還沒有[Anaconda](https://www.anaconda.com/distribution/#download-section) （Python 3.7 版本），請下載並安裝。

1. 開啟 Anaconda 提示字元，並使用下列命令建立環境：

    執行下列命令來建立環境。

    ```shell
    conda create -n myenv python=3.6.5
    ```

    然後啟用環境。

    ```shell
    conda activate myenv
    ```

    這個範例會使用 python 3.6.5 建立環境，但是可以選擇任何特定的 subversions。 SDK 相容性可能無法保證某些主要版本（建議使用 3.5 +），如果遇到錯誤，建議您在 Anaconda 環境中嘗試不同的版本/subversion。 建立環境可能需要幾分鐘的時間，因為需要下載元件和套件。

1. 在您的新環境中執行下列命令，以啟用環境特定的 IPython 核心。 這可確保在 Anaconda 環境中使用 Jupyter 筆記本時，預期的核心和套件匯入行為：

    ```shell
    conda install notebook ipykernel
    ```

    然後執行下列命令來建立核心：

    ```shell
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. 使用下列命令來安裝套件：

    此命令會使用筆記本和 `automl` 額外專案來安裝基底 Azure Machine Learning SDK。 `automl` 額外的是大型安裝，如果您不想要執行自動化機器學習實驗，可以從括弧中移除。 `automl` 額外項也包含 Azure Machine Learning 資料準備 SDK，預設為相依性。

    ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * 如果顯示訊息表示無法解除安裝 PyYAML ，請改用下列命令：
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * 從 macOS Catalina 開始，預設登入殼層和互動式殼層皆為 zsh (Z 殼層)。 在 zsh 中，請使用下列命令，並以 "\\" (反斜線) 逸出括號：
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`


   安裝 SDK 需要幾分鐘的時間。 如需安裝選項的詳細資訊，請參閱[安裝指南](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

1. 為您的機器學習實驗安裝其他套件。

    使用下列其中一個命令，並將 *\<新封裝 >* 取代為您要安裝的套件。 透過 `conda install` 安裝套件時，套件必須是目前通道的一部分（可以在 Anaconda Cloud 中新增新的通道）。

    ```shell
    conda install <new package>
    ```

    或者，您也可以透過 `pip`安裝套件。

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook 是 [Jupyter 專案](https://jupyter.org/)的一部分。 它們提供互動式程式碼撰寫體驗，讓您用來建立混合即時程式碼與敘述文字和圖形的文件。 Jupyter Notebook 也是與其他人共用結果的好方法，因為您可以將程式碼區段的輸出儲存在文件中。 您可以在各種不同的平台上安裝 Jupyter Notebook。

[[本機電腦](#local)] 區段中的程式會安裝在 Anaconda 環境中執行 Jupyter 筆記本所需的元件。

若要在您的 Jupyter Notebook 環境中啟用這些元件：

1. 開啟 Anaconda 提示字元並啟用您的環境。

    ```shell
    conda activate myenv
    ```

1. 複製一組範例筆記本[的 GitHub 存放庫](https://aka.ms/aml-notebooks)。

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 使用下列命令啟動 Jupyter Notebook 伺服器：

    ```shell
    jupyter notebook
    ```

1. 若要確認 Jupyter Notebook 可以使用 SDK，請建立**新**的筆記本，選取 [ **Python 3** ] 做為您的核心，然後在 [筆記本] 儲存格中執行下列命令：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 如果您在匯入模組和接收 `ModuleNotFoundError`時遇到問題，請在 [筆記本] 儲存格中執行下列程式碼，以確保 Jupyter 核心已連接到您環境的正確路徑。

    ```python
    import sys
    sys.path
    ```

1. 若要設定 Jupyter Notebook 使用您的 Azure Machine Learning 工作區，請移至[建立工作區設定檔](#workspace)一節。


### <a id="vscode"></a>Visual Studio Code

Visual Studio Code 是一個非常熱門的跨平臺程式碼編輯器，可透過[Visual Studio marketplace](https://marketplace.visualstudio.com/vscode)中提供的延伸模組，支援一組豐富的程式設計語言和工具。 [Azure Machine Learning 延伸](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)模組會安裝[python 延伸](https://marketplace.visualstudio.com/items?itemName=ms-python.python)模組，以便在所有類型的 python 環境中撰寫程式碼（虛擬、Anaconda 等）。 此外，它還提供便利的功能來處理 Azure Machine Learning 資源，並在不離開 Visual Studio Code 的情況下執行 Azure Machine Learning 實驗。

若要使用 Visual Studio Code 進行開發：

1. 安裝 Visual Studio Code 的 Azure Machine Learning 延伸模組，請參閱[Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)。

    如需詳細資訊，請參閱[使用適用於 Visual Studio Code 的 Azure Machine Learning](tutorial-setup-vscode-extension.md)。

1. 瞭解如何將 Visual Studio Code 用於任何類型的 Python 開發，請參閱[在 VSCode 中開始使用 python](https://code.visualstudio.com/docs/python/python-tutorial)。

    - 若要選取包含 SDK 的 SDK Python 環境，請開啟 [VS Code]，然後選取 [Ctrl + Shift + P （Linux 和 Windows）] 或 [命令 + Shift + P （Mac）]。
        - __命令__選擇區隨即開啟。

    - 輸入__Python：選取 [解釋__器]，然後選取適當的環境

1. 若要驗證您是否可以使用 SDK，請建立新的 Python 檔案（. .py），其中包含下列程式碼：

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    按一下 [執行資料格] CodeLens，或直接按 shift enter 來執行此程式碼。
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks 是 Azure 雲端中以 Apache Spark 為基礎的環境。 它提供以 CPU 或 GPU 為基礎的計算叢集的共同作業筆記本型環境。

Azure Databricks 如何與 Azure Machine Learning 搭配運作：
+ 您可以使用 Spark MLlib 來定型模型，並從 Azure Databricks 內將模型部署至 ACI/AKS。
+ 您也可以在具有 Azure Databricks 的特殊 Azure ML SDK 中，使用[自動化機器學習](concept-automated-ml.md)功能。
+ 您可以使用 Azure Databricks 做為來自[Azure Machine Learning 管線](concept-ml-pipelines.md)的計算目標。

### <a name="set-up-your-databricks-cluster"></a>設定 Databricks 叢集

建立[Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)叢集。 只有當您在 Databricks 上安裝 SDK 以進行自動化機器學習時，才適用某些設定。
**建立叢集需要幾分鐘的時間。**

使用這些設定：

| 設定 |適用於| 值 |
|----|---|---|
| 叢集名稱 |always| yourclustername |
| Databricks 執行階段 |always|非 ML 執行時間6.0 （scala 2.11、spark 2.4.3） |
| Python 版本 |always| 3 |
| 背景工作角色 |always| 2 個以上 |
| 背景工作節點 VM 類型 <br>（判斷並行反覆運算的最大數目） |自動化 ML<br>向| 建議使用已記憶體最佳化的 VM |
| 啟用自動調整功能 |自動化 ML<br>向| 取消選取 |

請靜候至叢集運作，再繼續操作。

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>將正確的 SDK 安裝到 Databricks 程式庫
叢集執行之後，請[建立程式庫](https://docs.databricks.com/user-guide/libraries.html#create-a-library)，以將適當的 Azure Machine Learning SDK 套件附加至您的叢集。

1. 以滑鼠右鍵按一下您要儲存程式庫的目前工作區資料夾。 選取 [**建立** > 連結**庫**]。

1. **只選擇一個**選項（不支援其他 SDK 安裝）

   |SDK&nbsp;套件&nbsp;額外專案|來源|PyPi&nbsp;名稱&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |針對 Databricks| 上傳 Python Egg 或 PyPI | azureml-sdk[databricks]|
   |針對 Databricks-with-<br> 自動化 ML 功能| 上傳 Python Egg 或 PyPI | azureml-sdk [automl]|

   > [!Warning]
   > 無法安裝其他 SDK 額外專案。 只選擇上述其中一個選項 [databricks] 或 [automl]。

   * 請勿選取 [**自動附加至所有**叢集]。
   * 選取叢集名稱旁的 [**附加**]。

1. 監視錯誤直到狀態變更為 [已**附加**]，這可能需要幾分鐘的時間。  如果此步驟失敗：

   嘗試重新開機您的叢集：
   1. 在左窗格中，選取 [叢集]。
   1. 請選取表格中您的叢集名稱。
   1. 在 [程式庫] 索引標籤上，選取 [重新啟動]。

   也請考慮：
   + 在 AutoML config 中，使用 Azure Databricks 新增下列參數：
       1. ```max_concurrent_iterations``` 是以叢集中的背景工作節點數目為基礎。
        2. ```spark_context=sc``` 是以預設的 spark 內容為基礎。
   + 或者，如果您有舊的 SDK 版本，請從叢集的已安裝的程式庫中取消選取它，並移至垃圾桶。 安裝新版 SDK，並重新啟動叢集。 如果重新開機後發生問題，請卸離並重新附加您的叢集。

如果安裝成功，則匯入的程式庫看起來應該像下列其中一項：

適用于 Databricks 的 SDK （ **_不含_** 自動化機器學習服務） ![Azure Machine Learning Sdk for Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK for Databricks 搭配自動化機器學習服務 ![**sdk，並**在 Databricks 上安裝自動化機器學習服務](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>開始探索

現在就試試看：
+ 雖然有許多範例筆記本可供使用，但**只有[這些範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)適用于 Azure Databricks。**

+ 直接從您的工作區匯入這些範例。 請參閱以下內容： ![選取 [匯入]](./media/how-to-configure-environment/azure-db-screenshot.png)
![匯入面板](./media/how-to-configure-environment/azure-db-import.png)

+ 瞭解如何[使用 Databricks 作為訓練計算來建立管線](how-to-create-your-first-pipeline.md)。

## <a id="workspace"></a>建立工作區組態檔

工作區設定檔是一種 JSON 檔案，可告知 SDK 如何與您的 Azure Machine Learning 工作區進行通訊。 檔案名稱為 *config.json*，其格式如下：

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

這個 JSON 檔案必須位於包含您的 Python 指令碼或 Jupyter Notebook 的目錄結構中。 可以位於相同的目錄，名為 *aml_config* 的子目錄，或位於父目錄。

要使用程式碼中的此檔案，請使用 `ws=Workspace.from_config()`。 此程式碼會從檔案載入資訊，並連接到您的工作區。

您可以透過三種方式建立組態檔：

* **使用[ws. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** ：寫入*config.xml*檔案。 此檔案包含您工作區的組態資訊。 您可以將此 *config.json* 下載或複製到其他開發環境。

* **下載檔案**：在[Azure 入口網站](https://ms.portal.azure.com)中，從工作區的 [**總覽**] 區段中選取 [**下載 config.xml** ]。

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **以程式設計方式建立**檔案：在下列程式碼片段中，您可以藉由提供訂用帳戶識別碼、資源群組和工作區名稱來連接到工作區。 接著，它會將工作區組態儲存至檔案：

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    此程式碼會將設定檔寫入*azureml/config json*檔案。


## <a name="next-steps"></a>後續步驟

- 在 Azure Machine Learning 使用 MNIST 資料集[定型模型](tutorial-train-models-with-aml.md) \(英文\)
- 檢視[適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) \(英文\) 參考
