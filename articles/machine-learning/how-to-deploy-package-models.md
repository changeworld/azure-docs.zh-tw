---
title: 套件模型
titleSuffix: Azure Machine Learning
description: 將模型封裝為 Dockerfile
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: edaae4f4c06f038d12267e35a2c356af430e1555
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998831"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>如何使用 Docker 封裝已註冊的模型

本文說明如何使用 Docker 封裝已註冊的 Azure Machine Learning 模型。

## <a name="prerequisites"></a>必要條件

本文假設您已在機器學習服務工作區中訓練並註冊模型。 若要瞭解如何定型和註冊 scikit-learn 學習模型，請 [遵循本教學](how-to-train-scikit-learn.md)課程。


## <a name="package-models"></a>套件模型

在某些情況下，您可能會想要在不部署模型的情況下建立 Docker 映射 (例如，您是否打算 [部署至 Azure App Service](how-to-deploy-app-service.md)) 。 或者，您可能想要下載映射並在本機 Docker 安裝上執行。 您甚至可能會想要下載用來建立映射、加以檢查、修改，以及手動建立映射的檔案。

模型封裝可讓您進行這些作業。 它會封裝將模型裝載為 web 服務所需的所有資產，並可讓您下載完全建立的 Docker 映射或建立檔案所需的檔案。 有兩種方式可以使用模型封裝：

**下載封裝的模型：** 下載 Docker 映射，其中包含將其裝載為 web 服務所需的模型和其他檔案。

**產生 Dockerfile：** 下載 Dockerfile、模型、輸入腳本，以及建立 Docker 映射所需的其他資產。 然後，您可以在本機建立映射之前，先檢查檔案或進行變更。

這兩個套件都可以用來取得本機 Docker 映射。

> [!TIP]
> 建立封裝類似于部署模型。 您可以使用已註冊的模型和推斷設定。

> [!IMPORTANT]
> 若要下載完全建立的映射或在本機建立映射，您必須在開發環境中安裝 [Docker](https://www.docker.com) 。

### <a name="download-a-packaged-model"></a>下載封裝的模型

下列範例會建立映射，此映射會在您工作區的 Azure container registry 中註冊：

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

建立封裝之後，您可以使用 `package.pull()` 將映射提取至您的本機 Docker 環境。 此命令的輸出會顯示映射的名稱。 例如： 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

下載模型之後，請使用 `docker images` 命令來列出本機映射：

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

若要根據此映射啟動本機容器，請使用下列命令，從 shell 或命令列啟動命名的容器。 將值取代為 `<imageid>` 命令所傳回的映射識別碼 `docker images` 。

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

此命令會啟動名為的映射的最新版本 `myimage` 。 它會將本機埠6789對應至 web 服務正在接聽 (5001) 之容器中的埠。 它也會將名稱指派 `mycontainer` 給容器，讓容器更容易停止。 容器啟動之後，您可以將要求提交至 `http://localhost:6789/score` 。

### <a name="generate-a-dockerfile-and-dependencies"></a>產生 Dockerfile 和相依性

下列範例顯示如何下載 Dockerfile、模型，以及在本機建立映射所需的其他資產。 `generate_dockerfile=True`參數表示您想要檔案，而不是完整建立的映射。

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

此程式碼會下載將映射建立至目錄所需的檔案 `imagefiles` 。 儲存的檔案中包含的 Dockerfile 會參考儲存在 Azure container registry 中的基底映射。 當您在本機 Docker 安裝上建立映射時，您必須使用位址、使用者名稱和密碼來向登錄進行驗證。 使用下列步驟，使用本機 Docker 安裝來建立映射：

1. 從 shell 或命令列會話，使用下列命令來向 Azure container registry 驗證 Docker。 `<address>`以抓取 `<username>` 的值取代、和 `<password>` `package.get_container_registry()` 。

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. 若要建立映射，請使用下列命令。 取代 `<imagefiles>` 為儲存檔案的目錄路徑 `package.save()` 。

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    此命令會將映射名稱設定為 `myimage` 。

若要驗證映射是否已建立，請使用 `docker images` 命令。 您應該會 `myimage` 在清單中看到影像：

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

若要根據此映射啟動新的容器，請使用下列命令：

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

此命令會啟動名為的映射的最新版本 `myimage` 。 它會將本機埠6789對應至 web 服務正在接聽 (5001) 之容器中的埠。 它也會將名稱指派 `mycontainer` 給容器，讓容器更容易停止。 容器啟動之後，您可以將要求提交至 `http://localhost:6789/score` 。

### <a name="example-client-to-test-the-local-container"></a>測試本機容器的範例用戶端

下列程式碼是可搭配容器使用的 Python 用戶端範例：

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

如需其他程式設計語言的用戶端範例，請參閱取用 [部署為 web 服務的模型](how-to-consume-web-service.md)。

### <a name="stop-the-docker-container"></a>停止 Docker 容器

若要停止容器，請從不同的 shell 或命令列使用下列命令：

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>後續步驟

* [針對失敗的部署進行疑難排解](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [建立用戶端應用程式以使用 web 服務](how-to-consume-web-service.md)
* [更新 Web 服務](how-to-deploy-update-web-service.md)
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [建立模型部署的事件警示和觸發程式](how-to-use-event-grid.md)
