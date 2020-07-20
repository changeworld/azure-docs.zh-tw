---
title: 教學課程：建置和執行自訂映像
description: 了解如何建置可在 Azure App Service 上執行的自訂 Linux 映像、將其部署至 Azure Container Registry，然後在 App Service 中加以執行。
keywords: azure app service, web 應用程式, linux, docker, 容器
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seodec18, tracking-python
ms.openlocfilehash: 88ca971986119b3612c79d0bee381d3a0fc9a977
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84906831"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>教學課程：建置自訂映像，並從私人登錄在 App Service 中執行

[App Service](app-service-linux-intro.md) 在 Linux 上提供內建的 Docker 映像，且支援特定的版本，例如 PHP 7.3 和 Node.js 10.14。 App Service 會使用 Docker 容器技術，來裝載內建映像和自訂映像作為平台即服務。 在此教學課程中，您將了解如何建置自訂映像，並在 App Service 中執行它。 當內建的映像不包含您所選擇的語言，或當您應用程式所需的特定組態未在內建的映像中提供時，此模式相當有用。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 將自訂映像部署到私人容器登錄中
> * 在 App Service 中執行自訂映像
> * 設定環境變數
> * 更新和重新部署映像
> * 存取診斷記錄
> * 使用 SSH 連線到容器

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要：

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>下載範例

在終端機視窗中執行下列命令，將範例應用程式存放庫複製到本機電腦，然後變更為包含範例程式碼的目錄。

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>從 Docker 檔案建立映像

