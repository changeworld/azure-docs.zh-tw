---
title: 教學課程：使用 Azure CLI 透過 MySQL 彈性伺服器在 AKS 叢集上部署 WordPress
description: 了解如何使用「適用於 MySQL 的 Azure 資料庫 - 彈性伺服器」在 AKS 上快速建置和部署 WordPress。
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc
ms.openlocfilehash: b82bb9b7684d1119b6b62216b52210845d8f4c67
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "98199626"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>教學課程：使用「適用於 MySQL 的 Azure 資料庫 - 彈性伺服器」在 AKS 上部署 WordPress

在本快速入門中，您會使用 Azure CLI 透過「適用於 MySQL 的 Azure 資料庫 - 彈性伺服器 (預覽)」在 Azure Kubernetes Service (AKS) 叢集上部署 WordPress 應用程式。 
**[AKS](../../aks/intro-kubernetes.md)** 是一項受控 Kubernetes 服務，可讓您快速部署及管理叢集。 **[適用於 MySQL 的 Azure 資料庫 - 彈性伺服器 (預覽)](overview.md)** 是完全受控的資料庫服務，其設計目的是要在資料庫管理功能和設定方面提供更細微的控制和彈性。 彈性伺服器目前處於預覽狀態。

> [!NOTE]
> - 適用於 MySQL 的 Azure 資料庫彈性伺服器目前處於公開預覽狀態
> - 本快速入門假設您已有 Kubernetes 概念的基本知識以及 WordPress and MySQL。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要最新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

> [!NOTE]
> 如果在本機 (而不是 Azure Cloud Shell) 執行本快速入門中的命令，請確定您是以系統管理員身分執行命令。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是部署及管理 Azure 資源所在的邏輯群組。 我們將使用 [az group create][az-group-create] 命令在 *eastus* 位置中建立資源群組 *wordpress-project*。

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> 資源群組的位置就是資源群組中繼資料儲存所在的位置。 如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。

下列範例輸出顯示已成功建立的資源群組：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>建立 AKS 叢集

使用 [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create) 命令來建立 AKS 叢集。 下列範例會建立名為 myAKSCluster  並包含一個節點的叢集。 這需要數分鐘的時間才能完成。

```azurecli-interactive
az aks create --resource-group wordpress-project --name myAKSCluster --node-count 1 --generate-ssh-keys
```

在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。

> [!NOTE]
> 建立 AKS 叢集時，系統會自動建立第二個資源群組來儲存 AKS 資源。 請參閱[為何使用 AKS 建立兩個資源群組？](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>連線至叢集

若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)。 如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 若要在本機安裝 `kubectl`，請使用 [az aks install-cli](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-install-cli) 命令：

```azurecli-interactive
az aks install-cli
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials) 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name myAKSCluster
```

> [!NOTE]
> 上述命令會使用 [Kubernetes 組態檔](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)的預設位置，也就是 `~/.kube/config`。 您可以使用 --file，為您的 Kubernetes 組態檔指定不同的位置。

