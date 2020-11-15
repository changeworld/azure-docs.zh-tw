---
title: 疑難排解
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: 了解在啟用和使用 Azure Dev Spaces 時，如何針對常見問題進行疑難排解並加以解決
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: a30ae2d78d682427cf53c8f98b0ca70b441d72e1
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636804"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces 疑難排解

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

本指南包含您在使用 Azure Dev Spaces 時可能會遇到的常見問題相關資訊。

如果您在使用 Azure Dev Spaces 時遇到問題，請[在 Azure Dev Spaces GitHub 存放庫中建立問題](https://github.com/Azure/dev-spaces/issues)。

## <a name="before-you-begin"></a>開始之前

為了更有效地進行問題的疑難排解，建立更詳細的記錄以供檢閱可能會有幫助。

若為 Visual Studio，請將 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 環境變數設定為 1。 請務必重新啟動 Visual Studio，讓環境變數生效。 啟用之後，詳細記錄會寫入至您的 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 目錄。

在命令執行期間，您可以在 CLI 中使用 `--verbose` 參數輸出更多資訊。 您也可以在 `%TEMP%\Azure Dev Spaces` 中瀏覽更多詳細的記錄。 在 Mac 上，可以在終端視窗執行 `echo $TMPDIR` 來找出 *TEMP* 目錄。 在 Linux 電腦上， *TEMP* 目錄通常是 `/tmp`。 此外，請確認已在 [Azure CLI 設定檔](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)中啟用記錄。

Azure Dev Spaces 在針對單一執行個體 (或 Pod) 進行偵錯時也能達到最佳效果。 `azds.yaml` 檔案包含一個 *replicaCount* 設定，此設定會指出 Kubernetes 將為服務執行的 Pod 數目。 如果您變更 *replicaCount* 以將應用程式設定成針對指定的服務執行多個 Pod，偵錯工具將會連結至第一個 Pod (依字母順序列出時)。 偵錯工具會在該原始 Pod 回收時連結至不同的 Pod，而可能導致非預期的行為。

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>啟用 Azure Dev Spaces 的常見問題

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>錯誤「無法建立 Azure Dev Spaces 控制器」

當發生錯誤而無法建立控制器時，您會看到此錯誤。 如果錯誤是暫時性的，刪除並重新建立控制器即可加以修正。

您也可以嘗試刪除控制器：

```bash
azds remove -g <resource group name> -n <cluster name>
```

使用 Azure Dev Spaces CLI 刪除控制器。 不可能從 Visual Studio 刪除控制器。 您也無法在 Azure Cloud Shell 中安裝 Azure Dev Spaces CLI，因此無法從 Azure Cloud Shell 中刪除控制器。

如果您未安裝 Azure Dev Spaces CLI，可以先使用下列命令安裝，再刪除您的控制器：

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>建立控制器時因控制器名稱長度而失敗

Azure Dev Spaces 控制器的名稱長度不可超過 31 個字元。 當您在 AKS 叢集上啟用 Dev Spaces 或建立控制器時，如果您的控制器名稱超過 31 個字元，就會收到錯誤。 例如：

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

若要修正此問題，請用其他名稱建立控制器。 例如：

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>將 Windows 節點集區新增至 AKS 叢集時，啟用 Dev Spaces 失敗

目前 Azure Dev Spaces 僅供在 Linux Pod 和節點上執行。 當您的 AKS 叢集具有 Windows 節點集區時，必須確定只有在 Linux 節點上排程 Azure Dev Spaces Pod。 如果已將 Azure Dev Spaces Pod 排程到 Windows 節點上執行，該 Pod 將不會啟動，且 Dev Spaces 會啟用失敗。

若要修正此問題，請[將污點新增](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)至您的 AKS 叢集，以確保 Linux Pod 不會排程到 Windows 節點上執行。

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>錯誤「在叢集上找不到處於就緒狀態的未污染 Linux 節點。 必須至少有一個未污染的 Linux 節點處於『就緒』狀態，才能在『azds』命名空間中部署 Pod。」

Azure Dev Spaces 無法在 AKS 叢集上建立控制器，因為在「就緒」狀態中找不到要排程 Pod 的未污染節點。 Azure Dev Spaces 需要至少一個 Linux 節點處於「就緒」狀態，此狀態允許在不指定容差的情況下排程 Pod。

若要修正此問題，請[在 AKS 叢集上更新污點設定](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)，以確保至少有一個 Linux 節點允許在不指定容差的情況下排程 Pod。 此外，請確定至少有一個 Linux 節點允許在不指定容差的情況下排程 Pod，且處於「就緒」狀態。 如果您的節點花很長的時間來達到「就緒」狀態，可嘗試重新啟動您的節點。

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>執行 az aks 時發生錯誤「未正確安裝 Azure Dev Spaces CLI」

Azure Dev Spaces CLI 的更新已變更其安裝路徑。 如果您使用 2.0.63 之前的 Azure CLI 版本，可能會看到此錯誤。 若要顯示您的 Azure CLI 版本，請使用 `az --version`。

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

雖然在 2.0.63 之前使用 Azure CLI 版本執行 `az aks use-dev-spaces` 時，仍會出現錯誤訊息，但安裝會成功。 您可以繼續使用 `azds` 而不會有任何問題。

若要修正此問題，請將 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 的安裝版本更新為 2.0.63 或更新版本。 此更新將會解決您在執行 `az aks use-dev-spaces` 時所收到的錯誤訊息。 或者，您可以繼續使用目前版本的 Azure CLI 和 Azure Dev Spaces CLI。

### <a name="error-unable-to-reach-kube-apiserver"></a>錯誤「無法觸達 kube-apiserver」

當 Azure Dev Spaces 無法連接到 AKS 叢集的 API 伺服器時，您可能會看到此錯誤。

如果您的 AKS 叢集 API 伺服器的存取權已遭鎖定，或您的 AKS 叢集已啟用 [API 伺服器授權 IP 位址範圍](../aks/api-server-authorized-ip-ranges.md) ，則您也必須 [建立](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) 或 [更新](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) 您的叢集，以 [根據您的區域允許其他範圍](configure-networking.md#aks-cluster-network-requirements)

執行 kubectl 命令以確定 API 伺服器可供使用。 如果 API 伺服器無法使用，請洽詢 AKS 支援服務，並在 API 伺服器運作時再試一次。

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>準備專案以進行 Azure Dev Spaces 時常見的問題

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>「因語言不受支援而無法產生 Dockerfile」的警告
Azure Dev Spaces 提供 C# 和 Node.js 的原生支援。 如果您在目錄中執行 `azds prep`，而該目錄包含以其中一種語言撰寫的程式碼，Azure Dev Spaces 將自動為您建立適當的 Dockerfile。

您仍可透過以其他語言撰寫的程式碼來使用 Azure Dev Spaces，但在第一次執行 `azds up` 之前，您必須先手動建立 Dockerfile。

如果您的應用程式是以 Azure Dev Spaces 未原生支援的語言所撰寫，您就必須提供適當的 Dockerfile，建置用來執行程式碼的容器映像。 Docker 提供[撰寫 Dockerfile 的最佳做法清單](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)及 [Dockerfile 參考](https://docs.docker.com/engine/reference/builder/)，可協助您撰寫符合需求的 Dockerfile。

在您備妥適當的 Dockerfile 後，您即可執行 `azds up` 在 Azure Dev Spaces 中執行您的應用程式。

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 啟動或停止服務時的常見問題

### <a name="error-config-file-not-found"></a>錯誤「找不到組態檔：」

執行 `azds up` 時，您可能會看到此錯誤。 `azds up` 和 `azds prep` 都必須從您要在開發空間中執行的專案根目錄中執行。

若要修正此問題：
1. 將目前目錄變更為內含服務程式碼的根資料夾。 
1. 如果您在程式碼資料夾中沒有 _azds.yaml_ 檔案，請執行 `azds prep` 以產生 Docker、Kubernetes 及 Azure Dev Spaces 資產。

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>AKS 虛擬節點在執行到「正在等待容器映像組建...」步驟時發生逾時

當您嘗試使用 Dev Spaces 來執行服務，而服務已設定為在 [AKS 虛擬節點](../aks/virtual-nodes-portal.md)上執行時，就會發生逾時。 Dev Spaces 目前不支援在虛擬節點上建置服務或對服務進行偵錯。

如果您執行 `azds up` 並搭配 `--verbose` 參數，或在 Visual Studio 中啟用詳細資訊記錄，則會看到其他詳細資料：

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

上述命令顯示服務的 Pod 已指派給 *virtual-node-aci-linux* ，其為虛擬節點。

若要修正此問題，請更新服務的 Helm 圖表，以移除任何允許服務在虛擬節點上執行的 *nodeSelector* 或 *tolerations* 值。 這些值通常會定義在圖表的 `values.yaml` 檔案中。

如果您想要透過 Dev Spaces 建置或偵錯的服務是在 VM 節點上執行的，則仍可使用已啟用虛擬節點功能的 AKS 叢集。 在 VM 節點上執行具有 Dev Spaces 的服務是預設設定。

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>啟動 Dev Spaces 時出現錯誤：「找不到就緒的 Tiller Pod」

如果 Helm 用戶端無法再與叢集中執行的 Tiller Pod 通訊，就會發生此錯誤。

若要修正此問題，請重新啟動叢集中的代理程式節點。

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>錯誤「release azds- \<identifier\> - \<spacename\> - \<servicename\> failed：服務 ' \<servicename\> ' 已存在」或「提取存取拒絕」 \<servicename\> ，存放庫不存在或可能需要「docker login '」

如果您在相同的開發空間內混合執行直接 Helm 命令 (例如 `helm install`、`helm upgrade` 或 `helm delete`) 與 Dev Spaces 命令 (例如 `azds up` 和 `azds down`)，就會發生這些錯誤。 因為 Dev Spaces 有本身的 Tiller 執行個體，所以會與您在相同開發空間中執行的自備 Tiller 執行個體相衝突，因此會發生這種情況。

您可以對相同的 AKS 叢集使用 Helm 命令和 Dev Spaces 命令，但是每個啟用 Dev Spaces 的命名空間只應使用其中一種。

例如，假設您使用 Helm 命令，在父代開發空間中執行整個應用程式。 您可以建立該父系的子開發空間，使用 Dev Spaces 在子開發空間內執行個別服務，並一起測試服務。 當您準備好簽入變更時，請使用 Helm 命令，將更新的程式碼部署至父代開發空間。 請勿使用 `azds up` 在父代開發空間中執行更新的服務，如此會與一開始使用 Helm 執行的服務發生衝突。

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>現有的 Dockerfile 未用來建立容器

Azure Dev Spaces 可以設定為指向您專案中的特定 _Dockerfile_ 。 如果發生 Azure Dev Spaces 未使用您預期的 Dockerfile 來建置容器的情形，您可能需要明確告訴 Azure Dev Spaces 要使用哪個 Dockerfile。 

若要修正此問題，請在專案中開啟 Azure Dev Spaces 所產生的 _azds.yaml_ 檔案。 更新 *configurations: develop: build: dockerfile* ，以指向您想要使用的 Dockerfile。 例如：

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>嘗試從私人登錄使用 Docker 映射時發生「未經授權：需要驗證」錯誤

您使用需要驗證的私人登錄中的 Docker 映射。

若要修正此問題，您可以使用 [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)，允許 Dev Spaces 驗證並從這個私人登錄提取映射。 若要使用 imagePullSecrets，請[在您要使用映射的命名空間中建立 Kubernetes 祕密](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)。 然後在 `azds.yaml` 中提供祕密做為 imagePullSecret。

以下是在 `azds.yaml` 中指定 imagePullSecrets 的範例。

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> 在 `azds.yaml` 中設定 imagePullSecrets 將會覆寫 `values.yaml` 中指定的 imagePullSecrets。

### <a name="error-service-cannot-be-started"></a>錯誤「服務無法啟動。」

當您的服務程式碼無法啟動時，您可能會看到此錯誤訊息。 通常是使用者程式碼所造成的。 若要取得更多診斷資訊，請在啟動服務時啟用更詳細的記錄。

從命令列中，使用 `--verbose` 來啟用更詳細的記錄。 您也可以使用 `--output` 來指定輸出格式。 例如：

```cmd
azds up --verbose --output json
```

在 Visual Studio 中：

1. 開啟 [工具] > [選項]，然後在 [專案和解決方案] 底下選擇 [建置並執行]。
2. 將 **MSBuild 專案建置輸出詳細資訊** 的設定變更為 [詳細] 或 [診斷]。

    ![工具選項對話方塊的螢幕擷取畫面](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>在重新建立控制器之後重新執行服務

在將與此叢集關聯的 Azure Dev Spaces 控制器移除並再予以重新建立之後，嘗試重新執行服務時，您會收到「服務無法啟動」的錯誤。 在此情況下，詳細資訊輸出會包含下列文字：

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

之所以會發生此錯誤，是因為移除 Dev Spaces 控制器並不會移除該控制器先前所安裝的服務。 重新建立控制器後再嘗試使用新控制器來執行服務會失敗，因為舊服務仍在原位。

若要解決此問題，請使用 `kubectl delete` 命令來手動從叢集中移除舊服務，然後重新執行 Dev Spaces 以安裝新服務。

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>錯誤「服務無法啟動。」 使用多階段 Dockerfile 時

在使用多階段 Dockerfile 時，您會收到「服務無法啟動」的錯誤。 在此情況下，詳細資訊輸出會包含下列文字：

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

之所以發生此錯誤，是因為 Azure Dev Spaces 目前不支援多階段組建。 若要避免多階段組建，請重寫 Dockerfile。

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>連線到您的開發機器時，網路流量不會轉送到您的 AKS 叢集

使用 [Azure Dev Spaces 將 AKS 叢集連線到您的開發機器](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes)時，可能會遇到無法在您的開發機器和 AKS 叢集之間轉送網路流量的問題。

將您的開發機器連接到 AKS 叢集時，Azure Dev Spaces 藉由修改開發機器的 `hosts` 檔案，來轉送 AKS 叢集與開發機器之間的網路流量。 Azure Dev Spaces 會在 `hosts` 中建立一個項目，其中包含您要取代為主機名稱的 Kubernetes 服務位址。 此項目會與埠轉送搭配使用，以引導您的開發機器與 AKS 叢集之間的網路流量。 如果開發機器上的服務與您要取代的 Kubernetes 服務連接埠發生衝突，Azure Dev Spaces 無法轉送 Kubernetes 服務的網路流量。 例如， *Windows BranchCache* 服務通常會繫結至 *0.0.0.0:80* ，而衝突會導致所有本機 IP 上的連接埠 80 發生衝突。

若要修正此問題，您必須停止與您嘗試取代的 Kubernetes 服務連接埠發生衝突的任何服務或處理常式。 您可以使用工具 (例如 *netstat* ) 來檢查開發機器上的哪些服務或處理常式衝突。

例如，若要停止和停用 *Windows BranchCache* 服務：
* 從命令提示字元執行 `services.msc`。
* 以滑鼠右鍵按一下 [BranchCache]，然後選取 [屬性]。
* 按一下 [停止]。
* 或者，您可以將 *啟動類型* 設定為 *停用* 來停用它。
* 按一下 [確定]。

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>錯誤「找不到適用于 pod 的 AzureAssignedIdentity： azds/azds-webhook-部署- \<id\> 處於已指派狀態」

當您在已安裝 [受控識別](../aks/use-managed-identity.md) 和 [pod 受控](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) 識別的 AKS 叢集上執行具有 Azure Dev Spaces 的服務時，此程式可能會在 *圖表安裝* 步驟之後停止回應。 如果您檢查 *azds* 命名空間中的 *azds-injector-webhook* ，您可能會看到此錯誤。

在您的叢集上執行的服務 Azure Dev Spaces 會使用叢集的受控識別，與叢集外的 Azure Dev Spaces 後端服務溝通。 安裝 Pod 受控身分識別時，會在叢集的節點上設定網路規則，以便將受控識別認證的所有呼叫重新導向至叢集上安裝的 [Node 受控識別 (NMI) DaemonSet](https://github.com/Azure/aad-pod-identity#node-managed-identity)。 此 NMI DaemonSet 會識別呼叫的 Pod，並確保 Pod 已妥善標示為可存取要求的受控識別。 Azure Dev Spaces 無法偵測叢集是否已安裝 Pod 受控識別，且無法執行必要的設定，以允許 Azure Dev Spaces 服務存取叢集的受控識別。 由於 Azure Dev Spaces 服務尚未設定為存取叢集的受控識別，因此 NMI DaemonSet 將不會允許它們取得受控識別的 Azure AD 權杖，而且無法與 Azure Dev Spaces 後端服務進行通訊。

若要修正此問題，請套用 *azds-injector-webhook* 的 [AzurePodIdentityException](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)，並透過 Azure Dev Spaces 所檢測的更新 Pod 來存取受控識別。

建立名為 *webhookException.yaml* 的檔案，並複製下列 YAML 定義：

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

上述檔案會建立 *azds-injector-webhook* 的 *AzurePodIdentityException* 物件。 若要部署這個物件，請使用 `kubectl`：

```cmd
kubectl apply -f webhookException.yaml
```

若要更新 Azure Dev Spaces 檢測的 Pod 以存取受控識別，請更新下列 YAML 定義中的 *命名空間* ，並使用 `kubectl` 套用到每個開發空間。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

或者，您可以建立 *AzureIdentity* 和 *AzureIdentityBinding* 物件，並針對以 Azure Dev Spaces 所檢測的空間中所執行的工作負載更新 Pod 標籤，以存取 AKS 叢集所建立的受控識別。

若要列出受控識別的詳細資料，請針對您的 AKS 叢集執行下列命令：

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

上述命令會輸出 *clientId* 和 *resourceId* 做為受控識別。 例如：

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

若要建立 *AzureIdentity* 物件，請建立名為 *clusteridentity.yaml* 的檔案，並使用來自上一個命令的受控識別詳細資料所更新的下列 YAML 定義：

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

若要建立 *AzureIdentityBinding* 物件，請建立名為 *clusteridentitybinding.yaml* 的檔案，並使用下列 YAML 定義：

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

若要部署 *AzureIdentity* 和 *AzureIdentityBinding* 物件，請使用 `kubectl`：

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

在您部署 *AzureIdentity* 和 *AzureIdentityBinding* 物件之後，具有 *aadpodidbinding: my-label-value* 標籤的任何工作負載都可以存取叢集的受控識別。 新增此標籤，並重新部署在任何開發空間中執行的所有工作負載。 例如：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>在 Azure Dev Spaces 中使用 Visual Studio 和 Visual Studio Code 的常見問題

### <a name="error-required-tools-and-configurations-are-missing"></a>錯誤「遺失必要的工具和組態」

啟動 VS Code 時可能會發生下列錯誤：「[Azure Dev Spaces] 遺失建置 '[專案名稱]' 及對其進行偵錯的必要工具和組態」。
此錯誤訊息表示 azds.exe 不在 PATH 環境變數中，如同 VS Code 中所示。

嘗試在已正確設定 PATH 環境變數的前提下，請從命令提示字元啟動 VS Code。

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>錯誤「建置 'projectname' 並對其進行偵錯所需的工具已過時。」

如果您有適用於 Azure Dev Spaces 的新版 VS Code 延伸模組，但 Azure Dev Spaces CLI 為舊版，就會在 Visual Studio Code 中看到此錯誤。

嘗試下載並安裝最新版的 Azure Dev Spaces CLI：

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>錯誤：「找不到 coreclr 類型的偵測工具擴充」

執行 Visual Studio Code 偵錯工具時，您可能會看到此錯誤。 您的開發機器上可能未安裝適用於 C# 的 VS Code 延伸模組。 C# 延伸模組包含對 .NET Core (CoreCLR) 的偵錯支援。

若要修正此問題，請安裝[適用於 C# 的 VS Code 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>錯誤「不支援設定的偵錯類型 'coreclr'」

執行 Visual Studio Code 偵錯工具時，您可能會看到此錯誤。 您可能沒有 VS Code 擴充功能可以讓 Azure Dev Spaces 在您的開發機器上安裝。

若要修正此問題，請安裝適用於 Azure Dev Spaces 的 VS Code 擴充功能。

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>錯誤「無效的 'cwd' 值 '/src'。 系統找不到指定的檔案。」 或「launch: program '/src/[專案二進位檔案路徑]' 不存在」

執行 Visual Studio Code 偵錯工具時，您可能會看到此錯誤。 VS Code 延伸模組預設會使用 `src` 作為容器上專案的工作目錄。 如果您已將 `Dockerfile` 更新成指定不同的工作目錄，就可能看到此錯誤。

若要修正此問題，請更新您專案資料夾的 `.vscode` 子目錄底下出現的 `launch.json` 檔案。 將 `configurations->cwd` 指示詞變更成指向與您專案之 `Dockerfile`.中所定義 `WORKDIR` 相同的目錄。 您可能也需要更新 `configurations->program` 指示詞。

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>錯誤「管道程式 'azds' 非預期地結束，代碼 126。」

執行 Visual Studio Code 偵錯工具時，您可能會看到此錯誤。

若要修正此問題，請關閉 Visual Studio Code，然後重新開啟。 重新啟動偵錯工具。

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>將偵錯工具附加至 node.js 應用程式時發生錯誤「內部監看失敗：監看 ENOSPC」

當執行 Pod 的節點與您嘗試附加的 node.js 應用程式已超過 *fs.inotify.max_user_watches* 值時，就會發生此錯誤。 在某些情況下，[fs.inotify.max_user_watches 的預設值可能太小，無法直接處理將偵錯工具附加至 Pod](https://github.com/Azure/AKS/issues/772) (英文)。

此問題的暫時解決方法是在叢集的每個節點上增加 fs.inotify.max_user_watches 的值，然後重新啟動該節點，讓變更生效。

## <a name="other-common-issues"></a>其他常見問題

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>錯誤「azds」未辨識為內部或外部命令、可執行程式或批次檔

如果未安裝或正確設定 `azds.exe`，會出現此錯誤。

若要修正此問題：

1. 請檢查位置 %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI 中是否有 `azds.exe`。 如果有的話，請將該位置新增至 PATH 環境變數中。
2. 如果未安裝 `azds.exe`，請執行下列命令：

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>授權錯誤「Microsoft.DevSpaces/register/action」

您需要 Azure 訂用帳戶中的「擁有者」或「參與者」權限才能管理 Azure Dev Spaces。 如果您嘗試管理 Dev Spaces，卻沒有相關聯 Azure 訂閱的「擁有者」或「參與者」權限，就可能會看到授權錯誤。 例如：

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

若要修正此問題，請使用擁有 Azure 訂閱「擁有者」或「參與者」存取權的帳戶，手動註冊 `Microsoft.DevSpaces` 命名空間：

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>新 Pod 未啟動

Kubernetes 初始化運算式無法對新的 pod 套用 PodSpec，因為對叢集中叢集 *管理員* 角色的 Kubernetes RBAC 許可權變更。 新的 Pod 可能也會有不正確 PodSpec，例如與 Pod 相關聯的服務帳戶已不存在。 若要查看因初始化運算式問題而處於「擱置」狀態的 Pod，請使用 `kubectl get pods` 命令：

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

此問題可能會影響在叢集的所有命名空間之中的 Pod，包括未啟用 Azure Dev Spaces 的命名空間。

若要修正此問題，請[將 Dev Spaces CLI 更新為最新版本](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools)然後從 Azure Dev Spaces 控制器刪除 azds InitializerConfiguration：

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

當您從 Azure Dev Spaces 控制器移除 azds InitializerConfiguration 後，請使用 `kubectl delete` 移除「擱置」狀態中的任何 Pod。 移除所有擱置的 Pod 之後，請重新部署您的 Pod。

如果在重新部署後，新的 Pod 仍停滯在「擱置」狀態，請使用 `kubectl delete` 移除「擱置」狀態中的任何 Pod。 移除所有擱置的 Pod 之後，請從叢集刪除控制器並重新安裝：

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

重新安裝您的控制器之後，請重新部署您的 Pod。

### <a name="incorrect-azure-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>呼叫 Dev Spaces 控制器和 Api 的 Azure RBAC 許可權不正確

存取 Azure Dev Spaces 控制器的使用者必須擁有讀取 AKS 叢集上的系統管理員 kubeconfig 所需的存取權。 例如，[內建 Azure Kubernetes Service 叢集系統管理員角色](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)提供此權限。 存取 Azure Dev Spaces 控制器的使用者也必須擁有該控制器的 *參與者* 或 *擁有* 者 Azure 角色。 如需更新 AKS 叢集使用者權限的詳細資訊，請參閱[這裡](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)。

若要為控制器更新使用者的 Azure 角色：

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 瀏覽至包含控制器的資源群組，這通常與您的 AKS 叢集相同。
1. 啟用 [顯示隱藏的類型] 核取方塊。
1. 按一下控制器。
1. 開啟 [存取控制 (IAM)] 窗格。
1. 按一下 [角色指派] 索引標籤。
1. 按一下 [新增]，然後按一下 [新增角色指派]。
    * 針對 [角色]，請選取 [參與者] 或 [擁有者]。
    * 在 [存取權指派對象為] 中，選取 [Azure AD 使用者、群組或服務主體]。
    * 針對 [選取]，搜尋您想要授與權限的使用者。
1. 按一下 [檔案] 。

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>與 Dev Spaces 服務相關聯的公用 URL 進行 DNS 名稱解析失敗

您可以藉由對 `azds prep` 命令指定 `--enable-ingress` 參數，或藉由在 Visual Studio 中選取 `Publicly Accessible` 核取方塊，來為服務設定公用 URL 端點。 當您在 Dev Spaces 中執行服務時，公用 DNS 名稱會自動完成註冊。 如果此 DNS 名稱未完成註冊，您就會在連線至公用 URL 時，於網頁瀏覽器中看到「頁面無法顯示」或「網站無法連線」的錯誤。

若要修正此問題：

* 檢查與您的開發空間服務相關聯的所有 URL 狀態：

  ```console
  azds list-uris
  ```

* 如果 URL 處於「擱置」狀態，表示 Dev Spaces 仍在等候 DNS 註冊完成。 有時需要幾分鐘的時間才能完成註冊。 Dev Spaces 也會為每個服務開啟一個 localhost 通道，您在等候 DNS 登錄完成時可以使用此通道。
* 如果 URL 處於「擱置」狀態超過 5 分鐘，可能表示建立公用端點的外部 DNS Pod 或取得公用端點的 nginx 輸入控制器 Pod 有問題。 使用下列命令來刪除這些 Pod，並允許 AKS 自動重新建立這些 Pod：
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>錯誤「上游連線錯誤或是在標頭前中斷連線/重設」

您在嘗試存取服務時，可能會看到這個錯誤。 例如，當您在瀏覽器中移至服務的 URL 時。 此錯誤表示容器連接埠無法使用。 這可能是因為下列原因：

* 容器仍處於建置和部署程序。 如果您執行 `azds up` 或啟動偵錯工具，然後在容器成功部署之前嘗試存取容器，就有可能發生這個問題。
* 您的 _Dockerfile_ 、Helm 圖表及開啟連接埠的任何伺服器代碼之間的連接埠組態不一致。

若要修正此問題：

1. 如果容器處於建置/部署程序，您可以等待 2-3 秒，然後再次嘗試存取服務。 
1. 檢查下列資產中的連接埠設定：
    * **[Helm 圖表](https://docs.helm.sh)：** 以 `azds prep` 命令建立結構的 values.yaml scaffolded 中的 `service.port` 和 `deployment.containerPort` 指定。
    * 在應用程式程式碼中開啟的任何連接埠，例如在 Node.js 中：`var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>找不到類型或命名空間名稱「MyLibrary」

找不到您要使用的程式庫專案。 使用 Dev Spaces 時，組建內容預設為專案/服務層級。  

若要修正此問題：

1. 修改 `azds.yaml` 檔案以將建置內容設定到解決方案層級。
2. 修改 `Dockerfile` 和 `Dockerfile.develop` 檔案以參考專案檔案，並正確對應到新的建置內容，例如 `.csproj`。
3. 在與 `.sln` 檔案相同的目錄中新增 `.dockerignore`。
4. 視需要使用其他項目來更新 `.dockerignore`。

您可以在[此處](https://github.com/sgreenmsft/buildcontextsample)找到範例

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>水平 Pod 自動調整無法在開發空間中運作

當您在開發空間中執行服務時，會[插入該服務的 Pod，並以其他容器進行檢測](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster)，而且 Pod 中的所有容器都必須具有針對水平 Pod 自動調整而設定的資源限制和要求。

若要修正此問題，請將資源要求和限制套用於插入的 Dev Spaces 容器。 藉由將 `azds.io/proxy-resources` 註釋新增至 Pod 規格，可對插入的容器 (devspaces Proxy) 套用資源要求和限制。值應該設定為 JSON 物件，代表 Proxy 的容器規格之中的 resources 區段。

以下是要套用於 Pod 規格的 Proxy 資源註釋範例。
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>在具有執行中 Pod 的現有命名空間上啟用 Azure Dev Spaces

您可能會有現有的 AKS 叢集和命名空間，以及要啟用 Azure Dev Spaces 的執行中 Pod。

若要在 AKS 叢集中的現有命名空間上啟用 Azure Dev Spaces，請執行 `use-dev-spaces`，並使用 `kubectl` 重新啟動該命名空間中的所有 Pod。

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

在您的 Pod 重新啟動之後，您就可以開始使用現有的命名空間與 Azure Dev Spaces。

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>使用叢集節點的受限輸出流量，在 AKS 叢集上啟用 Azure Dev Spaces

若要在 AKS 叢集上啟用 Azure Dev Spaces，以限制來自叢集節點的輸出流量，您必須允許下列 FQDN：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | 提取 Linux Alpine 和其他 Azure Dev Spaces 映像 |
| gcr.io | HTTP:443 | 提取 helm/tiller 映射|
| storage.googleapis.com | HTTP:443 | 提取 helm/tiller 映射|

更新您的防火牆或安全性設定，以允許與上述所有 Fqdn 和 [Azure Dev Spaces 基礎結構服務](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations)之間的網路流量。

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>「 \<cluster\> 在訂用帳戶中找不到叢集」錯誤 \<subscriptionId\>

如果您的 kubeconfig 檔案針對的叢集或訂閱不同於搭配 Azure Dev Spaces 用戶端工具使用的叢集或訂閱，您可能會看到此錯誤。 Azure Dev Spaces 的用戶端工具會複寫 *kubectl* 的行為，這會使用 [一個或多個 kubeconfig 檔案](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)來選取叢集並與叢集進行通訊。

若要修正此問題：

* 使用 `az aks use-dev-spaces -g <resource group name> -n <cluster name>` 來更新目前的內容。 如果尚未啟用，此命令也會在 AKS 叢集上啟用 Azure Dev Spaces。 或者，您可以使用 `kubectl config use-context <cluster name>` 來更新目前的內容。
* 使用 `az account show` 顯示您目前目標的 Azure 訂閱，並確認正確無誤。 您可以使用 `az account set` 變更目標訂閱。

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>輪替 AKS 憑證後使用 Dev Spaces 時發生錯誤

在 AKS 叢集中[輪替憑證](../aks/certificate-rotation.md)後，某些作業 (例如 `azds space list` 和 `azds up`) 會失敗。 在您的叢集上輪替憑證之後，您也需要重新整理 Azure Dev Spaces 控制器上的憑證。

若要修正此問題，請確定您的 *kubeconfig* 已使用 `az aks get-credentials` 更新的憑證，然後執行 `azds controller refresh-credentials` 命令。 例如：

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
