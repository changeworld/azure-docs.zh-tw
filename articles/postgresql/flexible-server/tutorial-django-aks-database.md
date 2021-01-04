---
title: 教學課程：使用 Azure CLI 透過 PostgreSQL 彈性伺服器在 AKS 叢集上部署 Django
description: 了解如何使用「適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器」在 AKS 上快速建置和部署 Django。
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc
ms.openlocfilehash: 15e94b647b0f12baceffc60ad1c6ab81cc84cf22
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631936"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>教學課程：使用「適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器」在 AKS 上部署 Django

在本快速入門中，您會使用 Azure CLI 透過「適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器 (預覽)」在 Azure Kubernetes Service (AKS) 叢集上部署 Django 應用程式。

**[AKS](../../aks/intro-kubernetes.md)** 是一項受控 Kubernetes 服務，可讓您快速部署及管理叢集。 **[適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器 (預覽)](overview.md)** 是完全受控的資料庫服務，其設計目的是要在資料庫管理功能和設定方面提供更細微的控制和彈性。

> [!NOTE]
> - 適用於 PostgreSQL 的 Azure 資料庫彈性伺服器目前為公開預覽狀態
> - 本快速入門假設您已有 Kubernetes 概念的基本知識以及 Django 和 PostgreSQL。

## <a name="pre-requisites"></a>必要條件
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- 使用 Bash 環境的 [Azure Cloud Shell](../../cloud-shell/quickstart.md)。

   [![內嵌啟動](https://shell.azure.com/images/launchcloudshell.png "啟動 Azure Cloud Shell")](https://shell.azure.com)  
- 如果想要，請[安裝](/cli/azure/install-azure-cli) Azure CLI 以執行 CLI 參考命令。
  - 如果您使用的是本機安裝，請使用 [az login](/cli/azure/reference-index#az-login) 命令，透過 Azure CLI 來登入。  請遵循您終端機上顯示的步驟，完成驗證程序。  如需其他登入選項，請參閱[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli)。
  - 出現提示時，請在第一次使用時安裝 Azure CLI 擴充功能。  如需延伸模組詳細資訊，請參閱[使用 Azure CLI 延伸模組](/cli/azure/azure-cli-extensions-overview)。
  - 執行 [az version](/cli/azure/reference-index?#az_version) 以尋找已安裝的版本和相依程式庫。 若要升級至最新版本，請執行 [az upgrade](/cli/azure/reference-index?#az_upgrade)。 本文需要最新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

> [!NOTE]
> 如果在本機 (而不是 Azure Cloud Shell) 執行本快速入門中的命令，請確定您是以系統管理員身分執行命令。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是部署及管理 Azure 資源所在的邏輯群組。 我們將使用 [az group create][az-group-create] 命令在 eastus 位置中建立資源群組 django-project。

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> 資源群組的位置就是資源群組中繼資料儲存所在的位置。 如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。

下列範例輸出顯示已成功建立的資源群組：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>建立 AKS 叢集

使用 [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create) 命令來建立 AKS 叢集。 下列範例會建立名為 myAKSCluster  並包含一個節點的叢集。 這需要數分鐘的時間才能完成。

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
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
az aks get-credentials --resource-group django-project --name djangoappcluster
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

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器
使用 [az postgreSQL flexible-server create](/azure/postgresql/flexible-server?view=azure-cli-latest&preserve-view=true) 命令建立彈性伺服器。 下列命令會使用服務預設值和 Azure CLI 的本機內容值，建立伺服器：

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

建立的伺服器具有下列屬性：
- 第一次佈建伺服器時，會建立新的空白資料庫 ```postgres```。 在本快速入門中，我們將使用此資料庫。
- 自動產生的伺服器名稱、管理使用者名稱、管理員密碼、資源群組名稱 (如果未在本機內容中指定)，並與您的資源群組位於相同位置
- 其餘伺服器組態的服務預設值：計算層 (一般用途)、計算大小/SKU (Standard_D2s_v3，使用 2 個虛擬核心)、備份保留期間 (7天) 和 PostgreSQL 版本 (12)
- 使用公用存取引數可讓您建立伺服器，且伺服器會具有受防火牆規則保護的公用存取權。 請藉由提供您的 IP 位址來新增防火牆規則，以允許從您的用戶端機器進行存取。
- 由於此命令使用本機內容，因此會在資源群組 ```django-project``` 和區域 ```eastus``` 中建立伺服器。


## <a name="build-your-django-docker-image"></a>建置您的 Django Docker 映像

建立新的 [Django 應用程式](https://docs.djangoproject.com/en/3.1/intro/)，或使用您現有的 Django 專案。 請確定您的程式碼位於此資料夾結構中。

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
        . . . . . . .
    ├───static
        . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
更新 ```settings.py``` 中的 ```ALLOWED_HOSTS```，確保 Django 應用程式使用指派給 kubernetes 應用程式的外部 IP。

```python
ALLOWED_HOSTS = ['*']
```

更新 ```settings.py``` 檔案中的 ```DATABASES={ }``` 區段。 下列程式碼片段會從 Kubernetes 資訊清單檔中讀取資料庫主機、使用者名稱和密碼。

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>產生 requirements.txt 檔案
建立 ```requirements.txt``` 檔案，以列出 Django 應用程式的相依性。 以下是 ```requirements.txt``` 範例檔案。 您可以使用 [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) 來產生現有應用程式的 requirements.txt 檔案。

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>建立 Dockerfile
建立名為 ```Dockerfile``` 的新檔案，並複製下列程式碼片段。 此 Dockerfile 會設定 Python 3.8 並安裝 requirements.txt 檔案中列出的所有需求。

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>建置您的映像
請使用 ```cd``` 命令，確定您位於終端機的 ```my-django-app``` 目錄中。 執行下列命令以建置您的佈告欄映像：

``` bash

docker build --tag myblog:latest .

```

將您的映像部署至 [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) 或 [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md)。

> [!IMPORTANT]
>如果您使用 Azure Container Regdistry (ACR)，請執行 ```az aks update``` 命令，以連結 ACR 帳戶與 AKS 叢集。
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>建立 Kubernetes 資訊清單檔

Kubernetes 資訊清單檔會定義所需的叢集狀態，例如要執行哪些容器映像。 建立名為 ```djangoapp.yaml``` 的資訊清單檔，然後將下列 YAML 定義複製進來。

>[!IMPORTANT]
> - 將 ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` 取代為實際的 Django Docker 映像名稱和標籤，例如 ```docker-hub-user/myblog:latest```。
> - 使用 postgres 彈性伺服器的 ```SERVERNAME```、```YOUR-DATABASE-USERNAME```、```YOUR-DATABASE-PASSWORD``` 更新以下 ```env``` 區段。


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>將 Django 部署至 AKS 叢集
使用 [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) 命令來部署應用程式並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f djangoapp.yaml
```

下列範例輸出會顯示已成功建立的部署和服務：

```output
deployment "django-app" created
service "python-svc" created
```

部署 ```django-app``` 可讓您描述部署的詳細資料，例如要用於應用程式的映像、pod 和 pod 組態的數目。 系統會建立一個 ```python-svc```K服務，以透過外部 IP 公開應用程式。

## <a name="test-the-application"></a>測試應用程式

執行應用程式時，Kubernetes 服務會向網際網路公開前端應用程式。 此程序需要數分鐘的時間完成。

若要監視進度，請使用 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令搭配 `--watch` 引數。

```azurecli-interactive
kubectl get service django-app --watch
```

一開始，django-app 服務的 EXTERNAL-IP 會顯示為「擱置中」。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

當 *EXTERNAL-IP* 位址從 *pending* 變成實際的公用 IP 位址時，請使用 `CTRL-C` 停止 `kubectl` 監看式流程。 下列範例輸出會顯示已指派給服務的有效公用 IP 位址：

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

現在開啟網頁瀏覽器並瀏覽至服務的外部 IP 位址，以檢視 Django 應用程式。  

>[!NOTE]
> - Django 網站目前未使用 HTTPS。 建議您[啟用 TLS 搭配自己的憑證](../../aks/ingress-own-tls.md)。
> - 您可以為叢集啟用 [HTTP 路由](../../aks/http-application-routing.md)。 啟用 http 路由時，會在 AKS 叢集中設定輸入控制器。 部署應用程式時，此解決方案也會針對應用程式端點建立可公開存取的 DNS 名稱。

## <a name="run-database-migrations"></a>執行資料庫移轉

對於任何 django 應用程式，您都必須執行資料庫移轉或收集靜態檔案。 您可以使用 ```$ kubectl exec <pod-name> -- [COMMAND]``` 執行這些 django 殼層命令。  執行命令之前，您必須使用 ```kubectl get pods```尋找 pod 名稱。 

```bash
$ kubectl get pods
```

您應該會看到如下的輸出
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

一旦找到 pod 名稱，就可以使用 ```$ kubectl exec <pod-name> -- [COMMAND]``` 命令執行 django 資料庫移轉。 請注意，```/code/``` 是在上述 ```Dockerfile``` 中定義之專案的工作目錄。

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

輸出看起來像這樣 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

如果您遇到問題，請執行 ```kubectl logs <pod-name>``` 查看應用程式所擲回的例外狀況。 如果應用程式順利運作，您會在執行 ```kubectl logs``` 時看到如下的輸出。

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>清除資源

若要避免 Azure 費用，您應該清除不需要的資源。  若不再需要叢集，可使用 [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) 命令來移除資源群組、容器服務和所有相關資源。

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱 [AKS 服務主體的考量和刪除](../../aks/kubernetes-service-principal.md#additional-considerations)。 如果您使用受控識別，則身分識別會由平台負責管理，您不需要刪除。

## <a name="next-steps"></a>後續步驟

- 了解如何[存取 Kubernetes Web 儀表板](../../aks/kubernetes-dashboard.md)，以供您的 AKS 叢集使用
- 了解如何[啟用持續部署](../../aks/deployment-center-launcher.md)
- 了解如何[調整您的叢集](../../aks/tutorial-kubernetes-scale.md)
- 了解如何管理您的 [postgres 彈性伺服器](./quickstart-create-server-cli.md)
- 了解如何為您的資料庫伺服器[設定伺服器參數](./howto-configure-server-parameters-using-cli.md)。
