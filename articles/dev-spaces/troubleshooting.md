---
title: 疑難排解
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: 瞭解如何在啟用和使用 Azure Dev Spaces 時，疑難排解和解決常見的問題
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: 0cf8eb7b07622a989bc78637b1601ba68b9b5f6f
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251114"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces 疑難排解

本指南包含您在使用 Azure Dev Spaces 時可能會遇到的常見問題相關資訊。

如果您在使用 Azure Dev Spaces 時遇到問題，請[在 Azure Dev Spaces GitHub 存放庫中建立問題](https://github.com/Azure/dev-spaces/issues)。

## <a name="before-you-begin"></a>開始之前

為了更有效地進行問題的疑難排解，建立更詳細的記錄以供檢閱可能會有幫助。

針對 Visual Studio 延伸模組，請將 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 環境變數設定為 1。 請務必重新啟動 Visual Studio，讓環境變數生效。 啟用之後，詳細記錄會寫入至您的 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 目錄。

在命令執行期間，您可以在 CLI 中使用 `--verbose` 參數輸出更多資訊。 您也可以在 `%TEMP%\Azure Dev Spaces` 中瀏覽更多詳細的記錄。 在 Mac 上，您可以從終端機視窗執行 `echo $TMPDIR` 來找到*暫存*目錄。 在 Linux 電腦上，*暫存*目錄通常是 `/tmp`。 此外，請確認您的[Azure CLI 設定檔](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)案中已啟用記錄。

Azure Dev Spaces 在對單一實例或 pod 進行調試時，也能發揮最佳效果。 `azds.yaml` 檔案包含一個設定*replicaCount*，表示 Kubernetes 為您的服務執行的 pod 數目。 如果您變更*replicaCount*以將應用程式設定為針對指定的服務執行多個 pod，則偵錯工具會在依字母順序列出時附加至第一個 pod。 偵錯工具會在該原始 Pod 回收時連結至不同的 Pod，而可能導致非預期的行為。

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>啟用 Azure Dev Spaces 時常見的問題

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>「無法建立 Azure Dev Spaces 控制器」錯誤

當發生錯誤而無法建立控制器時，您會看到此錯誤。 如果錯誤是暫時性的，刪除並重新建立控制器即可加以修正。

您也可以嘗試刪除控制器：

```bash
azds remove -g <resource group name> -n <cluster name>
```

使用 Azure Dev Spaces CLI 來刪除控制器。 不可能從 Visual Studio 刪除控制器。 您也無法在 Azure Cloud Shell 中安裝 Azure Dev Spaces CLI，因此無法從 Azure Cloud Shell 中刪除控制器。

如果您未安裝 Azure Dev Spaces CLI，您可以先使用下列命令來安裝它，然後刪除您的控制器：

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

重新建立控制器的程序可透過 CLI 或 Visual Studio 來完成。 如需範例，請參閱[使用 .Net Core](quickstart-netcore-visualstudio.md)快速入門進行[小組開發](quickstart-team-development.md)或開發。

### <a name="controller-create-failing-because-of-controller-name-length"></a>控制器建立失敗，因為控制器名稱長度

Azure Dev Spaces 控制器的名稱長度不能超過31個字元。 當您在 AKS 叢集上啟用 Dev Spaces 或建立控制器時，如果您的控制器名稱超過31個字元，就會收到錯誤。 例如，

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

若要修正此問題，請使用替代名稱建立控制器。 例如，

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>將 Windows 節點集區新增至 AKS 叢集時啟用 Dev Spaces 失敗

目前，Azure Dev Spaces 僅適用于在 Linux pod 和節點上執行。 當您的 AKS 叢集具有 Windows 節點集區時，您必須確定只在 Linux 節點上排程 Azure Dev Spaces pod。 如果 Azure Dev Spaces pod 已排程在 Windows 節點上執行，該 pod 將不會啟動，且啟用 Dev Spaces 將會失敗。

若要修正此問題，請[將污點新增](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)至您的 AKS 叢集，以確保 Linux pod 不會排程在 Windows 節點上執行。

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>「在叢集上找不到處于就緒狀態的 untainted Linux 節點」錯誤。 必須至少有一個 untainted Linux 節點處於 [就緒] 狀態，才能在「azds」命名空間中部署 pod。」

Azure Dev Spaces 無法在 AKS 叢集上建立控制器，因為它找不到處于*就緒*狀態的 untainted 節點來排程 pod。 Azure Dev Spaces 需要至少一個處於 [*就緒*] 狀態的 Linux 節點，以便在不指定容差的情況下排程 pod。

若要修正此問題，請在您的 AKS 叢集上[更新您的污點](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)設定，以確保至少有一個 Linux 節點允許排程 pod，而不指定容差。 此外，請確定至少有一個 Linux 節點允許在不指定容差的情況下排程 pod，處於*就緒*狀態。 如果您的節點花費較長的時間來達到 [*就緒*] 狀態，您可以嘗試重新開機您的節點。

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>執行 `az aks use-dev-spaces` 時發生「Azure Dev Spaces CLI 未正確安裝」錯誤

Azure Dev Spaces CLI 的更新已變更其安裝路徑。 如果您使用2.0.63 之前的 Azure CLI 版本，您可能會看到此錯誤。 若要顯示您的 Azure CLI 版本，請使用 `az --version`。

```azurecli
az --version
azure-cli                         2.0.60 *
...
```

儘管在2.0.63 之前使用 Azure CLI 版本執行 `az aks use-dev-spaces` 時，仍會出現錯誤訊息，但安裝會成功。 您可以繼續使用 `azds` 而不會有任何問題。

若要修正此問題，請將[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)安裝更新至2.0.63 或更新版本。 此更新將會解決您在執行 `az aks use-dev-spaces`時所收到的錯誤訊息。 或者，您可以繼續使用目前版本的 Azure CLI 和 Azure Dev Spaces CLI。

### <a name="error-unable-to-reach-kube-apiserver"></a>「無法到達 kube-kube-apiserver」錯誤

當 Azure Dev Spaces 無法連接到 AKS 叢集的 API 伺服器時，您可能會看到此錯誤。 

如果您的 AKS 叢集 API 伺服器的存取權已遭鎖定，或您已為您的 AKS 叢集啟用[API 伺服器授權的 IP 位址範圍](../aks/api-server-authorized-ip-ranges.md)，則您也必須[建立](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled)或[更新](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges)叢集，以[允許根據您的區域的其他範圍](https://github.com/Azure/dev-spaces/tree/master/public-ips)。

執行 kubectl 命令以確定 API 伺服器可供使用。 如果 API 伺服器無法使用，請洽詢 AKS 支援服務，並在 API 伺服器運作時再試一次。

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>準備專案以進行 Azure Dev Spaces 時常見的問題

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>警告「由於不支援的語言而無法產生 Dockerfile」
Azure Dev Spaces 提供 C# 和 Node.js 的原生支援。 當您使用其中一種語言撰寫的程式碼在目錄中執行 `azds prep` 時，Azure Dev Spaces 會自動為您建立適當的 Dockerfile。

您仍然可以將 Azure Dev Spaces 與以其他語言撰寫的程式碼搭配使用，但是您必須在第一次執行 `azds up` 之前手動建立 Dockerfile。

如果您的應用程式是以 Azure Dev Spaces 原本不支援的語言撰寫，您必須提供適當的 Dockerfile 來建立執行程式碼的容器映射。 Docker 提供[撰寫 Dockerfile 的最佳做法清單](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)及 [Dockerfile 參考](https://docs.docker.com/engine/reference/builder/)，可協助您撰寫符合需求的 Dockerfile。

備妥適當的 Dockerfile 之後，您就可以執行 `azds up`，在 Azure Dev Spaces 中執行應用程式。

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 啟動或停止服務時的常見問題

### <a name="error-config-file-not-found"></a>錯誤「找不到設定檔：」

執行 `azds up`時，您可能會看到此錯誤。 `azds up` 和 `azds prep` 都必須從您要在開發人員空間中執行之專案的根目錄中執行。

若要修正此問題：
1. 將目前目錄變更為內含服務程式碼的根資料夾。 
1. 如果您在程式碼資料夾中沒有_azds yaml_檔案，請執行 `azds prep` 以產生 Docker、Kubernetes 和 Azure Dev Spaces 資產。

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>「正在等候容器映射組建 ...」的超時使用 AKS 虛擬節點的步驟

當您嘗試使用 Dev Spaces 來執行設定為在[AKS 虛擬節點](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)上執行的服務時，就會發生此超時。 Dev Spaces 目前不支援在虛擬節點上建立或調試服務。

如果您執行 `azds up` 並搭配 `--verbose` 參數，或在 Visual Studio 中啟用詳細資訊記錄，則會看到其他詳細資料：

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

上述命令顯示服務的 pod 已指派給*虛擬節點-aci-linux*，也就是虛擬節點。

若要修正此問題，請更新服務的 Helm 圖表，以移除允許服務在虛擬節點上執行的任何*nodeSelector*或*容差*值。 這些值通常會定義在圖表的 `values.yaml` 檔案中。

如果您想要透過開發人員空間在 VM 節點上執行的服務，則您仍然可以使用已啟用虛擬節點功能的 AKS 叢集。 在 VM 節點上執行具有 Dev Spaces 的服務是預設設定。

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>啟動 Dev Spaces 時發生錯誤「找不到就緒的 tiller pod」

如果 Helm 用戶端無法再與叢集中執行的 Tiller Pod 通訊，就會發生此錯誤。

若要修正此問題，請重新開機叢集中的代理程式節點。

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>錯誤「release azds-\<識別碼\>-\<spacename\>-\<servicename\> 失敗：服務 '\<servicename\>' 已存在」，或「拒絕 \<servicename\>的提取存取權，存放庫不存在，或可能需要 ' docker login ' '

如果您在相同的開發人員空間內混合執行直接 Helm 命令（例如 `helm install`、`helm upgrade`或 `helm delete`）與 Dev Spaces 命令（例如 `azds up` 和 `azds down`），就會發生這些錯誤。 因為 Dev Spaces 有自己的 Tiller 實例，所以會與您在相同開發人員空間中執行的自有 Tiller 實例相衝突，因此會發生這種情況。

您可以針對相同的 AKS 叢集使用 Helm 命令和 Dev Spaces 命令，但是每個啟用 Dev Spaces 的命名空間都應該使用其中一個。

例如，假設您使用 Helm 命令，在父開發人員空間中執行整個應用程式。 您可以建立該父系的子開發人員空間，使用 Dev Spaces 在子開發人員空間內執行個別服務，並一起測試服務。 當您準備好簽入變更時，請使用 Helm 命令，將更新的程式碼部署至父開發人員空間。 請勿使用 `azds up` 在父開發人員空間中執行更新的服務，因為它會與使用 Helm 一開始執行的服務發生衝突。

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>現有的 Dockerfile 未用來建立容器

Azure Dev Spaces 可以設定為指向您專案中的特定 _Dockerfile_。 如果發生 Azure Dev Spaces 未使用您預期的 Dockerfile 來建置容器的情形，您可能需要明確告訴 Azure Dev Spaces 要使用哪個 Dockerfile。 

若要修正此問題，請開啟 Azure Dev Spaces 在專案中產生的_yaml_檔案。 更新設定： [開發]： [*組建]： dockerfile*以指向您想要使用的 dockerfile。 例如，

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>嘗試從私人登錄使用 Docker 映射時發生「未經授權：需要驗證」錯誤

您使用的是需要驗證的私人登錄中的 Docker 映射。

若要修正此問題，您可以使用[imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)，允許 Dev Spaces 驗證及提取此私人登錄中的映射。 若要使用 imagePullSecrets，請在您要使用映射的命名空間中[建立 Kubernetes 秘密](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)。 然後在 `azds.yaml`中提供密碼做為 imagePullSecret。

以下是在 `azds.yaml`中指定 imagePullSecrets 的範例。

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
> 在 `azds.yaml` 中設定 imagePullSecrets 將會覆寫 `values.yaml`中指定的 imagePullSecrets。

### <a name="error-service-cannot-be-started"></a>「無法啟動服務」錯誤。

當您的服務程式碼無法啟動時，您可能會看到此錯誤訊息。 通常是使用者程式碼所造成的。 若要取得更多診斷資訊，請在啟動服務時啟用更詳細的記錄。

從命令列中，使用 `--verbose` 來啟用更詳細的記錄。 您也可以使用 `--output`來指定輸出格式。 例如，

```cmd
azds up --verbose --output json
```

在 Visual Studio 中：

1. 開啟 [工具] > [選項]，然後在 [專案和解決方案] 底下選擇 [建置並執行]。
2. 將 **MSBuild 專案建置輸出詳細資訊**的設定變更為 [詳細] 或 [診斷]。

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

之所以發生此錯誤，是因為移除 Dev Spaces 控制器並不會移除該控制器先前所安裝的服務。 重新建立控制器後再嘗試使用新控制器來執行服務會失敗，因為舊服務仍在原位。

若要解決此問題，請使用 `kubectl delete` 命令來手動從叢集中移除舊服務，然後重新執行 Dev Spaces 以安裝新服務。

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>「無法啟動服務」錯誤。 使用多階段 Dockerfile 時

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

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>連線到您的開發電腦時，網路流量不會轉送到您的 AKS 叢集

當您使用[Azure Dev Spaces 將 AKS 叢集連接到開發電腦](how-to/connect.md)時，可能會遇到不會在您的開發電腦和 AKS 叢集之間轉送網路流量的問題。

將您的開發電腦連接到 AKS 叢集時，Azure Dev Spaces 藉由修改開發電腦的 `hosts` 檔，來轉送 AKS 叢集與開發電腦之間的網路流量。 Azure Dev Spaces 會在 `hosts` 中建立一個專案，其中包含您要取代為主機名之 Kubernetes 服務的位址。 此專案會與埠轉送搭配使用，以引導您的開發電腦與 AKS 叢集之間的網路流量。 如果開發電腦上的服務與您要取代之 Kubernetes 服務的埠發生衝突，Azure Dev Spaces 無法轉送 Kubernetes 服務的網路流量。 例如， *Windows BranchCache*服務通常會系結至*0.0.0.0： 80*，而衝突會導致所有本機 ip 上的埠80發生衝突。

若要修正此問題，您必須停止與您嘗試取代的 Kubernetes 服務埠衝突的任何服務或處理常式。 您可以使用工具（例如*netstat*）來檢查開發電腦上的哪些服務或處理常式衝突。

例如，若要停止和停用*Windows BranchCache*服務：
* 從命令提示字元執行 `services.msc`。
* 以滑鼠右鍵按一下 [ *BranchCache* ]，然後選取 [*屬性*]。
* 按一下 [*停止*]。
* （選擇性）您可以將 [*啟動類型*] 設為 [*停用*] 來停用它。
* 按一下 [確定]。

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>使用 Visual Studio 和 Visual Studio Code 搭配 Azure Dev Spaces 的常見問題

### <a name="error-required-tools-and-configurations-are-missing"></a>錯誤「缺少必要的工具和設定」

啟動 VS Code 時可能會發生下列錯誤：「[Azure Dev Spaces] 遺失建置 '[專案名稱]' 及對其進行偵錯的必要工具和組態」。
此錯誤訊息表示 azds.exe 不在 PATH 環境變數中，如同 VS Code 中所示。

嘗試從已正確設定 PATH 環境變數的命令提示字元啟動 VS Code。

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>錯誤「建置 'projectname' 並對其進行偵錯所需的工具已過時。」

如果您有適用於 Azure Dev Spaces 的新版 VS Code 延伸模組，但 Azure Dev Spaces CLI 為舊版，就會在 Visual Studio Code 中看到此錯誤。

請嘗試下載並安裝最新版的 Azure Dev Spaces CLI：

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>錯誤：「找不到類型： coreclr 的偵錯工具延伸模組」

執行 Visual Studio Code 偵錯工具時，您可能會看到此錯誤。 您可能沒有C#安裝在開發電腦上的 VS Code 延伸模組。 此C#延伸模組包含 .net Core （CoreCLR）的偵錯工具支援。

若要修正此問題，請安裝的[VS Code C#延伸](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)模組。

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>「不支援已設定的 debug 類型 ' coreclr '」錯誤

執行 Visual Studio Code 偵錯工具時，您可能會看到此錯誤。 您可能沒有在開發電腦上安裝 Azure Dev Spaces 的 VS Code 延伸模組。

若要修正此問題，請安裝[Azure Dev Spaces 的 VS Code 延伸](get-started-netcore.md)模組。

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>錯誤「不正確 ' cwd ' 值 '/src '。 系統找不到指定的檔案。」 或「launch: program '/src/[專案二進位檔案路徑]' 不存在」

執行 Visual Studio Code 偵錯工具時，您可能會看到此錯誤。 VS Code 延伸模組預設會使用 `src` 作為容器上專案的工作目錄。 如果您已將 `Dockerfile` 更新成指定不同的工作目錄，就可能看到此錯誤。

若要修正此問題，請更新專案資料夾的 `.vscode` 子目錄下的 `launch.json` 檔案。 將 `configurations->cwd` 指示詞變更成指向與您專案之 `WORKDIR`.中所定義 `Dockerfile` 相同的目錄。 您可能也需要更新 `configurations->program` 指示詞。

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>「管道程式 ' azds ' 意外結束並出現代碼126」錯誤。

執行 Visual Studio Code 偵錯工具時，您可能會看到此錯誤。

若要修正此問題，請關閉 Visual Studio Code，然後重新開啟。 重新開機偵錯工具。

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>將偵錯工具附加至 node.js 應用程式時發生錯誤「內部監看失敗：監看 ENOSPC」

當執行 pod 的節點與您嘗試附加的 node.js 應用程式已超過*max_user_watches inotifypropertychanged*值時，就會發生此錯誤。 在某些情況下， [ *max_user_watches inotifypropertychanged*的預設值可能太小，無法直接處理將偵錯工具附加至 pod](https://github.com/Azure/AKS/issues/772)。

此問題的暫時因應措施是增加*max_user_watches inotifypropertychanged*在叢集中每個節點上的值，然後重新開機該節點，讓變更生效。

## <a name="other-common-issues"></a>其他常見問題

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>錯誤 "azds" 無法辨識為內部或外部命令、可運作的程式或批次檔

如果未正確安裝或設定 `azds.exe`，就會發生此錯誤。

若要修正此問題：

1. 請檢查位置% ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI 以取得 `azds.exe`。 如果有的話，請將該位置新增至 PATH 環境變數中。
2. 如果未安裝 `azds.exe`，請執行下列命令：

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>授權錯誤 "Microsoft. DevSpaces/register/action"

您需要 Azure 訂用帳戶中的「擁有者」或「參與者」權限才能管理 Azure Dev Spaces。 如果您嘗試管理 Dev Spaces，但沒有相關聯 Azure 訂用帳戶的*擁有*者或*參與者*存取權，您可能會看到授權錯誤。 例如，

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

若要修正此問題，請使用具有「*擁有*者」或「*參與者*」存取 Azure 訂用帳戶的帳戶，手動註冊 `Microsoft.DevSpaces` 命名空間：

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>新 pod 未啟動

Kubernetes 初始化運算式無法針對新的 pod 套用 PodSpec，因為叢集中對叢集*管理員*角色的 RBAC 許可權變更。 新的 pod 可能也會有不正確 PodSpec，例如與 pod 相關聯的服務帳戶已不存在。 若要查看因初始化運算式問題而處於*擱置*狀態的 pod，請使用 `kubectl get pods` 命令：

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

此問題可能會影響叢集中*所有命名空間*中的 pod，包括未啟用 Azure Dev Spaces 的命名空間。

若要修正此問題，請[將 Dev SPACES CLI 更新為最新版本](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools)，然後從 Azure Dev Spaces 控制器中刪除*azds InitializerConfiguration* ：

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

從 Azure Dev Spaces 控制器移除*Azds InitializerConfiguration*之後，請使用 `kubectl delete` 移除處於*擱置*狀態的任何 pod。 移除所有擱置中的 pod 之後，請重新部署您的 pod。

如果新的 pod 在重新部署後仍處於*擱置*狀態，請使用 `kubectl delete` 移除處於*擱置*狀態的任何 pod。 移除所有擱置中的 pod 之後，請從叢集刪除控制器並重新安裝：

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

重新安裝您的控制器之後，請重新部署您的 pod。

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>呼叫 Dev Spaces 控制器和 Api 的 RBAC 許可權不正確

存取 Azure Dev Spaces 控制器的使用者必須具有讀取 AKS 叢集上系統管理員*kubeconfig*的存取權。 例如，您可以在內建的 Azure Kubernetes Service 叢集系統[管理員角色](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)中取得此許可權。 存取 Azure Dev Spaces 控制器的使用者也必須擁有該控制器的「*參與者*」或「*擁有*者」 RBAC 角色。 如需更新使用者 AKS 叢集許可權的詳細資訊，請參閱[這裡](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)。

若要更新該控制器的使用者 RBAC 角色：

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 流覽至包含控制器的資源群組，這通常與您的 AKS 叢集相同。
1. 啟用 [*顯示隱藏的類型*] 核取方塊。
1. 按一下控制器。
1. 開啟 [*存取控制（IAM）* ] 窗格。
1. 按一下 [*角色指派*] 索引標籤。
1. 依序按一下 [*新增*] 和 [*新增角色指派*]。
    * 針對 [*角色*]，選取 [*參與者*] 或 [*擁有*者]。
    * 在 [存取權指派對象為] 中，選取 [Azure AD 使用者、群組或服務主體]。
    * 針對 [*選取*]，搜尋您想要授與許可權的使用者。
1. Haga clic en *Guardar*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>與 Dev Spaces 服務相關聯的公用 URL 進行 DNS 名稱解析失敗

您可以藉由對 `--enable-ingress` 命令指定 `azds prep` 參數，或藉由在 Visual Studio 中選取 `Publicly Accessible` 核取方塊，來為服務設定公用 URL 端點。 當您在 Dev Spaces 中執行服務時，公用 DNS 名稱會自動完成註冊。 如果此 DNS 名稱未完成註冊，您就會在連線至公用 URL 時，於網頁瀏覽器中看到「頁面無法顯示」或「網站無法連線」的錯誤。

若要修正此問題：

* 檢查與您的開發人員空間服務相關聯之所有 Url 的狀態：

  ```console
  azds list-uris
  ```

* 如果 URL 處於「*擱置*」狀態，則 Dev Spaces 仍在等候 DNS 註冊完成。 有時需要幾分鐘的時間才能完成註冊。 Dev Spaces 也會為每個服務開啟一個 localhost 通道，您在等候 DNS 登錄完成時可以使用此通道。
* 如果 URL 處於「擱置」狀態超過 5 分鐘，可能表示建立公用端點的外部 DNS Pod 或取得公用端點的 nginx 輸入控制器 Pod 有問題。 使用下列命令來刪除這些 pod，並允許 AKS 自動重新建立它們：
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>錯誤「上游連接錯誤或在標頭之前中斷連線/重設」

您在嘗試存取服務時，可能會看到這個錯誤。 例如，當您在瀏覽器中移至服務的 URL 時。 此錯誤表示容器埠無法使用。 這可能是因為下列原因：

* 容器仍處於建置和部署程序。 如果您執行 `azds up` 或啟動偵錯工具，然後在容器成功部署之前嘗試存取容器，就有可能發生這個問題。
* 您的 _Dockerfile_、Helm 圖表及開啟連接埠的任何伺服器代碼之間的連接埠組態不一致。

若要修正此問題：

1. 如果容器處於建置/部署程序，您可以等待 2-3 秒，然後再次嘗試存取服務。 
1. 檢查下列資產中的埠設定：
    * **[Helm 圖](https://docs.helm.sh)：** 由 `service.port` 所指定，並 `deployment.containerPort` yaml scaffold by `azds prep` 命令。
    * 在應用程式程式碼中開啟的任何連接埠，例如在 Node.js 中：`var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>找不到類型或命名空間名稱 "MyLibrary"

找不到您要使用的程式庫專案。 使用 Dev Spaces 時，組建內容預設為專案/服務層級。  

若要修正此問題：

1. 修改 `azds.yaml` 檔案，將組建內容設定為方案層級。
2. 修改 `Dockerfile` 和 `Dockerfile.develop` 檔案來參考專案檔，例如 `.csproj`，正確地相對於新的組建內容。
3. 在與 `.sln` 檔案相同的目錄中新增 `.dockerignore`。
4. 視需要使用其他專案來更新 `.dockerignore`。

您可以在[這裡](https://github.com/sgreenmsft/buildcontextsample)找到範例。

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>水準 pod 自動調整無法在開發人員空間中運作

當您在開發人員空間中執行服務時，該服務的 pod 會[插入其他容器以進行檢測](how-dev-spaces-works.md#prepare-your-aks-cluster)，而 pod 中的所有容器都必須針對水準 pod 自動調整設定資源限制和要求。

若要修正此問題，請將資源要求和限制套用至插入的 Dev Spaces 容器。 藉由將 `azds.io/proxy-resources` 注釋新增至 pod 規格，可對插入的容器（devspaces proxy）套用資源要求和限制。值應該設定為 JSON 物件，代表 proxy 之容器規格的 resources 區段。

以下是要套用至 pod 規格的 proxy 資源注釋範例。
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>在具有執行中 pod 的現有命名空間上啟用 Azure Dev Spaces

您可能會有現有的 AKS 叢集和命名空間，以及要啟用 Azure Dev Spaces 的執行中 pod。

若要在 AKS 叢集中的現有命名空間上啟用 Azure Dev Spaces，請執行 `use-dev-spaces`，並使用 `kubectl` 重新開機該命名空間中的所有 pod。

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

在您的 pod 重新開機之後，您就可以開始使用現有的命名空間與 Azure Dev Spaces。

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>使用叢集節點的受限輸出流量，在 AKS 叢集上啟用 Azure Dev Spaces

若要在 AKS 叢集上啟用 Azure Dev Spaces，以限制來自叢集節點的輸出流量，您必須允許下列 Fqdn：

| FQDN                                    | Port      | 用法      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS：443 | 提取 linux alpine 和其他 Azure Dev Spaces 映射 |
| gcr.io | HTTP：443 | 提取 helm/tiller 映射|
| storage.googleapis.com | HTTP：443 | 提取 helm/tiller 映射|
| azds-<guid>。<location>. azds.io | HTTPS：443 | 與您的控制器 Azure Dev Spaces 後端服務進行通訊。 您可以在% USERPROFILE%\.azds\settings.json 的 "dataplaneFqdn" 中找到確切的 FQDN|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>錯誤「找不到訂用帳戶 \<subscriptionId\>」中的叢集 \<叢集\>

如果您的 kubeconfig 檔案是以不同于嘗試與 Azure Dev Spaces 用戶端工具搭配使用的叢集或訂用帳戶為目標，您可能會看到此錯誤。 Azure Dev Spaces 的用戶端工具會複寫*kubectl*的行為，這會使用[一或多個 kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)檔案來選取和與叢集通訊。

若要修正此問題：

* 使用 `az aks use-dev-spaces -g <resource group name> -n <cluster name>` 來更新目前的內容。 如果尚未啟用，此命令也會在 AKS 叢集上啟用 Azure Dev Spaces。 或者，您可以使用 `kubectl config use-context <cluster name>` 來更新目前的內容。
* 使用 `az account show` 顯示您目前目標的 Azure 訂用帳戶，並確認這是正確的。 您可以使用 `az account set`變更目標訂用帳戶。

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>輪替 AKS 憑證後使用 Dev Spaces 時發生錯誤

在[AKS 叢集中輪替憑證](../aks/certificate-rotation.md)之後，某些作業（例如 `azds space list` 和 `azds up`）將會失敗。 在您的叢集上輪替憑證之後，您也需要重新整理 Azure Dev Spaces 控制器上的憑證。

若要修正此問題，請確定您的*kubeconfig*已使用 `az aks get-credentials` 更新的憑證，然後執行 `azds controller refresh-credentials` 命令。 例如，

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
