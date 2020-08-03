---
title: 使用 Docker Compose
description: 如何在具有 Azure CLI 的 Linux 虛擬機器上安裝和使用 Docker 並加以撰寫。
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/14/2019
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: e9f824a3b7e1e5e482bbc04c448ca4930bf88678
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500941"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>在 Azure 中開始使用 Docker 和 Compose 定義並執行多容器應用程式
使用 [[撰寫](https://github.com/docker/compose)] 時，您可以使用簡單的文字檔來定義由多個 Docker 容器所組成的應用程式。 接著，您可以透過單一命令來啟動應用程式，此命令會執行所需的一切準備工作，以部署您的已定義環境。 舉例來說，本文將說明如何藉由 Ubuntu VM 上的後端 MariaDB SQL 資料庫來快速設定 WordPress 部落格。 您也可以使用 Compose 來設定更複雜的應用程式。

本文最後一次測試是在 2019 年 2 月 14 日，使用 [Azure Cloud Shell](https://shell.azure.com/bash) 和 [Azure CLI](/cli/azure/install-azure-cli) 2.0.58 版進行的。

## <a name="create-docker-host-with-azure-cli"></a>使用 Azure CLI 建立 Docker 主機
請安裝最新的 [Azure CLI](/cli/azure/install-az-cli2)，並使用 [az login](/cli/azure/reference-index) 來登入 Azure 帳戶。

首先，使用 [az group create](/cli/azure/group) 建立 Docker 環境的資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

建立名為 cloud-init.txt** 的檔案並貼上下列組態。 輸入 `sensible-editor cloud-init.txt` 可建立檔案，並查看可用的編輯器清單。 

```yaml
#include https://get.docker.com
```

現在，使用 [az vm create](/cli/azure/vm#az-vm-create) 建立 VM。 使用 `--custom-data` 參數以傳入 cloud-init 組態檔。 如果您將檔案儲存於目前工作目錄之外的位置，請提供 cloud-init.txt 組態的完整路徑。 下列範例會建立名為 *myDockerVM* 的 VM，並針對 Web 流量開啟連接埠 80。

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

系統需要花幾分鐘的時間來建立 VM、安裝封裝和啟動應用程式。 在 Azure CLI 將您返回提示字元之後，背景工作會繼續執行。 建立 VM 之後，請注意 Azure CLI 所顯示的 `publicIpAddress`。 

                 

## <a name="install-compose"></a>安裝 Compose


以 SSH 連線到新的 Docker 主機 VM。 提供您自己的 IP 位址。

```bash
ssh azureuser@10.10.111.11
```

在 VM 上安裝 Compose。

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>建立 docker-compose.yml 組態檔
建立 `docker-compose.yml` 設定檔，以定義要在 VM 上執行的 Docker 容器。 此檔案會指定要在每個容器上執行的映像、必要的環境變數和相依性、連接埠，以及容器之間的連結。 如需有關 yml 檔案語法的詳細資料，請參閱 [Compose file reference (Compose 檔案參考)](https://docs.docker.com/compose/compose-file/)。

建立 *docker-compose.yml* 檔案。 使用慣用的文字編輯器將一些資料新增至檔案。 下列範例會建立提示 `sensible-editor` 挑選您要使用之編輯器的檔案。

```bash
sensible-editor docker-compose.yml
```

將下列範例貼入 Docker Compose 檔案。 此組態會使用來自 [DockerHub 登錄](https://registry.hub.docker.com/_/wordpress/) 的映像，安裝 WordPress (開放原始碼部落格和內容管理系統) 和連結的後端 MariaDB SQL 資料庫。 輸入您自已的 *MYSQL_ROOT_PASSWORD*。

```yml
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>以 Compose 啟動容器
在與您 docker-compose.yml** 檔案相同的目錄中，執行下列命令 (根據您的環境，您可能需要使用 `sudo` 執行 `docker-compose`)：

```bash
sudo docker-compose up -d
```

這個命令會啟動 docker-compose.yml** 中指定的 Docker 容器。 此步驟需要幾分鐘的時間來完成。 您會看到類似以下的輸出：

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


若要確認容器是否已啟動，請輸入 `sudo docker-compose ps`。 您應該會看到如下的內容：

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

您現在可以在 VM 的連接埠 80 上直接連線到 WordPress。 開啟網頁瀏覽器並輸入您 VM 的 IP 位址名稱。 您現在應該會看到 WordPress 起始畫面，您可以在其中完成安裝，然後開始使用此應用程式。

![WordPress 起始畫面](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>後續步驟
* 如需更多建置和部署多容器應用程式的範例，請參閱 [Compose command-line reference (Compose 命令列參考)](https://docs.docker.com/compose/reference/) 和[使用者指南](https://docs.docker.com/compose/)。
* 使用 Azure 資源管理員範本 (您自己的範本或 [社群](https://azure.microsoft.com/documentation/templates/)提供的範本) 部署包含 Docker 的 Azure VM，以及使用 Compose 設定的應用程式。 例如， [以 Docker 部署 WordPress 部落格](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) 範本使用 Docker 和 Compose，藉由 Ubuntu VM 上的 MySQL 後端快速部署 WordPress。
* 嘗試整合 Docker Compose 與 Docker Swarm 叢集。 如需案例，請參閱[搭配使用 Compose 與 Swarm (英文)](https://docs.docker.com/compose/swarm/)。
