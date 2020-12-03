---
title: 啟用具有側車容器的 TLS
description: 在側車容器中執行 Nginx，為在 Azure 容器實例中執行的容器群組建立 SSL 或 TLS 端點
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 6587a84e7cbe655c509f74e9e39e93010e7058be
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558074"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>在側車容器中啟用 TLS 端點

本文說明如何建立具有應用程式容器的 [容器群組](container-instances-container-groups.md) ，以及執行 TLS/SSL 提供者的側車容器。 藉由使用個別的 TLS 端點來設定容器群組，您可以為應用程式啟用 TLS 連線，而不需要變更應用程式程式碼。

您可以設定包含兩個容器的容器群組範例：
* 應用程式容器，它會使用公用 Microsoft [aci helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 映射執行簡單的 web 應用程式。 
* 執行公用 [Nginx](https://hub.docker.com/_/nginx) 映射的側車容器，設定為使用 TLS。 

在此範例中，容器群組只會以其公用 IP 位址公開 Nginx 的埠443。 Nginx 會將 HTTPS 要求傳送至隨附的 web 應用程式，此應用程式會在內部接聽埠80。 您可以調整在其他埠上接聽的容器應用程式範例。 

請參閱 [後續步驟](#next-steps) ，以瞭解在容器群組中啟用 TLS 的其他方法。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 的版本使用2.0.55 版或更新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="create-a-self-signed-certificate"></a>建立自我簽署憑證

若要將 Nginx 設定為 TLS 提供者，您需要 TLS/SSL 憑證。 本文說明如何建立和設定自我簽署的 TLS/SSL 憑證。 針對生產案例，您應該從憑證授權單位單位取得憑證。

若要建立自我簽署的 TLS/SSL 憑證，請使用 Azure Cloud Shell 和許多 Linux 發行版本中提供的 [OpenSSL](https://www.openssl.org/) 工具，或在您的作業系統中使用類似的用戶端工具。

先在本機工作目錄中) 建立憑證要求 ( 的 csr 檔案：

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

遵循提示來新增識別資訊。 在 [一般名稱] 中，輸入與憑證相關聯的主機名稱。 當系統提示您輸入密碼時，請按 Enter 而不輸入，以略過新增密碼。

執行下列命令，以從憑證要求建立自我簽署憑證 ( .crt 檔案) 。 例如：

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

您現在應該會在目錄中看到三個檔案：憑證要求 (`ssl.csr`) 、私密金鑰 (`ssl.key`) ，以及自我簽署憑證 (`ssl.crt`) 。 在 `ssl.key` `ssl.crt` 稍後的步驟中，您會使用和。

## <a name="configure-nginx-to-use-tls"></a>將 Nginx 設定為使用 TLS

### <a name="create-nginx-configuration-file"></a>建立 Nginx 設定檔

在本節中，您會建立 Nginx 的設定檔，以使用 TLS。 首先，將下列文字複製到名為的新檔案中 `nginx.conf` 。 在 Azure Cloud Shell 中，您可以使用 Visual Studio Code，在工作目錄中建立檔案：

```console
code nginx.conf
```

在中 `location` ，請務必為 `proxy_pass` 您的應用程式設定正確的埠。 在此範例中，我們會設定容器的埠 80 `aci-helloworld` 。

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

        # Optimize TLS/SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive TLS/SSL handshakes.
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

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64 編碼的秘密和設定檔

Base64 編碼 Nginx 設定檔、TLS/SSL 憑證和 TLS 金鑰。 在下一節中，您會在用來部署容器群組的 YAML 檔案中輸入編碼的內容。

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>部署容器群組

現在藉由在 [YAML](container-instances-multi-container-yaml.md)檔中指定容器設定來部署容器群組。

### <a name="create-yaml-file"></a>建立 YAML 檔案

將下列 YAML 複製到名為的新檔案中 `deploy-aci.yaml` 。 在 Azure Cloud Shell 中，您可以使用 Visual Studio Code，在工作目錄中建立檔案：

```console
code deploy-aci.yaml
```

輸入以 base64 編碼的檔案內容，並在下方顯示 `secret` 。 例如， `cat` 每個 base64 編碼的檔案都會看到其內容。 在部署期間，會將這些檔案新增至容器群組中的 [秘密磁片](container-instances-volume-secret.md) 區。 在此範例中，會將秘密磁片區掛接至 Nginx 容器。

```YAML
api-version: 2019-12-01
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

使用 [az group create](/cli/azure/group#az-group-create) 命令，建立資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

使用 [az container create](/cli/azure/container#az-container-create) 命令部署容器群組，並將 YAML 檔案傳遞為引數。

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>檢視部署狀態

若要檢視部署狀態，請使用下列 [az container show](/cli/azure/container#az-container-show) 命令：

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

針對成功的部署，輸出類似于下列內容：

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>確認 TLS 連接

使用您的瀏覽器流覽至容器群組的公用 IP 位址。 此範例中顯示的 IP 位址是 `52.157.22.76` ，因此 URL 為 **https://52.157.22.76** 。 由於 Nginx 伺服器設定，您必須使用 HTTPS 來查看正在執行的應用程式。 嘗試透過 HTTP 進行連線失敗。

![顯示在 Azure 容器執行個體中執行之應用程式的瀏覽器螢幕擷取畫面](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> 因為此範例會使用自我簽署的憑證，而不是來自憑證授權單位單位的憑證，所以瀏覽器會在透過 HTTPS 連線到網站時顯示安全性警告。 您可能需要接受警告或調整瀏覽器或憑證設定，才能繼續前往頁面。 這是預期的行為。

>

## <a name="next-steps"></a>後續步驟

本文說明如何設定 Nginx 容器，以啟用對容器群組中執行之 web 應用程式的 TLS 連線。 您可以針對接聽埠80以外埠的應用程式，調整此範例。 您也可以更新 Nginx 設定檔，以自動將埠80上的伺服器連線重新導向 (HTTP) 以使用 HTTPS。

雖然本文使用側車中的 Nginx，但您可以使用另一個 TLS 提供者，例如 [Caddy](https://caddyserver.com/)。

如果您將容器群組部署在 [Azure 虛擬網路](container-instances-vnet.md)中，您可以考慮使用其他選項來啟用後端容器實例的 TLS 端點，包括：

* [Azure Functions Proxy](../azure-functions/functions-proxies.md)
* [Azure API 管理](../api-management/api-management-key-concepts.md)
* [Azure 應用程式閘道](../application-gateway/overview.md) -請參閱範例 [部署範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)。
