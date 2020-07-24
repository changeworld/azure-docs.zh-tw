---
title: 設定自訂的 Linux 容器
description: 瞭解如何在 Azure App Service 中設定自訂的 Linux 容器。 本文說明最常見的設定工作。
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: df766c289ac9ece4c1dc1fbdc65d49ae1306a592
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87008586"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>設定 Azure App Service 的自訂 Linux 容器

本文說明如何設定要在 Azure App Service 上執行的自訂 Linux 容器。

本指南提供在 App Service 中容器化 Linux 應用程式的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循[自訂容器快速入門](quickstart-docker-go.md)和[教學](tutorial-custom-docker-image.md)課程。 另外還有[多容器應用程式快速入門](quickstart-multi-container.md)和[教學](tutorial-multi-container-app.md)課程。

## <a name="configure-port-number"></a>設定埠號碼

根據預設，App Service 會假設您的自訂容器在埠80上接聽。 自訂映射中的 web 伺服器可能會使用80以外的埠。 您會使用應用程式設定，告訴 Azure 您的自訂容器所使用的埠 `WEBSITES_PORT` 。 [本教學課程中的 Python 範例](https://github.com/Azure-Samples/docker-django-webapp-linux) GitHub 頁面說明您必須將 `WEBSITES_PORT` 設定為 _8000_。 您可以藉由 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 在 Cloud Shell 中執行命令來設定它。 例如:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>設定環境變數

您的自訂容器可能會使用需要在外部提供的環境變數。 您可以藉由在 Cloud Shell 中執行命令，在中傳遞它們 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 。 例如:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

這個方法適用于單一容器應用程式或多容器應用程式，其中環境變數是在*docker-compose.dev.debug.yml. yml*檔案中指定。

## <a name="use-persistent-shared-storage"></a>使用持續性共用存放裝置

您可以使用應用程式檔案系統中的 */home*目錄，在重新開機期間保存檔案，並在實例之間共用檔案。 `/home`系統會提供您應用程式中的，讓您的容器應用程式能夠存取持續性儲存體。

停用持續性儲存體時， `/home` 不會跨應用程式重新開機或跨多個實例來保存目錄的寫入。 唯一的例外是 `/home/LogFiles` 用來儲存 Docker 和容器記錄檔的目錄。 啟用持續性儲存體時，會保存目錄的所有寫入， `/home` 並可供相應放大應用程式的所有實例存取。

根據預設，會*啟用*持續性儲存體，且不會在應用程式設定中公開設定。 若要停用它，請在 Cloud Shell 中執行命令，以設定 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 應用程式設定 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 。 例如:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> 您也可以[設定自己的持續性儲存體](how-to-serve-content-from-azure-storage.md)。

## <a name="enable-ssh"></a>啟用 SSH

SSH 可讓容器和用戶端之間進行安全通訊。 為了讓自訂容器支援 SSH，您必須將它新增至 Dockerfile 本身。

> [!TIP]
> 所有內建的 Linux 容器都已在其映射存放庫中新增 SSH 指示。 您可以使用[Node.js 10.14 存放庫](https://github.com/Azure-App-Service/node/blob/master/10.14)來執行下列指示，以瞭解其如何在該處啟用。

- 使用[執行](https://docs.docker.com/engine/reference/builder/#run)指令來安裝 SSH 伺服器，並將根帳號的密碼設定為 `"Docker!"` 。 例如，針對以[Alpine Linux](https://hub.docker.com/_/alpine)為基礎的映射，您需要下列命令：

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    此設定不允許對容器的外部連線。 SSH 只能透過 `https://<app-name>.scm.azurewebsites.net` 發佈認證進行驗證。

- 將[此 sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config)檔案新增至您的映射存放庫，並使用[複製](https://docs.docker.com/engine/reference/builder/#copy)指令將檔案複製到*到/etc/ssh/* 目錄。 如需*sshd_config*檔案的詳細資訊，請參閱[OpenBSD](https://man.openbsd.org/sshd_config)檔。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > sshd_config** 檔案必須包含下列項目︰
    > - `Ciphers` 必須在此清單中包含至少一個項目：`aes128-cbc,3des-cbc,aes256-cbc`。
    > - `MACs` 必須在此清單中包含至少一個項目：`hmac-sha1,hmac-sha1-96`。

- 使用[公開](https://docs.docker.com/engine/reference/builder/#expose)指令，在容器中開啟埠2222。 雖然已知根密碼，但無法從網際網路存取埠2222。 只有私人虛擬網路的橋接器網路中的容器可以存取它。

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 在容器的啟動腳本中，啟動 SSH 伺服器。

    ```bash
    /usr/sbin/sshd
    ```

    如需範例，請參閱預設[Node.js 10.14 容器](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh)如何啟動 SSH 伺服器。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>設定多容器應用程式

- [在 Docker Compose 中使用持續性儲存體](#use-persistent-storage-in-docker-compose)
- [預覽限制](#preview-limitations)
- [Docker Compose 選項](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>在 Docker Compose 中使用持續性儲存體

WordPress 之類的多容器應用程式需要持續性儲存體，才能正常運作。 若要啟用它，您的 Docker Compose 設定必須指向容器*外*的儲存位置。 容器內的儲存體位置不會保存應用程式重新開機之後的變更。

`WEBSITES_ENABLE_APP_SERVICE_STORAGE`使用 Cloud Shell 中的[az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令，藉由設定應用程式設定來啟用持續性儲存體。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

在您的*docker-compose.dev.debug.yml. yml*檔案中，將 `volumes` 選項對應至 `${WEBAPP_STORAGE_HOME}` 。 

`WEBAPP_STORAGE_HOME` 是 App Service 中與您應用程式的永續性儲存體相對應的環境變數。 例如:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>預覽限制

多容器目前為預覽狀態。 不支援下列 App Service 平臺功能：

- 驗證 / 授權
- 受控識別

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
> 公開預覽中會忽略未明確呼叫的任何其他選項。

## <a name="configure-vnet-integration"></a>設定 VNet 整合

使用具有 VNet 整合的自訂容器可能需要額外的容器設定。 請參閱[將您的應用程式與 Azure 虛擬網路整合](../web-sites-integrate-with-vnet.md)。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [教學課程：從私人容器存放庫部署](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [教學課程：多容器 WordPress 應用程式](tutorial-multi-container-app.md)
