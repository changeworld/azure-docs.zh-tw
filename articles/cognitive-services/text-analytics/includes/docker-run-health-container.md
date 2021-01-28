---
title: 執行 docker run 命令的容器範例
titleSuffix: Azure Cognitive Services
description: Health 容器的 Docker run 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: af8fec56c32b52e2af584e59f08db6cc7129c9c5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947357"
---
## <a name="install-the-container"></a>安裝容器

您可以透過多種方式來安裝和執行健康情況容器的文字分析。 

- 使用 [Azure 入口網站](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) 建立文字分析資源，並使用 Docker 來取得您的容器。
- 使用下列 PowerShell 和 Azure CLI 腳本，將資源部署和容器設定自動化。

### <a name="run-the-container-locally"></a>在本機執行容器

若要在下載容器映射之後于自己的環境中執行容器，請尋找其映射識別碼：
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

執行下列 `docker run` 命令。 以您自己的值取代下列預留位置：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | 文字分析資源的索引鍵。 您可以在資源的 [ **金鑰和端點** ] 頁面上找到 Azure 入口網站上的。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 用於存取文字分析 API 的端點。 您可以在資源的 [ **金鑰和端點** ] 頁面上找到 Azure 入口網站上的。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | 容器的映射識別碼。 | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | 容器的輸入目錄。 | Windows：`C:\healthcareMount` <br> Linux/MacOS： `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

此命令：

- 假設輸入目錄存在於主機電腦上
- 從容器映射執行健康情況容器的文字分析
- 配置6個 CPU 核心和 12 gb (GB) 的記憶體
- 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
- 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

### <a name="demo-ui-to-visualize-output"></a>以視覺化方式呈現輸出的示範 UI

> [!NOTE]
> 此示範僅適用于 health 容器的文字分析。

容器會提供以 REST 為基礎的查詢預測端點 API。  我們也提供容器中的視覺效果工具，可透過附加 `/demo` 至容器的端點來存取。 例如：

```
http://<serverURL>:5000/demo
```

使用下面的範例捲曲要求，將查詢提交至您已部署的容器， `serverURL` 並將變數取代為適當的值。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>使用 Azure 用於容器的 Web App 安裝容器

Azure [用於容器的 Web App](https://azure.microsoft.com/services/app-service/containers/) 是專門用來在雲端中執行容器的 azure 資源。 它帶來現成的功能，例如自動調整、支援 docker 容器和 docker 撰寫、HTTPS 支援，以及其他更多功能。

> [!NOTE]
> 使用 Azure Web 應用程式時，您將會自動取得的網域格式為 `<appservice_name>.azurewebsites.net`

使用 Azure CLI 來執行此 PowerShell 腳本，以使用您的訂用帳戶和透過 HTTPS 的容器映射來建立用於容器的 Web App。 請先等候腳本完成 (大約25-30 分鐘) ，然後再提交第一個要求。

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>使用 Azure 容器實例安裝容器

您也可以使用 Azure 容器實例 (ACI) 讓部署更容易。 ACI 是一項資源，可讓您在受控、無伺服器的 Azure 環境中，視需要執行 Docker 容器。 

請參閱 [如何使用 Azure 容器實例](../../containers/azure-container-instance-recipe.md) ，以取得使用 AZURE 入口網站部署 ACI 資源的步驟。 您也可以使用下列 PowerShell 腳本搭配 Azure CLI，這會使用容器映射在您的訂用帳戶上建立 ACI。  請先等候腳本完成 (大約25-30 分鐘) ，然後再提交第一個要求。  由於每個 ACI 資源的 Cpu 數目上限限制，如果您想要提交5個以上的大型檔，請不要選取此選項， (大約5000個字元) 每個要求。
如需可用性資訊，請參閱 [ACI 區域支援](../../../container-instances/container-instances-region-availability.md) 文章。 

> [!NOTE] 
> Azure 容器實例不包含內建網域的 HTTPS 支援。 如果您需要 HTTPS，您將需要手動設定它，包括建立憑證和註冊網域。 您可以使用下面的 NGINX 找到相關指示。

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>安全 ACI 連線能力

依預設，使用 ACI 搭配容器 API 時，不會提供任何安全性。 這是因為容器通常會作為 pod 的一部分來執行，而該 pod 是由網路橋接外部保護。 不過，您可以修改具有 front 元件的容器，讓容器端點保持私用。 下列範例會使用 [NGINX](https://www.nginx.com) 做為輸入閘道，以支援 HTTPS/SSL 和用戶端憑證驗證。

> [!NOTE]
> NGINX 是開放原始碼、高效能的 HTTP 伺服器和 proxy。 NGINX 容器可以用來終止單一容器的 TLS 連線。 也可以更複雜的 NGINX 輸入型 TLS 終止解決方案。

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>將 NGINX 設定為輸入閘道

NGINX 會在執行時間使用 [設定檔](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) 來啟用功能。 若要啟用另一個服務的 TLS 終止，您必須指定 SSL 憑證來終止 TLS 連線，並  `proxy_pass` 指定服務的位址。 以下提供範例。


> [!NOTE]
> `ssl_certificate` 需要在 NGINX 容器的本機檔案系統內指定路徑。 指定的位址 `proxy_pass` 必須可從 NGINX 容器的網路中取得。

NGINX 容器會將中裝載的所有檔案載入 `_.conf_` `/etc/nginx/conf.d/` 至 HTTP 設定路徑。

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Docker 撰寫檔案範例

下列範例顯示如何建立 [docker 撰寫](https://docs.docker.com/compose/reference/overview) 檔案，以部署適用于健康情況容器的 NGINX 和文字分析：

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

若要起始此 Docker 撰寫檔案，請從位於檔案根目錄層級的主控台中執行下列命令：

```bash
docker-compose up
```

如需詳細資訊，請參閱有關 [NGINX SSL 終止](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)的 NGINX 檔。