---
title: 使用 Visual Studio Code 的互動式調試
titleSuffix: Azure Machine Learning
description: 使用 Visual Studio Code 以互動方式進行 Azure Machine Learning 程式碼、管線和部署的偵錯工具
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
author: luisquintanilla
ms.author: luquinta
ms.date: 08/06/2020
ms.openlocfilehash: 1449114ad14ebbd064f95ad2853b516893ba4b12
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661697"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>使用 Visual Studio Code 的互動式調試

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何使用 Visual Studio Code (VS Code) 和 [depugpy](https://github.com/microsoft/debugpy/)，以互動方式來偵測 Azure Machine Learning 管線和部署。

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>機器學習管線的偵錯和疑難排解

在某些情況下，您可能需要以互動方式來偵測 ML 管線中使用的 Python 程式碼。 藉由使用 VS Code 和 debugpy，您可以在定型環境中執行時附加至程式碼。

### <a name="prerequisites"></a>必要條件

* 設定為使用__Azure 虛擬網路__的__Azure Machine Learning 工作區__。
* 在管線步驟中使用 Python 腳本的 __Azure Machine Learning 管線__ 。 例如，PythonScriptStep。
* Azure Machine Learning 的計算叢集，其位於 __虛擬網路中__ ，供 __管線用來進行定型__。
* 位於__虛擬網路中__的__開發環境__。 開發環境可能是下列其中一項：

  * 虛擬網路中的 Azure 虛擬機器
  * 虛擬網路中筆記本 VM 的計算實例
  * 用戶端電腦，其具有虛擬網路的私人網路連線能力（透過 VPN 或 ExpressRoute）。

如需有關使用 Azure 虛擬網路搭配 Azure Machine Learning 的詳細資訊，請參閱 [虛擬網路隔離和隱私權總覽](how-to-network-security-overview.md)。

> [!TIP]
> 雖然您可以使用不在虛擬網路後方的 Azure Machine Learning 資源，但建議使用虛擬網路。

### <a name="how-it-works"></a>運作方式

您的 ML 管線步驟會執行 Python 腳本。 這些腳本會經過修改，以執行下列動作：

1. 記錄正在執行之主機的 IP 位址。 您可以使用 IP 位址將偵錯工具連線至腳本。

2. 啟動 debugpy debug 元件，並等候偵錯工具連接。

3. 從您的開發環境中，您可以監視定型程式所建立的記錄，以找出腳本執行所在的 IP 位址。

4. 您可以使用檔案告訴 VS Code 要將偵錯工具連接到的 IP 位址 `launch.json` 。

5. 您可以附加偵錯工具，並以互動方式逐步執行腳本。

### <a name="configure-python-scripts"></a>設定 Python 腳本

若要啟用偵錯工具，請對您的 ML 管線中的步驟所使用的 Python 腳本進行下列變更)  (s：

1. 新增下列 import 語句：

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. 新增下列引數。 這些引數可讓您視需要啟用偵錯工具，並設定附加偵錯工具的超時時間：

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. 新增下列語句。 這些語句會載入目前的執行內容，讓您可以記錄執行程式碼之節點的 IP 位址：

    ```python
    global run
    run = Run.get_context()
    ```

1. 加入可 `if` 啟動 debugpy 並等候偵錯工具附加的語句。 如果沒有偵錯工具在超時之前附加，腳本會繼續正常執行。 請務必將 `HOST` 和值取代 `PORT` 為 `listen` 您自己的函數。

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

下列 Python 範例顯示 `train.py` 啟用偵錯工具的基本檔案：

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>設定 ML 管線

若要提供啟動 debugpy 所需的 Python 套件並取得執行內容，請建立環境並設定 `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` 。 變更 SDK 版本，使其符合您所使用的版本。 下列程式碼片段示範如何建立環境：

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

在 [ [設定 Python 腳本](#configure-python-scripts) ] 區段中，已將新引數新增至您的 ML 管線步驟所使用的腳本。 下列程式碼片段示範如何使用這些引數來啟用元件的偵錯工具，並設定 timeout。 它也會示範如何使用稍早透過設定來建立的環境 `runconfig=run_config` ：

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

當管線執行時，每個步驟都會建立子執行。 如果啟用了偵錯工具，修改過的腳本會在子執行的中記錄類似下列文字的資訊 `70_driver_log.txt` ：

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

儲存 `ip_address` 值。 此資訊使用於下一節。

> [!TIP]
> 您也可以從執行記錄中尋找此管線步驟的子回合執行記錄的 IP 位址。 如需有關如何查看此資訊的詳細資訊，請參閱 [監視 AZURE ML 實驗執行和計量](how-to-track-experiments.md)。

### <a name="configure-development-environment"></a>設定開發環境

1. 若要在 VS Code 開發環境上安裝 debugpy，請使用下列命令：

    ```bash
    python -m pip install --upgrade debugpy
    ```

    如需搭配 VS Code 使用 debugpy 的詳細資訊，請參閱 [遠端偵錯](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)程式。

1. 若要將 VS Code 設定為與正在執行偵錯工具的 Azure Machine Learning 計算進行通訊，請建立新的 debug 設定：

    1. 從 VS Code 選取 [偵錯] 功能表，然後選取 [開啟組態]。 隨即開啟名為 launch.json 的檔案。

    1. 在 [檔案 __launch.js__ 中，尋找包含的行 `"configurations": [` ，並在其後插入下列文字。 將 `"host": "<IP-ADDRESS>"` 專案變更為您在上一節中的記錄所傳回的 IP 位址。 將 `"localRoot": "${workspaceFolder}/code/step"` 專案變更為本機目錄，其中包含要進行調試的腳本複本：

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 如果組態區段中已經有其他項目，請在您插入的程式碼後面新增逗號 (,)。

        > [!TIP]
        > 最佳做法，特別是針對管線，將腳本的資源保留在不同的目錄中，讓程式碼只與每個步驟相關。 在此範例中， `localRoot` 範例值會參考 `/code/step1` 。
        >
        > 如果您要對多個腳本進行偵錯工具，請在不同的目錄中為每個腳本建立個別的設定區段。

    1. 儲存 launch.json檔案。

### <a name="connect-the-debugger"></a>連接偵錯工具

1. 開啟 VS Code 並開啟腳本的本機複本。
2. 設定您要讓腳本在附加之後停止的中斷點。
3. 當子進程正在執行腳本，且 `Timeout for debug connection` 顯示在記錄檔中時，請使用 F5 鍵或選取 [ __Debug__]。 出現提示時，請選取 [ __計算：遠端偵錯] Azure Machine Learning__ 。 您也可以從提要欄位選取 [偵錯工具] 圖示，從 [偵錯工具] 下拉式功能表選取 [ __Azure Machine Learning：遠端偵錯__ 程式] 專案，然後使用綠色箭號附加偵錯工具。

    此時，VS Code 會連接到計算節點上的 debugpy，並在您先前設定的中斷點停止。 您現在可以在程式碼執行時逐步執行、檢視變數等。

    > [!NOTE]
    > 如果記錄檔顯示指出的專案 `Debugger attached = False` ，則 timeout 已過期，且腳本會在沒有偵錯工具的情況下繼續。 再次提交管線，並將偵錯工具連接到 `Timeout for debug connection` 訊息之後，以及在超時時間到期之前。

## <a name="debug-and-troubleshoot-deployments"></a>針對部署進行調試和疑難排解

在某些情況下，您可能需要以互動方式來對模型部署中包含的 Python 程式碼進行偵錯。 例如，如果輸入腳本失敗，而且無法由其他記錄來判斷原因。 藉由使用 VS Code 和 debugpy，您可以附加至在 Docker 容器內執行的程式碼。

> [!IMPORTANT]
> 使用 `Model.deploy()` 和 `LocalWebservice.deploy_configuration` 在本機部署模型時，無法使用這種偵錯方法。 相反地，您必須使用 [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-) 方法來建立映像。

本機 Web 服務部署需要在您的本機系統上執行正常的 Docker 安裝。 如需使用 Docker 的詳細資訊，請參閱 [Docker Azure 文件](https://docs.docker.com/)。 請注意，使用計算實例時，已安裝 Docker。

### <a name="configure-development-environment"></a>設定開發環境

1. 若要在本機 VS Code 開發環境上安裝 debugpy，請使用下列命令：

    ```bash
    python -m pip install --upgrade debugpy
    ```

    如需搭配 VS Code 使用 debugpy 的詳細資訊，請參閱 [遠端偵錯](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)程式。

1. 若要設定 VS Code 以與 Docker 映像通訊，請建立新的偵錯組態：

    1. 從 VS Code 選取 [偵錯] 功能表，然後選取 [開啟組態]。 隨即開啟名為 launch.json 的檔案。

    1. 在 launch.json 檔案中，尋找包含 `"configurations": [` 的那一行，並在其後插入下列文字：

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 如果組態區段中已經有其他項目，請在您插入的程式碼後面新增逗號 (,)。

        本節會使用連接埠 5678 附加至 Docker 容器。

    1. 儲存 launch.json檔案。

### <a name="create-an-image-that-includes-debugpy"></a>建立包含 debugpy 的映射

1. 為您的部署修改 conda 環境，讓它包含 debugpy。 以下範例示範如何使用 `pip_packages` 參數新增：

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. 若要在服務啟動時啟動 debugpy 並等候連接，請將下列內容新增至檔案頂端 `score.py` ：

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. 建立以環境定義為基礎的映像，並將映像提取到本機登錄。 

    > [!NOTE]
    > 這個範例假設 `ws` 指向您的 Azure Machine Learning 工作區，而且該 `model` 是要部署的模型。 `myenv.yml` 檔案包含在步驟 1 中建立的 Conda 相依性。

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    建立並下載映像之後，映像路徑 (在此案例中也會包含存放庫、名稱和標籤，也就是其摘要) 會顯示在類似下列的訊息中：

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. 若要更輕鬆地使用映像，請使用下列命令來新增標籤。 使用先前步驟中的位置值取代 `myimagepath`。

    ```bash
    docker tag myimagepath debug:1
    ```

    在其餘的步驟中，您可以將本機映像參照為 `debug:1`，而不是完整的映像路徑值。

### <a name="debug-the-service"></a>服務偵錯

> [!TIP]
> 如果您在檔案中設定 debugpy 連接的超時時間 `score.py` ，您必須將 VS Code 連接到超時時間過期之前的 debug 會話。 啟動 VS Code 並開啟 `score.py` 的本機複本，然後設定中斷點，並在使用本節中的步驟之前做好準備。
>
> 如需有關偵錯和設定中斷點的詳細資訊，請參閱[偵錯](https://code.visualstudio.com/Docs/editor/debugging)。

1. 若要使用映像啟動 Docker 容器，請使用下列命令：

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_path_to_score.py>:/var/azureml-apps/score.py debug:1 /bin/bash
    ```

    這會將您的 `score.py` 本機連接到容器中的一個。 因此，在編輯器中所做的任何變更都會自動反映在容器中。

1. 在容器內，于 shell 中執行下列命令

    ```bash
    runsvdir /var/runit
    ```

1. 若要將 VS Code 附加至容器內的 debugpy，請開啟 VS Code 並使用 F5 鍵或選取 [ __Debug__]。 出現提示時，請選取 __Azure Machine Learning 部署： Docker Debug__ configuration。 您也可以從側邊列選取 [偵錯工具] 圖示，從 [偵錯工具] 下拉式功能表中選取 [ __Azure Machine Learning 部署： Docker debug__ ] 專案，然後使用綠色箭號附加偵錯工具。

    ![偵錯圖示、啟動偵錯按鈕和組態選取器](./media/how-to-troubleshoot-deployment/start-debugging.png)

此時，VS Code 會連接到 Docker 容器內的 debugpy，並在您先前設定的中斷點停止。 您現在可以在程式碼執行時逐步執行、檢視變數等。

如需使用 VS Code 來偵錯 Python 的詳細資訊，請參閱[偵錯您的 Python 程式碼](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)。

### <a name="stop-the-container"></a>停止容器

若要停止容器，請使用下列命令：

```bash
docker stop debug
```

## <a name="next-steps"></a>後續步驟

現在您已設定 Visual Studio Code 遠端，您可以使用計算實例作為 Visual Studio Code 的遠端計算，以互動方式進行程式碼的偵錯工具。 

[教學課程：進行第一個 ML 模型的定型](tutorial-1st-experiment-sdk-train.md)會示範如何搭配使用計算執行個體與整合式筆記本。