若要驗證針對您叢集的連線，請使用 [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令來傳回叢集節點的清單。

```azurecli-interactive
kubectl get nodes
```

下列輸出範例會顯示上一個步驟中建立的單一節點。 請確定節點的狀態為 *Ready*：

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>建立適用於 MySQL 的 Azure 資料庫 - 彈性伺服器
使用 [az mysql flexible-server create](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true) 命令建立彈性伺服器。 下列命令會使用服務預設值和 Azure CLI 的本機內容值，建立伺服器：

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

建立的伺服器具有下列屬性：
- 第一次佈建伺服器時，會建立新的空白資料庫 ```flexibleserverdb```。 在本快速入門中，我們將使用此資料庫。
- 自動產生的伺服器名稱、管理使用者名稱、管理員密碼、資源群組名稱 (如果未在本機內容中指定)，並與您的資源群組位於相同位置
- 其餘伺服器組態的服務預設值：計算層 (高載)、計算大小/SKU (B1MS)、備份保留期間 (7天) 和 MySQL 版本 (5.7)
- 使用公用存取引數可讓您建立伺服器，且伺服器會具有受防火牆規則保護的公用存取權。 請藉由提供您的 IP 位址來新增防火牆規則，以允許從您的用戶端機器進行存取。
- 由於此命令使用本機內容，因此會在資源群組 ```wordpress-project``` 和區域 ```eastus``` 中建立伺服器。


### <a name="build-your-wordpress-docker-image"></a>建置您的 WordPress Docker 映像

下載[最新的 WordPress](https://wordpress.org/download/) 版本。 為您的專案建立新的目錄 ```my-wordpress-app```，並使用這個簡單的資料夾結構

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


將 ```wp-config-sample.php``` 重新命名為 ```wp-config.php```，並將第 21 到 32 行取代為此程式碼片段。 下列程式碼片段會從 Kubernetes 資訊清單檔中讀取資料庫主機、使用者名稱和密碼。

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>建立 Dockerfile
建立新的 Dockerfile，並複製此程式碼片段。 此 Dockerfile 會使用 PHP 設定 Apache Web 伺服器並啟用 mysqli 擴充功能。

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>建置您的 Docker 映像
請使用 ```cd``` 命令，確定您位於終端機的 ```my-wordpress-app``` 目錄中。 執行下列命令以建置映像：

``` bash

docker build --tag myblog:latest .

```

將您的映像部署至 [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) 或 [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md)。

> [!IMPORTANT]
>如果您使用 Azure Container Regdistry (ACR)，請執行 ```az aks update``` 命令，以連結 ACR 帳戶與 AKS 叢集。
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>建立 Kubernetes 資訊清單檔

Kubernetes 資訊清單檔會定義所需的叢集狀態，例如要執行哪些容器映像。 建立名為 `mywordpress.yaml` 的資訊清單檔，然後將下列 YAML 定義複製進來。

>[!IMPORTANT]
> - 將 ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` 取代為實際的 WordPress Docker 映像名稱和標籤，例如 ```docker-hub-user/myblog:latest```。
> - 使用 MySQL 彈性伺服器的 ```SERVERNAME```、```YOUR-DATABASE-USERNAME```、```YOUR-DATABASE-PASSWORD``` 更新以下 ```env``` 區段。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>將 WordPress 部署至 AKS 叢集
使用 [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) 命令來部署應用程式並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f mywordpress.yaml
```

下列範例輸出會顯示已成功建立的部署和服務：

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>測試應用程式

執行應用程式時，Kubernetes 服務會向網際網路公開前端應用程式。 此程序需要數分鐘的時間完成。

若要監視進度，請使用 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令搭配 `--watch` 引數。

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

一開始，*wordpress-blog* 服務的 *EXTERNAL-IP* 會顯示為「暫止」。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

當 *EXTERNAL-IP* 位址從 *pending* 變成實際的公用 IP 位址時，請使用 `CTRL-C` 停止 `kubectl` 監看式流程。 下列範例輸出會顯示已指派給服務的有效公用 IP 位址：

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>瀏覽 WordPress

開啟網頁瀏覽器並瀏覽至服務的外部 IP 位址，以查看您的 WordPress 安裝頁面。

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="AKS 和 MySQL 彈性伺服器上的 Wordpress 安裝成功":::

>[!NOTE]
> - WordPress 網站目前未使用 HTTPS。 建議您[啟用 TLS 搭配自己的憑證](../../aks/ingress-own-tls.md)。
> - 您可以為叢集啟用 [HTTP 路由](../../aks/http-application-routing.md)。

## <a name="clean-up-the-resources"></a>清除資源

若要避免 Azure 費用，您應該清除不需要的資源。  若不再需要叢集，可使用 [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) 命令來移除資源群組、容器服務和所有相關資源。

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱 [AKS 服務主體的考量和刪除](../../aks/kubernetes-service-principal.md#additional-considerations)。 如果您使用受控識別，則身分識別會由平台負責管理，您不需要刪除。

## <a name="next-steps"></a>後續步驟

- 了解如何[存取 Kubernetes Web 儀表板](../../aks/kubernetes-dashboard.md)，以供您的 AKS 叢集使用
- 了解如何[調整您的叢集](../../aks/tutorial-kubernetes-scale.md)
- 了解如何管理您的 [MySQL 彈性伺服器](./quickstart-create-server-cli.md)
- 了解如何為您的資料庫伺服器[設定伺服器參數](./how-to-configure-server-parameters-cli.md)。

