---
title: 在 Kubernetes 服務中執行語言偵測容器
titleSuffix: Text Analytics -  Azure Cognitive Services
description: 將語言偵測容器以及執行範例部署到 Azure Kubernetes Service，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 1968bc03bfddb9d6f6c8fe743a2a1a99722c074d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399178"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>將文字分析語言偵測容器部署至 Azure Kubernetes Service

了解如何部署語言偵測容器。 此程序示範如何建立本機的 Docker 容器、將容器推送至私人容器登錄、在 Kubernetes 叢集中執行容器，並在網頁瀏覽器中進行測試。

## <a name="prerequisites"></a>必要條件

此程序需要必須安裝並在本機執行的多個工具。 請勿使用 Azure Cloud Shell。

* 使用 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* [Git](https://git-scm.com/downloads) 適用於您的作業系統，因此您可以複製此程序中使用的[範例](https://github.com/Azure-Samples/cognitive-services-containers-samples)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Docker 引擎](https://www.docker.com/products/docker-engine)，並驗證 Docker CLI 可在主控台視窗中運作。
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe)。
* 具有正確定價層的 Azure 資源。 並非所有的定價層都會使用這個容器︰
  * 只有 F0 或標準定價層的**文字分析**資源。
  * 有 S0 定價層的**認知服務**資源。

## <a name="running-the-sample"></a>執行範例

此程序會載入並執行語言偵測的認知服務容器範例。 此範例有兩個容器，一個用於用戶端應用程式，一個用於認知服務容器。 我們會將這兩個映射推送到 Azure Container Registry。 當這些映像出現在您自己的登錄之後，請建立 Azure Kubernetes Service 來存取這些映像並執行容器。 容器執行時，使用 **kubectl** CLI 來監看容器效能。 使用 HTTP 要求存取用戶端應用程式，並查看結果。

