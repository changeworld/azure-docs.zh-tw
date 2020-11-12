---
title: 將 PyTorch 模型部署為 Azure Functions 應用程式
description: 從 PyTorch 中預先定型的 ResNet 18 深度類神經網路搭配 Azure Functions，將 1000 個 ImageNet 標籤的其中之一指派給影像。
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 8891c29e5d8d06df6292d06ec06e5e57fb9880e7
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422836"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>教學課程：使用 PyTorch 將預先定型的影像分類模型部署到 Azure Functions

在本文中，您將了解如何使用 Python、PyTorch 和 Azure Functions 根據影像的內容載入預先定型的模型，以將影像分類。 您將在本機上執行所有工作，且不會在雲端中建立任何 Azure 資源，因此完成本教學課程並不會產生任何費用。

> [!div class="checklist"]
> * 初始化本機環境以在 Python 中開發 Azure Functions。
> * 將預先定型的 PyTorch 機器學習模型匯入至函式應用程式。
> * 建置無伺服器 HTTP API，將影像分類為 1000 個 ImageNet [分類](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a)中的其中一種。
> * 從 Web 應用程式取用 API。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Python 3.7.4 或更新版本](https://www.python.org/downloads/release/python-374/)。 (Python 3.8. x 和 Python 3.6. x 也會使用 Azure Functions 進行驗證。)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- 程式碼編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>先決條件檢查

1. 在終端機或命令視窗中，執行 `func --version`，以確認 Azure Functions Core Tools 為 2.7.1846 版或更新版本。
1. 執行 `python --version` (Linux/MacOS) 或 `py --version` (Windows)，以確認您的 Python 版本回報為 3.7.x。

## <a name="clone-the-tutorial-repository"></a>複製教學課程存放庫

1. 在終端機或命令視窗中，使用 Git 複製下列存放庫：

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. 瀏覽至資料夾，並檢查其內容。

    ```
    cd functions-python-pytorch-tutorial
    ```

    - start  是教學課程的工作資料夾。
    - end  是供您參考的最終結果和完整實作。
    - resources  包含機器學習模型和協助程式庫。
    - frontend  是呼叫函式應用程式的網站。

## <a name="create-and-activate-a-python-virtual-environment"></a>建立並啟用 Python 虛擬環境

瀏覽至start  資料夾，並執行下列命令以建立並啟用名為 `.venv` 的虛擬環境。


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

如果 Python 未在您的 Linux 發行版本上安裝 venv 套件，請執行下列命令：

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
.venv\scripts\activate
```

---

您將在這個已啟用的虛擬環境中執行所有後續命令。 (若要退出虛擬環境，請執行 `deactivate`。)


## <a name="create-a-local-functions-project"></a>建立本機 Functions 專案

在 Azure Functions 中，函式專案是包含一或多個個別函式的容器，而每個函式分別會回應特定的觸發程序。 專案中的所有函式會共用相同的本機和裝載設定。 在本節中，您將建立一個函式專案，其中包含名為 `classify` 的單一重複使用函式，可提供 HTTP 端點。 您將在後續章節中新增更具體的程式碼。

1. 在 start  資料夾中，使用 Azure Functions Core Tools 來初始化 Python 函式應用程式：

    ```
    func init --worker-runtime python
    ```

    初始化之後，start  資料夾會包含專案的各種檔案，包括名為 [local.settings.json](functions-run-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的組態檔。 由於 *local.settings.json* 可能會包含從 Azure 下載的秘密，因此 *.gitignore* 檔案依預設會將該檔案排除在原始檔控制以外。

    > [!TIP]
    > 由於函式專案會繫結至特定執行階段，因此專案中的所有函式都必須以相同的語言撰寫。

1. 使用下列命令，將函式新增至您的專案，其中 `--name` 引數是函式的唯一名稱，而 `--template` 引數可指定函式的觸發程序。 `func new` 建立符合函式名稱的子資料夾，其中包含適合專案所選語言的程式碼檔案，以及名為 *function.json* 的組態檔。

    ```
    func new --name classify --template "HTTP trigger"
    ```

    此命令會建立符合函式名稱的資料夾，即 classify  。 該資料夾中有兩個檔案： *\_\_init\_\_.py* 包含函式程式碼，而 *function.json* 則說明函式的觸發程序及其輸入和輸出繫結。 如需這些檔案內容的詳細資訊，請參閱 Python 快速入門中的[檢查檔案內容](./create-first-function-cli-python.md#optional-examine-the-file-contents)。


## <a name="run-the-function-locally"></a>在本機執行函式

1. 啟動 start  資料夾中的本機 Azure Functions 執行階段主機，以啟動函式：

    ```
    func start
    ```

1. 如果您看到 `classify` 端點出現在輸出中，請瀏覽至 URL ```http://localhost:7071/api/classify?name=Azure```。 訊息 "Hello Azure!" 應該會出現在輸出中。

1. 使用 **Ctrl**-**C** 將主機停止。


## <a name="import-the-pytorch-model-and-add-helper-code"></a>匯入 PyTorch 模型並新增協助程式程式碼

若要修改 `classify` 函式以根據影像的內容來分類，您可以使用預先定型的 [ResNet](https://arxiv.org/abs/1512.03385) 模型。 預先定型的模型 (來自 [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/)) 會將影像分類為 1000 個 [ImageNet 類別](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a)中的其中 1 種。 接著，您可以將一些協助程式碼和相依性新增至您的專案。

1. 在 [start]  資料夾中執行下列命令，將預測程式碼和標籤複製到 [classify]  資料夾中。

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. 確認 classify  資料夾包含名為 predict.py  和 labels.txt  的檔案。 如果沒有，請確認您已執行 start  資料夾中的命令。

1. 在文字編輯器中開啟 start/requirements.txt  ，並新增協助程式程式碼所需相依性，如下所示：

    ```txt
    azure-functions
    requests
    -f https://download.pytorch.org/whl/torch_stable.html
    torch==1.5.0+cpu
    torchvision==0.6.0+cpu
    ```

1. 儲存 requirements.txt  ，然後從 [start]  資料夾執行下列命令，以安裝相依性。


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

安裝可能需要幾分鐘的時間，在這段期間，您可以繼續修改下一節中的函式。
> [!TIP]
> >在 Windows 上，如果檔案的路徑名稱較長 (例如 sharded_mutable_dense_hashtable.cpython-37.pyc  )，則可能會出現錯誤「無法安裝套件，因為發生 EnvironmentError：[Errno 2] 沒有此類檔案或目錄：」。 之所以發生此錯誤，通常是因為資料夾路徑的深度太長所致。 在此情況下，請將登錄機碼 `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` 設定為 `1`，以啟用長路徑。 或者，請檢查 Python 解譯器的安裝位置。 如果該位置具有長路徑，請嘗試在路徑較短的資料夾中重新安裝。

## <a name="update-the-function-to-run-predictions"></a>更新函式以執行預測

1. 在文字編輯器中開啟 *classify/\_\_init\_\_.py* ，然後在現有的 `import` 陳述式後面新增以下幾行，以匯入標準 JSON 程式庫和 predict  協助程式：

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. 將 `main` 函式的整個內容取代為下列程式碼：

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    此函式會在名為 `img` 的查詢字串參數中接收影像 URL。 接著會從協助程式程式庫呼叫 `predict_image_from_url`，以下載影像並使用 PyTorch 模型加以分類。 然後，函式會傳回包含結果的 HTTP 回應。

    > [!IMPORTANT]
    > 由於此 HTTP 端點會由裝載於另一個網域的網頁呼叫，因此回應會包含 `Access-Control-Allow-Origin` 標頭以符合瀏覽器的跨原始來源資源共用 (CORS) 需求。
    >
    > 在生產應用程式中，將 `*` 變更為網頁的特定來源以提高安全性。

1. 儲存您的變更，然後假設相依性已完成安裝，使用 `func start` 重新啟動本機函式主機。 請務必在已啟用虛擬環境的 start  資料夾中執行主機。 否則，主機將會啟動，但您會在叫用函式時會看到錯誤。

    ```
    func start
    ```

1. 在瀏覽器中開啟下列 URL，以使用伯恩山犬影像的 URL 叫用函式，並確認傳回的 JSON 將影像分類為伯恩山犬。

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. 讓主機保持執行狀態，因為您在後續步驟將會用到此主機。

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>執行本機 Web 應用程式前端以測試函式

若要測試從另一個 Web 應用程式叫用函式端點的功能，存放庫的 frontend  資料夾中有一個簡單的應用程式可供使用。

1. 開啟新的終端機或命令提示字元，並啟用虛擬環境 (如先前的[建立並啟用 Python 虛擬環境](#create-and-activate-a-python-virtual-environment)所說明)。

1. 瀏覽至存放庫的 frontend  資料夾。

1. 使用 Python 啟動 HTTP 伺服器：

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    python -m http.server
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. 在瀏覽器中瀏覽至 `localhost:8000`，然後在文字方塊中輸入下列其中一個相片 URL，或使用任何可公開存取之影像的 URL。

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. 選取 [提交]  叫用函式端點，以進行影像分類。

    ![螢幕擷取畫面：已完成的專案](media/machine-learning-pytorch/screenshot.png)

    如果瀏覽器在您提交影像 URL 時回報了錯誤，請檢查函式應用程式執行所在的終端機。 如果您看到「找不到模組 'PIL'」之類的錯誤，表示您可能未事先啟用先前建立的虛擬環境，即在 start  資料夾中啟動了函式應用程式。 如果您仍看到錯誤，請在啟用虛擬環境後再次執行 `pip install -r requirements.txt`，並觀察是否有錯誤。

## <a name="clean-up-resources"></a>清除資源

本教學課程全都會在機器本機上執行，因此並沒有需要清除的 Azure 資源或服務。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Azure Functions 來建置和自訂 HTTP API 端點，以使用 PyTorch 模型來分類影像。 您也已了解如何從 Web 應用程式呼叫 API。 不論多麼複雜的 API 都可以使用本教學課程的技術來建置，而且都在 Azure Functions 提供的無伺服器計算模型上執行。

另請參閱：

- [使用 Visual Studio Code 將函式部署至 Azure](https://code.visualstudio.com/docs/python/tutorial-azure-functions)。
- [Azure Functions Python 開發人員指南](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [使用 Azure CLI 將函式部署至 Azure Functions 的指南](./functions-run-local.md#publish)
