---
title: 使用側車容器啟用 SSL
description: 通過在側車容器中運行 Nginx，為在 Azure 容器實例中運行的容器組創建 SSL 或 TLS 終結點
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 43b39c7c13d6d5e52aae2ce1706e4880ab27d225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294951"
---
# <a name="enable-an-ssl-endpoint-in-a-sidecar-container"></a>在側車容器中啟用 SSL 終結點

本文演示如何使用應用程式容器和運行 SSL 提供程式的側車容器創建[容器組](container-instances-container-groups.md)。 通過設置具有單獨 SSL 終結點的容器組，可以為應用程式啟用 SSL 連線，而無需更改應用程式代碼。

設置由兩個容器組成的示例容器組：
* 使用公共 Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld)映射運行簡單 Web 應用程式的應用程式容器。 
* 運行公共[Nginx](https://hub.docker.com/_/nginx)映射的側車容器，配置為使用 SSL。 

在此示例中，容器組僅公開 Nginx 的埠 443 及其公共 IP 位址。 Nginx 將 HTTPS 請求路由到配套 Web 應用，該應用在埠 80 上內部偵聽。 您可以為在其他埠上偵聽的容器應用調整示例。 

有關在容器組中啟用 SSL 的其他方法，請參閱[後續步驟](#next-steps)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成本文。 如果您想要在本機使用，建議使用 2.0.55 版或更新版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-self-signed-certificate"></a>建立自我簽署憑證

要將 Nginx 設置為 SSL 提供程式，您需要 SSL 憑證。 本文演示如何創建和設置自簽名 SSL 憑證。 對於生產方案，應從憑證授權單位獲取證書。

要創建自簽名 SSL 憑證，請使用 Azure 雲外殼和許多 Linux 發行版本中可用的[OpenSSL](https://www.openssl.org/)工具，或在作業系統中使用可比較的用戶端工具。

首先在本地工作目錄中創建證書請求 （.csr 檔）：

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

按照提示添加標識資訊。 對於通用名稱，輸入與證書關聯的主機名稱。 當提示輸入密碼時，按"輸入而不鍵入"，以跳過添加密碼。

運行以下命令從證書請求創建自簽章憑證 （.crt 檔）。 例如：

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

現在，您應該在目錄中看到三個檔：證書請求 （）、`ssl.csr`私密金鑰 （）`ssl.key`和自簽章憑證 （）。`ssl.crt` 在`ssl.key`後續步驟`ssl.crt`中使用和後續步驟。

## <a name="configure-nginx-to-use-ssl"></a>將 Nginx 配置為使用 SSL

### <a name="create-nginx-configuration-file"></a>創建 Nginx 設定檔

在本節中，您將創建一個設定檔，供 Nginx 使用 SSL。 首先將以下文本複製到名為`nginx.conf`的新檔中。 在 Azure 雲外殼中，可以使用視覺化工作室代碼在工作目錄中創建檔：

```console
code nginx.conf
```

在`location`中，請確保使用`proxy_pass`應用的正確埠進行設置。 在此示例中，我們為`aci-helloworld`容器設置了埠 80。

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64 編碼機密和設定檔

Base64 編碼 Nginx 設定檔、SSL 憑證和 SSL 金鑰。 在下一節中，您將在用於部署容器組的 YAML 檔中輸入編碼的內容。

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>部署容器組

現在通過在[YAML 檔中](container-instances-multi-container-yaml.md)指定容器配置來部署容器組。

### <a name="create-yaml-file"></a>創建 YAML 檔

將以下 YAML 複製到名為`deploy-aci.yaml`的新檔中。 在 Azure 雲外殼中，可以使用視覺化工作室代碼在工作目錄中創建檔：

```console
code deploy-aci.yaml
```

輸入 base64 編碼檔的內容，在 中`secret`指示。 例如，`cat`每個基64編碼的檔以查看其內容。 在部署期間，這些檔將添加到容器組中[的機密卷](container-instances-volume-secret.md)中。 在此示例中，機密卷被裝載到 Nginx 容器中。

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>部署容器群組

使用[az 組創建命令創建資源組](/cli/azure/group#az-group-create)：

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

使用[az 容器創建](/cli/azure/container#az-container-create)命令部署容器組，將 YAML 檔作為參數傳遞。

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>檢視部署狀態

若要檢視部署的狀態，請使用下列 [az container show](/cli/azure/container#az-container-show) 命令：

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

對於成功部署，輸出類似于以下內容：

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>驗證 SSL 連線

使用瀏覽器導航到容器組的公共 IP 位址。 此示例中顯示的 IP 位址為`52.157.22.76`，因此 URL**https://52.157.22.76**為 。 由於 Nginx 伺服器配置，您必須使用 HTTPS 查看正在運行的應用程式。 嘗試通過 HTTP 連接失敗。

![顯示在 Azure 容器執行個體中執行之應用程式的瀏覽器螢幕擷取畫面](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> 由於此示例使用自簽章憑證，而不是憑證授權單位頒發的證書，因此瀏覽器在通過 HTTPS 連接到網站時會顯示安全警告。 您可能需要接受警告或調整瀏覽器或證書設置才能繼續進入頁面。 這是預期的行為。

>

## <a name="next-steps"></a>後續步驟

本文介紹如何設置 Nginx 容器，以啟用與容器組中運行的 Web 應用的 SSL 連線。 您可以針對在埠 80 以外的埠上偵聽的應用調整此示例。 您還可以更新 Nginx 設定檔，以自動重定向埠 80 （HTTP） 上的伺服器連接以使用 HTTPS。

雖然本文在側車中使用 Nginx，但您可以使用另一個 SSL 提供程式（如[Caddy](https://caddyserver.com/)）。

如果在[Azure 虛擬網路中](container-instances-vnet.md)部署容器組，則可以考慮為後端容器實例啟用 SSL 終結點的其他選項，包括：

* [Azure 函數代理](../azure-functions/functions-proxies.md)
* [Azure API 管理](../api-management/api-management-key-concepts.md)
* [Azure 應用程式閘道](../application-gateway/overview.md)- 請參閱示例[部署範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)。