![執行範例容器的概念](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>範例容器

此範例有兩個容器映像，一個用於前端網站。 第二個映像是對於文字傳回偵測語言 (文化特性) 的語言偵測容器。 在您完成後，即可從外部的 IP 存取這兩個容器。

### <a name="the-language-frontend-container"></a>語言前端容器

此網站相當於您本身提出語言偵測端點要求的用戶端應用程式。 程序完成時，您可以使用 `http://<external-IP>/<text-to-analyze>` 存取瀏覽器中的網站容器，收到所偵測到的字元字串語言。 此 URL 的範例是 `http://132.12.23.255/helloworld!`。 瀏覽器中的結果是 `English`。

### <a name="the-language-container"></a>語言容器

任何外部要求均可存取此特定程序中的語言偵測容器。 容器尚未出現任何變更，因此標準的認知服務容器特定的語言偵測 API 可供使用。

對於此容器，該 API 是語言偵測的 POST 要求。 對於所有認知服務容器，您可以從其託管的 Swagger 資訊 `http://<external-IP>:5000/swagger/index.html` 深入了解容器。

連接埠 5000 是與認知服務容器搭配使用的預設連接埠。

## <a name="create-azure-container-registry-service"></a>建立 Azure Container Registry 服務

若要將容器部署至 Azure Kubernetes Service，必須可存取容器映像。 建立您自己的 Azure Container Registry 服務來裝載映像。

1. 登入 Azure CLI

    ```azurecli-interactive
    az login
    ```

1. 建立名為 `cogserv-container-rg` 的資源群組來保存在此程序中建立的每個資源。

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. 然後，以您的名稱加上 `registry` 的格式 (例如 `pattyregistry`) 來建立您自己的 Azure Container Registry。 在名稱中請勿使用連字號或底線字元。

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    將結果儲存以取得 **loginServer** 屬性。 這將是託管容器的位址，稍後將用於 `language.yml` 檔案。

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. 登入您的容器登錄。 您需要登入，才能將映像推送至您的登錄。

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>取得網站 Docker 映像

1. 此程序中使用的範例程式碼是在認知服務容器範例儲存機制中。 複製存放庫來取得範例的本機副本。

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    存放庫出現在本機電腦後，在 [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) 目錄中尋找網站。 此網站將作為用戶端應用程式，呼叫語言偵測容器中託管的語言偵測 API。  

1. 建置此網站的 Docker 映像。 您執行下列命令時，確定主控台位於 Dockerfile 所在的 [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) 目錄：

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    若要追蹤容器登錄庫的版本，請將標記加入版本格式，例如 `v1`。

1. 將映像推送至容器登錄。 這會花費幾分鐘的時間。您可以在裝置註冊至公司入口網站的期間繼續使用它

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    如果您收到 `unauthorized: authentication required` 錯誤，請使用 `az acr login --name <your-container-registry-name>` 命令登入。 

    此程序完成時，結果應該如下所示：

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>取得語言偵測 Docker 映像

1. 將最新版 Docker 映像提取到本機電腦。 這會花費幾分鐘的時間。您可以在裝置註冊至公司入口網站的期間繼續使用它 如果沒有這個容器的較新版本，請將該值從 `1.1.006770001-amd64-preview` 變更為較新版本。

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. 使用容器登錄標記映像。 尋找最新版本，如果有較新版本，則取代版本 `1.1.006770001-amd64-preview`。 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. 將映像推送至容器登錄。 這會花費幾分鐘的時間。您可以在裝置註冊至公司入口網站的期間繼續使用它

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>取得容器登錄認證

需要進行下列步驟，才能取得您稍後在此程序中建立的 Azure Kubernetes Service 連接您的容器登錄所需的資訊。

1. 建立服務主體。

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    對於步驟 3 中的受託人參數 `appId` 儲存結果 `<appId>` 值。 對於下一個區段的 client-secret 參數 `password` 儲存 `<client-secret>`。

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. 取得容器登錄 ID。

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    在下一個步驟中儲存範圍參數值的輸出 `<acrId>`。 如下所示：

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    對於這個區段的步驟 3 儲存完整值。

1. 若要授予 AKS 叢集所需的正確存取權，以使用容器登錄中儲存的映像，請建立角色指派。 使用在前兩個步驟中所蒐集的值，取代 `<appId>` 和 `<acrId>`。

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>建立 Azure Kubernetes Service

1. 建立 Kubernetes 叢集。 除了 name 參數之外的所有參數值均來自先前區段。 選擇表示建立者及其用途的名稱，例如 `patty-kube`。

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    這個步驟可能需要幾分鐘的時間。 結果如下：

    ```output
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    服務已建立，但是沒有網站容器或語言偵測容器。  

1. 取得 Kubernetes 叢集的認證。

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>將協調流程定義載入到您的 Kubernetes 服務

本節使用 **kubectl** CLI 與 Azure Kubernetes 服務交談。

1. 載入協調流程定義之前，請檢查 **kubectl** 可存取節點。

    ```console
    kubectl get nodes
    ```

    回應如下所示：

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. 複製下列檔案並命名為 `language.yml`。 檔案有 `service` 區段和 `deployment` 區段，分別用於兩種容器類型，而且有 `language-frontend` 網站容器和 `language` 偵測容器。

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. 根據下表變更 `language.yml` 的語言前端部署行，以便新增您自己的容器登錄映像名稱、用戶端密碼和文字分析設定。

    語言前端部署設定|目的|
    |--|--|
    |行 32<br> `image` 屬性|容器登錄中的前端映像所在的映像位置<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |行 44<br> `name` 屬性|上一個區段之中映像的容器登錄密碼，稱為 `<client-secret>`。|

1. 根據下表變更 `language.yml` 的語言部署行，以便新增您自己的容器登錄映像名稱、用戶端密碼和文字分析設定。

    |語言部署設定|目的|
    |--|--|
    |行 78<br> `image` 屬性|容器登錄中的語言映像所在的映像位置<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |行 95<br> `name` 屬性|上一個區段之中映像的容器登錄密碼，稱為 `<client-secret>`。|
    |行 91<br> `apiKey` 屬性|您的文字分析資源索引鍵|
    |行 92<br> `billing` 屬性|文字分析資源的計費端點。<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    因為 **apiKey** 和**計費端點**會設定為 Kubernetes 協調流程定義的一部分，因此網站容器不需要知道這些，也不需要傳遞這些成為要求的一部分。 網站容器是指其協調器名稱為 `language` 的語言偵測容器。

1. 從您建立和儲存 `language.yml` 的資料夾中，載入此範例的協調流程定義檔。

    ```console
    kubectl apply -f language.yml
    ```

    回應如下：

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>取得容器的外部 IP

對於這兩個容器，確認 `language-frontend` 和 `language` 服務正在執行，並取得外部 IP 位址。

```console
kubectl get all
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

如果服務的 `EXTERNAL-IP` 顯示為擱置，請重新執行命令，直到移至下一個步驟之前顯示 IP 位址為止。

## <a name="test-the-language-detection-container"></a>測試語言偵測容器

從上一個區段開啟瀏覽器並瀏覽至 `language` 容器的外部 IP：`http://<external-ip>:5000/swagger/index.html`。 您可以使用 API 的 `Try it` 功能測試語言偵測端點。

![檢視容器的 swagger 文件](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>測試用戶端應用程式容器

使用下列格式，將瀏覽器中的 URL 變更為 `language-frontend` 容器的外部 IP：`http://<external-ip>/helloworld`。 `helloworld` 的英文文化特性文字預測為 `English`。

## <a name="clean-up-resources"></a>清除資源

在您完成叢集之後，請刪除 Azure 資源群組。

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>相關資訊

* [Docker 使用者的 kubectl](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [認知服務容器](../cognitive-services-container-support.md)

<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->