在 Git 存放庫中，看看 _Dockerfile_。 此檔案會描述執行您應用程式所需的 Python 環境。 此外，映像會設定 [SSH](https://www.ssh.com/ssh/protocol/) 伺服器，以在容器與主機之間進行安全通訊。 _Dockerfile_ 的最後一行 `ENTRYPOINT ["init.sh"]` 會叫用 `init.sh` 來啟動 SSH 服務和 Python 伺服器。

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#service SSH start
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

使用 `docker build` 命令建置 Docker 映像。

```bash
docker build --tag mydockerimage .
```

測試組建運作的方式是執行 Docker 容器。 發出 [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) 命令，並將映像的名稱和標記傳遞給它。 請務必使用 `-p` 引數來指定連接埠。

```bash
docker run -p 8000:8000 mydockerimage
```

驗證 web 應用程式和容器是否會正常運作，方法是瀏覽至 `http://localhost:8000`。

![在本機測試 Web 應用程式](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>將應用程式部署到 Azure

若要建立一個使用您剛才建立之映像的應用程式，請執行 Azure CLI 命令建立資源群組、推送映像，然後建立 App Service 方案 Web 應用程式來執行它。

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry

在 Cloud Shell 中，使用 [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) 命令來建立 Azure Container Registry。

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>登入 Azure Container Registry

若要將映像推送至登錄，您需要向私人登錄進行驗證。 在 Cloud Shell 中，使用 [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) 命令從您所建立的登錄中擷取認證。

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

輸出會顯示兩個密碼及使用者名稱。

<pre>
{
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "&lt;registry-username&gt;"
}
</pre>

使用 `docker login` 命令從本機終端機視窗登入 Azure Container Registry，如下列範例所示。 將 *\<azure-container-registry-name>* 和 *\<registry-username>* 取代為登錄的值。 出現提示時，輸入上一個步驟中的其中一個密碼。

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

確認登入成功。

### <a name="push-image-to-azure-container-registry"></a>將映像推送至 Azure Container Registry

標記 Azure Container Registry 的本機映像。 例如：
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

使用 `docker push` 命令來推送映像。 以登錄名稱再接著映像名稱和標籤來標記映像。

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

在 Cloud Shell 中，確認推送已成功。

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

您應該會取得下列輸出。

<pre>
[
  "mydockerimage"
]
</pre>

### <a name="create-app-service-plan"></a>建立 App Service 方案

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>建立 Web 應用程式

在 Cloud Shell 中，使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 命令，在 `myAppServicePlan` App Service 方案中建立 [Web 應用程式](app-service-linux-intro.md)。 使用唯一的應用程式名稱取代 _\<app-name>_ ，並將 _\<azure-container-registry-name>_ 取代為您的登錄名稱。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的輸出：

<pre>
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="configure-registry-credentials-in-web-app"></a>在 Web 應用程式中設定登錄認證

若要使 App Service 提取私人映像，它需要有關您的登錄和映像的相關資訊。 在 Cloud Shell 中，為他們提供 [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) 命令。 取代 *\<app-name>* 、 *\<azure-container-registry-name>* 、 _\<registry-username>_ 和 _\<password>_ 。

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> 使用 Docker Hub 以外的登錄時，`--docker-registry-server-url` 必須格式化為 `https://`，後面加上登錄的完整網域名稱。
>

### <a name="configure-environment-variables"></a>設定環境變數

大部分的 Docker 映像會使用自訂環境變數，例如 80 以外的連接埠。 可告訴 App Service 您映像所使用的連接埠，方法是使用 `WEBSITES_PORT` 應用程式設定。 [本教學課程中的 Python 範例](https://github.com/Azure-Samples/docker-django-webapp-linux) GitHub 頁面說明您必須將 `WEBSITES_PORT` 設定為 _8000_。

若要設定應用程式的設定，請在 Cloud Shell 中使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令。 應用程式設定為區分大小寫和空格分隔。

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>測試 Web 應用程式

瀏覽至 Web 應用程式以確認它可運作 (`http://<app-name>.azurewebsites.net`)。

> [!NOTE]
> 第一次存取應用程式時，可能需要一些時間，因為 App Service 必須提取整個映像。 如果瀏覽器逾時，只需重新整理頁面即可。

![測試 web 應用程式連接埠設定](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>變更 Web 應用程式並重新部署

在您的本機 Git 存放庫中，開啟 app/templates/app/index.html。 變更第一個 HTML 元素以符合下列程式碼。

```html
<nav class="navbar navbar-inverse navbar-fixed-top">
  <div class="container">
    <div class="navbar-header">
      <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
    </div>
  </div>
</nav>
```

儲存變更之後，請使用您稍早在本教學課程中使用的相同命令來重建和推送新的 Docker 映像。 您可以參考[從 Docker 檔案建立映像](#build-the-image-from-the-docker-file)和[將映像推送至 Azure Container Registry](#push-image-to-azure-container-registry)。

推送新的映像之後，請使用下列命令重新啟動 Web 應用程式，讓變更生效：

```azurecli-interactive
az webapp restart --name <app_name> --resource-group myResourceGroup
```

以稍早所使用的特定名稱取代 `<app_name>`。

重新啟動應用程式後，請遵循[測試 Web 應用程式](#test-the-web-app)中的指示進行測試。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="enable-ssh-connections"></a>啟用 SSH 連線

SSH 可讓容器和用戶端之間進行安全通訊。 若要啟用與容器的 SSH 連線，則必須設定它的自訂映像。 讓我們看一下已經具有必要設定的範例存放庫。

* 在 [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile) 中，下列程式碼會安裝 SSH 伺服器，並也會設定登入認證。

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > 此設定不允許容器的外部連線。 SSH 只能透過 Kudu/SCM 站台提供。 Kudu/SCM 站台會向 Azure 帳戶進行驗證。

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) 會將存放庫中的 [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) 檔案複製到 /etc/ssh/ 目錄。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) 會公開容器中的連接埠 2222。 它是供內部使用的連接埠，只有私人虛擬網路之橋接網路內的容器可以存取。 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* [輸入指令碼](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5)會啟動 SSH 伺服器。

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>開啟對容器的 SSH 連線

SSH 連線只能透過 Kudu 站台提供，可在 `https://<app-name>.scm.azurewebsites.net` 存取。

若要連線，請瀏覽至 `https://<app-name>.scm.azurewebsites.net/webssh/host` 並以您的 Azure 帳戶登入。

然後，系統會將您重新導向至一個顯示互動式主控台的頁面。

您需要確認特定應用程式正在容器中執行。 若要檢查容器並確認執行的流程，在提示字元提出 `top` 命令。

```bash
top
```

`top` 命令會公開容器中所有執行中的程序。

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

恭喜！ 您已在 App Service 中設定自訂的 Linux 容器。

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>後續步驟

您已了解如何︰

> [!div class="checklist"]
> * 將自訂映像部署到私人容器登錄中
> * 在 App Service 中執行自訂映像
> * 設定環境變數
> * 更新和重新部署映像
> * 存取診斷記錄
> * 使用 SSH 連線到容器

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至應用程式。

> [!div class="nextstepaction"]
> [教學課程：將自訂 DNS 名稱對應至應用程式](../app-service-web-tutorial-custom-domain.md)

或者，查看其他資源：

> [!div class="nextstepaction"]
> [設定自訂容器](configure-custom-container.md)

> [!div class="nextstepaction"]
> [教學課程：多容器 WordPress 應用程式](tutorial-multi-container-app.md)
