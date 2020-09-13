---
title: 設定自訂 Linux 容器
description: 瞭解如何在 Azure App Service 中設定自訂 Linux 容器。 本文說明最常見的設定工作。
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9a27abe5457cf8adf2963db545c629134ae53709
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566971"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>針對 Azure App Service 設定自訂 Linux 容器

本文說明如何將自訂 Linux 容器設定為在 Azure App Service 上執行。

本指南提供在 App Service 中容器化 Linux 應用程式的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循 [自訂容器快速入門](quickstart-custom-container.md?pivots=container-linux) 和 [教學](tutorial-custom-container.md?pivots=container-linux) 課程。 另外還有一個 [多容器應用程式快速入門](quickstart-multi-container.md) 和 [教學](tutorial-multi-container-app.md)課程。

## <a name="configure-port-number"></a>設定埠號碼

根據預設，App Service 會假設您的自訂容器正在接聽埠80。 您自訂映射中的網頁伺服器可能會使用80以外的埠。 您可以使用應用程式設定，告訴 Azure 您的自訂容器所使用的埠 `WEBSITES_PORT` 。 [本教學課程中的 Python 範例](https://github.com/Azure-Samples/docker-django-webapp-linux) GitHub 頁面說明您必須將 `WEBSITES_PORT` 設定為 _8000_。 您可以 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 在 Cloud Shell 中執行命令來設定它。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>設定環境變數

您的自訂容器可以使用需要在外部提供的環境變數。 您可以藉由在 Cloud Shell 中執行命令來傳遞它們 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

這個方法適用于單一容器應用程式或多容器應用程式，其中的環境變數是在 *>docker-compose.yml. yml* 檔案中指定。

## <a name="use-persistent-shared-storage"></a>使用持續性共用儲存體

您可以使用應用程式檔案系統中的 */home* 目錄，在重新開機時保存檔案，並在實例之間共用檔案。 `/home`提供應用程式中的，以讓您的容器應用程式存取持續性儲存體。

停用持續性儲存體時， `/home` 不會跨應用程式重新開機或跨多個實例保存寫入目錄。 唯一的例外狀況是 `/home/LogFiles` 用來儲存 Docker 和容器記錄檔的目錄。 啟用持續性儲存體時，會保存目錄的所有寫入， `/home` 並且可供相應放大應用程式的所有實例存取。

依預設，會 *啟用* 持續性儲存體，而不會在應用程式設定中公開設定。 若要停用，請 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 在 Cloud Shell 中執行命令來設定應用程式設定 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> 您也可以 [設定自己的持續性儲存體](configure-connect-to-azure-storage.md?pivots=platform-linux)。

## <a name="enable-ssh"></a>啟用 SSH

SSH 可讓容器和用戶端之間進行安全通訊。 為了讓自訂容器支援 SSH，您必須將它新增至 Dockerfile 本身。

> [!TIP]
> 所有內建 Linux 容器都已在其映射存放庫中新增 SSH 指令。 您可以使用 [Node.js 10.14 存放庫](https://github.com/Azure-App-Service/node/blob/master/10.14) 進行下列指示，以瞭解其如何在該處啟用。

- 使用 [執行](https://docs.docker.com/engine/reference/builder/#run) 指令來安裝 SSH 伺服器，並將根帳號的密碼設定為 `"Docker!"` 。 例如，針對以 [Alpine Linux](https://hub.docker.com/_/alpine)為基礎的映射，您需要下列命令：

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    此組態不允許容器的外部連線。 SSH 只能透過 `https://<app-name>.scm.azurewebsites.net` 發佈認證進行驗證。

- 將 [此 sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) 檔案新增至您的映射存放庫，並使用 [複製](https://docs.docker.com/engine/reference/builder/#copy) 指示將檔案複製到 */etc/ssh/* 目錄。 如需 *sshd_config* 檔案的詳細資訊，請參閱 [OpenBSD](https://man.openbsd.org/sshd_config)檔。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > sshd_config** 檔案必須包含下列項目︰
    > - `Ciphers` 必須在此清單中包含至少一個項目：`aes128-cbc,3des-cbc,aes256-cbc`。
    > - `MACs` 必須在此清單中包含至少一個項目：`hmac-sha1,hmac-sha1-96`。

- 使用 [公開](https://docs.docker.com/engine/reference/builder/#expose) 指令來開啟容器中的埠2222。 雖然已知根密碼，但無法從網際網路存取埠2222。 只有私人虛擬網路的橋接器網路內的容器可以存取它。

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 在您容器的啟動腳本中，啟動 SSH 伺服器。

    ```bash
    /usr/sbin/sshd
    ```

    如需範例，請參閱預設的 [Node.js 10.14 容器](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) 如何啟動 SSH 伺服器。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="change-the-docker-image-of-a-custom-container"></a>變更自訂容器的 Docker 映射

若要將現有的自訂容器應用程式從目前的 Docker 映射變更為新映射，請使用下列命令：

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>使用私人登錄中的映射

若要使用私人登錄中的映射（例如 Azure Container Registry），請執行下列命令：

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

針對 *\<username>* 和 *\<password>* ，提供私人登錄帳戶的登入認證。

## <a name="configure-multi-container-apps"></a>設定多容器應用程式

- [在 Docker Compose 中使用持續性儲存體](#use-persistent-storage-in-docker-compose)
- [預覽限制](#preview-limitations)
- [Docker Compose 選項](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>在 Docker Compose 中使用持續性儲存體

WordPress 等多容器應用程式需要持續性儲存體才能正常運作。 若要啟用它，您的 Docker Compose 設定必須指向容器 *以外* 的儲存位置。 容器內的儲存位置不會在應用程式重新開機後保存變更。

`WEBSITES_ENABLE_APP_SERVICE_STORAGE`使用 Cloud Shell 中的[az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令來設定應用程式設定，以啟用持續性儲存體。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

在您的 *>docker-compose.yml. yml* 檔案中，將 `volumes` 選項對應至 `${WEBAPP_STORAGE_HOME}` 。 

`WEBAPP_STORAGE_HOME` 是 App Service 中與您應用程式的永續性儲存體相對應的環境變數。 例如：

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>預覽限制

多容器目前為預覽狀態。 下列 App Service 平臺功能不受支援：

- 驗證 / 授權
- 受控識別
- CORS

### <a name="docker-compose-options"></a>Docker Compose 選項

下列清單顯示支援和不支援的 Docker Compose 設定選項：

#### <a name="supported-options"></a>支援的選項

- 命令
- entrypoint
- 環境
- image
- 連接埠
- restart
- 服務
- 磁碟區

#### <a name="unsupported-options"></a>不支援的選項

- build (不允許)
- depends_on (已忽略)
- networks (已忽略)
- secrets (已忽略)
- 80 和 8080 以外的連接埠 (已忽略)

> [!NOTE]
> 未明確呼叫的任何其他選項都會忽略公開預覽。

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [教學課程：從私人容器存放庫部署](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [教學課程：多容器 WordPress 應用程式](tutorial-multi-container-app.md)
