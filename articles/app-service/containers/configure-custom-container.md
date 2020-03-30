---
title: 配置自訂 Linux 容器
description: 瞭解如何在 Azure 應用服務中配置自訂 Linux 容器。 本文說明最常見的設定工作。
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280140"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>為 Azure 應用服務配置自訂 Linux 容器

本文介紹如何配置自訂 Linux 容器以在 Azure 應用服務上運行。

本指南提供了應用服務中 Linux 應用容器化的關鍵概念和說明。 如果您從未使用過 Azure 應用服務，請先按照[自訂容器快速入門](quickstart-docker-go.md)和[教程](tutorial-custom-docker-image.md)操作。 還有一個[多容器應用程式快速啟動](quickstart-multi-container.md)和[教程](tutorial-multi-container-app.md)。

## <a name="configure-port-number"></a>配置埠號

自訂映射中的 Web 服務器可能使用 80 以外的埠。 使用`WEBSITES_PORT`應用設置向 Azure 介紹自訂容器使用的埠。 [本教學課程中的 Python 範例](https://github.com/Azure-Samples/docker-django-webapp-linux) GitHub 頁面說明您必須將 `WEBSITES_PORT` 設定為 _8000_。 您可以通過在雲殼中運行[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令來設置它。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>設定環境變數

自訂容器可能使用需要外部提供的環境變數。 您可以通過在雲殼中運行[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令來傳遞它們。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

此方法適用于單容器應用或多容器應用，其中環境變數在*docker-compose.yml*檔中指定。

## <a name="use-persistent-shared-storage"></a>使用持久共用存儲

您可以使用應用檔案系統中的 */home*目錄在重新開機期間保留檔並在實例之間共用檔。 提供`/home`應用中的 ，以使容器應用能夠訪問持久存儲。

禁用持久存儲後，不會跨應用重新開機或`/home`跨多個實例持久化寫入目錄。 唯一的例外是`/home/LogFiles`目錄，用於存儲 Docker 和容器日誌。 啟用持久存儲後，將保留對`/home`目錄的所有寫入，並可以由橫向擴展應用的所有實例訪問。

預設情況下，*已啟用*持久存儲，並且該設置不會在"應用程式設定"中公開。 要禁用它，請通過在`WEBSITES_ENABLE_APP_SERVICE_STORAGE`雲外殼中運行[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令來設置應用設置。 例如：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> 您還可以[配置自己的持久存儲](how-to-serve-content-from-azure-storage.md)。

## <a name="enable-ssh"></a>啟用 SSH

SSH 可讓容器和用戶端之間進行安全通訊。 為了使自訂容器支援 SSH，必須將其添加到 Dockerfile 本身。

> [!TIP]
> 所有內置 Linux 容器都在其映射存儲庫中添加了 SSH 指令。 您可以使用[Node.js 10.14 存儲庫](https://github.com/Azure-App-Service/node/blob/master/10.14)執行以下說明，瞭解該存儲庫的啟用方式。

- 使用[RUN](https://docs.docker.com/engine/reference/builder/#run)指令安裝 SSH 伺服器並將根帳號的密碼設置為`"Docker!"`。 例如，對於基於[阿爾卑斯 Linux](https://hub.docker.com/_/alpine)的圖像，您需要以下命令：

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    此配置不允許與容器的外部連接。 SSH 只能通過發佈憑據`https://<app-name>.scm.azurewebsites.net`進行身份驗證。

- [將此sshd_config檔](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config)添加到映射存儲庫，並使用[COPY](https://docs.docker.com/engine/reference/builder/#copy)指令將該檔案複製到 */etc/ssh/* 目錄。 有關*sshd_config*檔的詳細資訊，請參閱[OpenBSD 文檔](https://man.openbsd.org/sshd_config)。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > sshd_config** 檔案必須包含下列項目︰
    > - `Ciphers` 必須在此清單中包含至少一個項目：`aes128-cbc,3des-cbc,aes256-cbc`。
    > - `MACs` 必須在此清單中包含至少一個項目：`hmac-sha1,hmac-sha1-96`。

- 使用[EXPOSE](https://docs.docker.com/engine/reference/builder/#expose)指令在容器中打開端口 2222。 雖然根密碼已知，但埠 2222 可從 Internet 訪問。 它只能由專用虛擬網路橋網中的容器訪問。

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 在容器的啟動腳本中，啟動 SSH 伺服器。

    ```bash
    /usr/sbin/sshd
    ```

    例如，請參閱預設[Node.js 10.14 容器](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh)如何啟動 SSH 伺服器。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>配置多容器應用

- [在 Docker 撰寫中使用持久存儲](#use-persistent-storage-in-docker-compose)
- [預覽限制](#preview-limitations)
- [Docker 組合選項](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>在 Docker 撰寫中使用持久存儲

WordPress 等多容器應用需要持久存儲才能正常運行。 要啟用它，Docker 合成配置必須指向容器*外部*的存儲位置。 容器內的存儲位置不會保留超出應用重新開機的更改。

使用雲殼中的 az `WEBSITES_ENABLE_APP_SERVICE_STORAGE` [Webapp 配置應用設置集](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令設置應用設置，啟用持久存儲。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

在*docker-compose.yml*檔中，將`volumes`選項映射到`${WEBAPP_STORAGE_HOME}`。 

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

多容器當前處於預覽狀態。 不支援以下應用服務平臺功能：

- 驗證 / 授權
- 受控識別

### <a name="docker-compose-options"></a>Docker 組合選項

以下清單顯示受支援且不支援的 Docker 組合配置選項：

#### <a name="supported-options"></a>支援的選項

- 命令
- entrypoint
- Environment
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
> 在"公共預覽"中，將忽略未明確調用的任何其他選項。

## <a name="configure-vnet-integration"></a>配置 VNet 集成

使用具有 VNet 集成的自訂容器可能需要其他容器配置。 請參閱[將您的應用程式與 Azure 虛擬網路整合](../web-sites-integrate-with-vnet.md)。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教程：從專用容器存儲庫進行部署](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [教程：多容器 WordPress 應用程式](tutorial-multi-container-app.md)
