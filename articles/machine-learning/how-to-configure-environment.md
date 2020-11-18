---
title: 設定 Python 開發環境
titleSuffix: Azure Machine Learning
description: 在 Jupyter 筆記本、Visual Studio Code、Azure Databricks 及資料科學虛擬機器中設定 Azure Machine Learning Python 開發環境。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1, devx-track-azurecli
ms.openlocfilehash: 03d10f71b585090157eff164cc98246f50608fe1
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695362"
---
# <a name="set-up-a-python-development-environment-for-azure-machine-learning"></a>針對 Azure Machine Learning 設定 Python 開發環境

瞭解如何為 Azure Machine Learning 設定 Python 開發環境。

下表顯示本文涵蓋的每個開發環境，以及優缺點。

| 環境 | 優點 | 缺點 |
| --- | --- | --- |
| [本機環境](#local) | 完全掌控您的開發環境和相依性。 使用您選擇的任何組建工具、環境或 IDE 來執行。 | 開始使用需要較長的時間。 必須安裝必要的 SDK 套件，而且如果您還沒有環境，也必須安裝環境。 |
| [資料科學虛擬機器 (DSVM) ](#dsvm) | 類似于以雲端為基礎的計算實例 (Python 和 SDK 已預先安裝) ，但已預先安裝其他熱門的資料科學和機器學習工具。 易於調整規模，並與其他自訂工具和工作流程合併。 | 相較于以雲端為基礎的計算實例，較慢的使用者入門體驗。 |
| [Azure Machine Learning 計算執行個體](#compute-instance) | 入門的最簡單方式。 整個 SDK 已安裝在您的工作區 VM 中，而筆記本教學課程已預先複製並準備好執行。 | 缺乏開發環境和相依性的控制權。 針對 Linux VM (VM 所產生的額外成本，可能會在未使用時停止，以避免) 費用。 請參閱[定價詳細資料](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。 |
| [Azure Databricks](how-to-configure-databricks-automl-environment.md) | 適用于在可擴充的 Apache Spark 平臺上執行大規模密集的機器學習工作流程。 | 實驗性機器學習的麻煩，或較小規模的實驗和工作流程。 Azure Databricks 所產生的額外成本。 請參閱[定價詳細資料](https://azure.microsoft.com/pricing/details/databricks/)。 |

本文也提供下列工具的其他使用秘訣：

* Jupyter Notebook：如果您已經在使用 Jupyter Notebook，那麼 SDK 有一些您應該安裝的附加功能。

* Visual Studio Code：如果您使用 Visual Studio Code，則 [Azure Machine Learning 延伸](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) 模組包含 Python 的廣泛語言支援，以及可讓您更方便且更有效率地使用 Azure Machine Learning 的功能。

## <a name="prerequisites"></a>先決條件

* Azure Machine Learning 工作區。 如果您沒有帳戶，可以透過 [Azure 入口網站](how-to-manage-workspace.md)、 [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)和 [Azure Resource Manager 範本](how-to-create-workspace-template.md)建立 Azure Machine Learning 工作區。

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> 僅限本機和 DSVM：建立工作區設定檔

工作區設定檔案是 JSON 檔案，可告知 SDK 如何與您的 Azure Machine Learning 工作區進行通訊。 檔案名稱為 *config.json*，其格式如下：

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

這個 JSON 檔案必須位於包含您的 Python 指令碼或 Jupyter Notebook 的目錄結構中。 可以位於相同的目錄，名為 *aml_config* 的子目錄，或位於父目錄。

若要從您的程式碼使用這個檔案，請使用 [`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#from-config-path-none--auth-none---logger-none---file-name-none-&preserve-view=true) 方法。 此程式碼會從檔案載入資訊，並連接到您的工作區。

使用下列其中一種方法來建立工作區設定檔：

* Azure 入口網站

    **下載檔案**：在 [Azure 入口網站](https://ms.portal.azure.com)中，從工作區的 [**總覽**] 區段中選取 [**下載 config.js** 。

    ![Azure 入口網站](./media/how-to-configure-environment/configure.png)

* Azure Machine Learning Python SDK

    建立腳本以連接到您的 Azure Machine Learning 工作區，然後使用 [`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#write-config-path-none--file-name-none-&preserve-view=true) 方法來產生您的檔案，並將它儲存為 *azureml/config.js*。 請務必將 `subscription_id` 、和取代為 `resource_group` `workspace_name` 您自己的。

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

## <a name="local-computer-or-remote-vm-environment"></a><a id="local"></a>本機電腦或遠端 VM 環境

您可以在本機電腦或遠端虛擬機器上設定環境，例如 Azure Machine Learning 計算實例或資料科學 VM。 

若要設定本機開發環境或遠端 VM：

1. 建立 Python 虛擬環境 (virtualenv、conda) 。

    > [!NOTE]
    > 雖然並非必要，但建議您使用 [Anaconda](https://www.anaconda.com/download/) 或 [Miniconda](https://www.anaconda.com/download/) 來管理 Python 虛擬環境並安裝套件。

    > [!IMPORTANT]
    > 如果您使用的是 Linux 或 macOS 並使用 bash 以外的殼層 (例如，zsh)，則在執行一些命令時可能會收到錯誤。 若要解決此問題，請使用 `bash` 命令啟動新的 bash 殼層，並在其中執行命令。

1. 啟用新建立的 Python 虛擬環境。
1. 安裝 [Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。
1. 若要設定您的本機環境以使用您的 Azure Machine Learning 工作區，請 [建立工作區設定檔或使用現有的設定檔](#workspace) 。

現在您已設定本機環境，接下來就可以開始使用 Azure Machine Learning。 請參閱 [Azure Machine Learning Python 快速入門手冊](tutorial-1st-experiment-sdk-setup-local.md) 以開始使用。

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebook

執行本機 Jupyter Notebook 伺服器時，建議您建立 Python 虛擬環境的 IPython 核心。 這有助於確保預期的核心和套件匯入行為。

1. 啟用環境特定的 IPython 核心

    ```bash
    conda install notebook ipykernel
    ```

1. 為您的 Python 虛擬環境建立核心。 請務必以 `<myenv>` 您的 Python 虛擬環境名稱取代。

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. 啟動 Jupyter Notebook 伺服器

若要開始使用 Azure Machine Learning 和 Jupyter 筆記本，請參閱 [Azure Machine Learning 筆記本存放庫](https://github.com/Azure/MachineLearningNotebooks) 。

> [!NOTE]
> 如需範例的社群導向存放庫，請參閱 https://github.com/Azure/azureml-examples 。

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

若要使用 Visual Studio Code 進行開發：

1. 安裝 [Visual Studio Code](https://code.visualstudio.com/Download)。
1.  (preview) 安裝 [Azure Machine Learning Visual Studio Code 擴充](tutorial-setup-vscode-extension.md) 功能。

安裝 Visual Studio Code 擴充功能之後，您就可以管理 [Azure Machine Learning 資源](how-to-manage-resources-vscode.md)、 [執行和調試](how-to-debug-visual-studio-code.md)程式，以及 [部署定型的模型](tutorial-train-deploy-image-classification-model-vscode.md)。

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Azure Machine Learning 計算實例

Azure Machine Learning [計算實例](concept-compute-instance.md) 是安全的雲端式 Azure 工作站，可為數據科學家提供 Jupyter Notebook 伺服器、JupyterLab 和完全受控的機器學習環境。

不需要為計算實例安裝或設定任何專案。  

您隨時都能從 Azure Machine Learning 工作區中建立一個。 只提供名稱並指定 Azure VM 類型。 請在此教學課程中立即試用 [：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)。

若要深入瞭解計算實例，包括如何安裝套件，請參閱 [建立和管理 Azure Machine Learning 計算實例](how-to-create-manage-compute-instance.md)。

> [!TIP]
> 若要避免未使用的計算實例產生費用，請 [停止計算實例](how-to-create-manage-compute-instance.md#manage)。

除了 Jupyter Notebook server 和 JupyterLab 之外，您還可以在 [Azure Machine Learning studio 內的整合式筆記本功能](how-to-run-jupyter-notebooks.md)中使用計算實例。

您也可以使用 Azure Machine Learning Visual Studio Code 延伸模組，將 [Azure Machine Learning 計算實例設定為遠端 Jupyter Notebook 伺服器](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server)。

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>資料科學虛擬機器

資料科學 VM 是自訂的虛擬機器 (VM) 映射，可供您用來作為開發環境。 它是專為數據科學工作而設計，其預先設定的工具和軟體如下：

  - TensorFlow、PyTorch、Scikit-learn、XGBoost 及 Azure Machine Learning SDK 等套件
  - Spark 獨立版和 Drill 等常用的資料科學工具
  - Azure CLI、AzCopy 及儲存體總管等 Azure 工具
  - Visual Studio Code 和 PyCharm 等整合式開發環境 (IDE)
  - Jupyter Notebook 伺服器

如需更完整的工具清單，請參閱 [資料科學 VM 工具指南](data-science-virtual-machine/tools-included.md)。

> [!IMPORTANT]
> 如果您打算使用資料科學 VM 作為定型或推斷作業的 [計算目標](concept-compute-target.md) ，則僅支援 Ubuntu。

使用資料科學 VM 作為開發環境：

1. 使用下列其中一種方法建立資料科學 VM：

    * 使用 Azure 入口網站來建立 [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) 或 [Windows](data-science-virtual-machine/provision-vm.md) DSVM。
    * [使用 ARM 範本建立資料科學 VM](data-science-virtual-machine/dsvm-tutorial-resource-manager.md)。
    * 使用 Azure CLI

        若要建立 Ubuntu 資料科學 VM，請使用下列命令：

        ```azurecli-interactive
        # create a Ubuntu Data Science VM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        若要建立 Windows DSVM，請使用下列命令：

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. 啟動包含 Azure Machine Learning SDK 的 conda 環境。

    * 針對 Ubuntu 資料科學 VM：

        ```bash
        conda activate py36
        ```

    * 針對 Windows 資料科學 VM：

        ```bash
        conda activate AzureML
        ```

1. 若要將資料科學 VM 設定為使用您的 Azure Machine Learning 工作區，請 [建立工作區設定檔](#workspace) ，或使用現有的設定檔。

與本機環境類似，您可以使用 Visual Studio Code 和 [Azure Machine Learning Visual Studio Code 擴充](#vscode) 功能來與 Azure Machine Learning 互動。

如需詳細資訊，請參閱[資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。


## <a name="next-steps"></a>後續步驟

- 使用 MNIST 資料集將 Azure Machine Learning 上的[模型定型](tutorial-train-models-with-aml.md)。
- 請參閱 [適用于 Python 的 AZURE MACHINE LEARNING SDK 參考](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)。 