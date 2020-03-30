---
title: 疑難排解
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: 瞭解如何在啟用和使用 Azure 開發人員空間時疑難排解並解決常見問題
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: c12dfd385962d8dd7de8239a0d4ecd46746499c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239774"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure 開發空間故障排除

本指南包含您在使用 Azure Dev Spaces 時可能會遇到的常見問題相關資訊。

如果在使用 Azure 開發空間時遇到問題，請[在 Azure 開發空間 GitHub 存儲庫中創建問題](https://github.com/Azure/dev-spaces/issues)。

## <a name="before-you-begin"></a>開始之前

為了更有效地進行問題的疑難排解，建立更詳細的記錄以供檢閱可能會有幫助。

針對 Visual Studio 延伸模組，請將 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 環境變數設定為 1。 請務必重新啟動 Visual Studio，讓環境變數生效。 啟用之後，詳細記錄會寫入至您的 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 目錄。

在命令執行期間，您可以在 CLI 中使用 `--verbose` 參數輸出更多資訊。 您也可以在 `%TEMP%\Azure Dev Spaces` 中瀏覽更多詳細的記錄。 在 Mac 上*TEMP*，可以從終端視窗運行`echo $TMPDIR`找到 TEMP 目錄。 在 Linux 電腦上 *，TEMP*目錄通常是`/tmp`。 此外，請驗證[Azure CLI 設定檔](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)中是否啟用了日誌記錄。

在調試單個實例或窗格時，Azure 開發人員空間也效果最佳。 該檔`azds.yaml`包含一個設置，*副本Count*，指示 Kubernetes 為您的服務運行的窗格數。 如果更改*副本Count*以將應用程式佈建為為給定服務運行多個 pod，則調試器按字母順序列出時附加到第一個 Pod。 偵錯工具會在該原始 Pod 回收時連結至不同的 Pod，而可能導致非預期的行為。

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>啟用 Azure 開發空間時的常見問題

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>錯誤"無法創建 Azure 開發空間控制器"

當發生錯誤而無法建立控制器時，您會看到此錯誤。 如果錯誤是暫時性的，刪除並重新建立控制器即可加以修正。

您還可以嘗試刪除控制器：

```bash
azds remove -g <resource group name> -n <cluster name>
```

使用 Azure 開發空間 CLI 刪除控制器。 無法從視覺化工作室中刪除控制器。 也不能在 Azure 雲外殼中安裝 Azure 開發空間 CLI，因此無法從 Azure 雲外殼中刪除控制器。

如果未安裝 Azure 開發空間 CLI，則可以首先使用以下命令安裝它，然後刪除控制器：

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

重新建立控制器的程序可透過 CLI 或 Visual Studio 來完成。 有關示例，請參閱[團隊開發](quickstart-team-development.md)或使用[.NET 核心快速入門進行開發](quickstart-netcore-visualstudio.md)。

### <a name="controller-create-failing-because-of-controller-name-length"></a>控制器創建失敗，因為控制器名稱長度

Azure 開發人員空間控制器的名稱不能超過 31 個字元。 如果在 AKS 群集上啟用開發人員空間或創建控制器時，控制器的名稱超過 31 個字元，您將收到錯誤。 例如：

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

要解決此問題，請創建一個具有備用名稱的控制器。 例如：

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>將 Windows 節點池添加到 AKS 群集時啟用虛擬空間失敗

目前，Azure 開發人員空間僅用於 Linux pod 和節點。 當您具有具有 Windows 節點池的 AKS 群集時，必須確保 Azure 開發人員空間窗格僅安排在 Linux 節點上。 如果 Azure 開發人員空間窗格計畫在 Windows 節點上運行，則該窗格不會啟動，啟用開發人員空間將失敗。

要解決此問題，請向 AKS 群集[添加污點](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)，以確保 Linux pod 不會計畫在 Windows 節點上運行。

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>錯誤"未在群集上找到未受污染的 Linux 節點狀態。 需要至少有一個未受污染的 Linux 節點處於"就緒"狀態，才能在"azds"命名空間中部署 pod。

Azure 開發人員空間無法在 AKS 群集上創建控制器，因為它無法找到處于 *"就緒"* 狀態的未受污染的節點來安排 pod。 Azure 開發人員空間至少需要一個*處於"就緒"* 狀態的 Linux 節點，該節點允許在不指定節肢的情況下安排窗格。

要解決此問題，請更新 AKS 群集上的[污點配置](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)，以確保至少有一個 Linux 節點允許在不指定節肢的情況下調度 pod。 此外，請確保至少有一個 Linux 節點允許在不指定節肢的情況下調度 pod 處於 *"就緒"* 狀態。 如果節點需要很長時間才能達到 *"就緒"* 狀態，則可以嘗試重新開機節點。

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>運行 az aks 使用開發空間時出錯"Azure 開發空間 CLI 未正確安裝"

對 Azure 開發空間 CLI 的更新更改了其安裝路徑。 如果您使用的 Azure CLI 版本早于 2.0.63，您可能會看到此錯誤。 要顯示 Azure CLI 的版本，請使用`az --version`。

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

儘管在 2.0.63 之前使用 Azure CLI 版本運行時`az aks use-dev-spaces`出現錯誤訊息，但安裝確實成功。 您可以繼續使用`azds`，沒有任何問題。

要解決此問題，請將[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)的安裝更新為 2.0.63 或更高版本。 此更新將解決您在運行`az aks use-dev-spaces`時收到的錯誤訊息。 或者，可以繼續使用當前版本的 Azure CLI 和 Azure 開發空間 CLI。

### <a name="error-unable-to-reach-kube-apiserver"></a>錯誤"無法訪問庫貝-apiserver"

當 Azure 開發人員空間無法連接到 AKS 群集的 API 伺服器時，您可能會看到此錯誤。 

如果對 AKS 群集 API 伺服器的訪問被鎖定，或者如果您為 AKS 群集啟用了[API 伺服器授權的 IP 位址範圍](../aks/api-server-authorized-ip-ranges.md)，則還必須[創建](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled)或[更新](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges)群集，[以允許基於區域的其他範圍](https://github.com/Azure/dev-spaces/tree/master/public-ips)。

通過運行 kubectl 命令確保 API 伺服器可用。 如果 API 伺服器不可用，請與 AKS 支援部門聯繫，並在 API 伺服器工作時重試。

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>為 Azure 開發空間準備專案時的常見問題

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>警告"由於不支援的語言，無法生成 Dockerfile"
Azure Dev Spaces 提供 C# 和 Node.js 的原生支援。 當您在包含`azds prep`以這些語言之一編寫的代碼的目錄中運行時，Azure 開發人員空間會自動為您創建適當的 Dockerfile。

您仍然可以將 Azure 開發人員空間與其他語言編寫的代碼一起使用，但需要在第一次運行`azds up`之前手動創建 Dockerfile。

如果應用程式是用 Azure 開發人員空間不支援的語言編寫的，則需要提供適當的 Dockerfile 來生成運行代碼的容器映射。 Docker 提供[撰寫 Dockerfile 的最佳做法清單](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)及 [Dockerfile 參考](https://docs.docker.com/engine/reference/builder/)，可協助您撰寫符合需求的 Dockerfile。

獲得適當的 Dockerfile 後，將運行`azds up`以在 Azure 開發空間中運行應用程式。

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>使用 Azure 開發空間啟動或停止服務時的常見問題

### <a name="error-config-file-not-found"></a>錯誤"未找到"設定檔"：

運行`azds up`時，您可能會看到此錯誤。 `azds up`和`azds prep`都必須從要在開發空間中運行的專案的根目錄運行。

若要修正此問題：
1. 將目前目錄變更為內含服務程式碼的根資料夾。 
1. 如果代碼資料夾中沒有_azds.yaml_檔，請運行`azds prep`以生成 Docker、Kubernetes 和 Azure 開發人員空間資源。

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>超時在"等待容器映射生成..."使用 AKS 虛擬節點的步驟

當您嘗試使用開發人員空間回合組態為在[AKS 虛擬節點](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)上運行的服務時，將發生此超時。 開發空間目前不支援在虛擬節點上構建或調試服務。

如果您執行 `azds up` 並搭配 `--verbose` 參數，或在 Visual Studio 中啟用詳細資訊記錄，則會看到其他詳細資料：

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

上述命令顯示服務的 pod 已分配給*虛擬節點 aci-linux，* 這是一個虛擬節點。

要解決此問題，請更新服務的 Helm 圖表，以刪除允許服務在虛擬節點上運行的任何*節點選擇器*或*折服*值。 這些值通常會定義在圖表的 `values.yaml` 檔案中。

如果希望通過開發人員空間構建或調試的服務在 VM 節點上運行，您仍可以使用啟用虛擬節點功能的 AKS 群集。 在 VM 節點上運行具有開發人員空間的服務是預設配置。

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>啟動開發空間時出錯"找不到現成的收銀台"

如果 Helm 用戶端無法再與叢集中執行的 Tiller Pod 通訊，就會發生此錯誤。

要解決此問題，請重新開機群集中的代理節點。

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>錯誤\<"釋放 azds-\>-\<識別碼\>-\<空間名稱\>服務名稱失敗：\<服務'\>服務名稱'已存在"或"服務\<名稱\>的拉取訪問被拒絕，存儲庫不存在或可能需要'docker 登錄'"

如果將運行的直接 Helm`helm install`命令（如 、`helm upgrade`或`helm delete`） 與同一開發空間中的開發人員空間命令（`azds up`如`azds down`和 ）混合使用，則可能發生這些錯誤。 它們之所以發生，是因為開發人員空間有自己的 Tiller 實例，該實例與在同一開發空間中運行自己的 Tiller 實例衝突。

對同一 AKS 群集同時使用 Helm 命令和開發人員空間命令可以很好，但每個啟用開發人員空間的命名空間都應使用其中一個或另一個。

例如，假設您使用 Helm 命令在父開發空間中運行整個應用程式。 可以從該父級創建子開發空間，使用開發人員空間在子開發空間內運行單個服務，並一起測試服務。 準備好簽入更改後，請使用 Helm 命令將更新的代碼部署到父開發空間。 不要使用 在`azds up`父開發空間中運行更新的服務，因為它將與最初使用 Helm 運行的服務衝突。

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>不用於構建容器的現有 Docker 檔

可以將 Azure 開發人員空間配置為指向專案中的特定_Dockerfile。_ 如果發生 Azure Dev Spaces 未使用您預期的 Dockerfile__ 來建置容器的情形，您可能需要明確告訴 Azure Dev Spaces 要使用哪個 Dockerfile。 

要解決此問題，請打開 Azure 開發人員空間在專案中生成的_azds.yaml_檔。 更新*配置：開發：生成：dockerfile*指向要使用的 Dockerfile。 例如：

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>嘗試從專用註冊表使用 Docker 映射時出錯"未授權：需要身份驗證"

您使用的是來自需要身份驗證的專用註冊表的 Docker 映射。

要解決此問題，您可以允許開發人員空間使用[imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)進行身份驗證並從該私有註冊表中提取圖像。 要使用圖像PullSecrets，請在使用圖像的命名空間中[創建 Kubernets 機密](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)。 然後提供秘密作為圖像拉秘密在`azds.yaml`。

下面是 在 中`azds.yaml`指定圖像 PullSecrets 的示例。

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
> 在 中`azds.yaml`設置圖像拉圖秘密將覆蓋 在`values.yaml`中指定的圖像拉圖秘密。

### <a name="error-service-cannot-be-started"></a>錯誤"無法啟動服務"。

當您的服務程式碼無法啟動時，您可能會看到此錯誤訊息。 通常是使用者程式碼所造成的。 要獲取更多診斷資訊，請在啟動服務時啟用更詳細的日誌記錄。

從命令列中，使用`--verbose`啟用更詳細的日誌記錄。 您還可以使用 指定輸出格式`--output`。 例如：

```cmd
azds up --verbose --output json
```

在 Visual Studio 中：

1. 開啟 [工具] > [選項]****，然後在 [專案和解決方案]**** 底下選擇 [建置並執行]****。
2. 將 **MSBuild 專案建置輸出詳細資訊**的設定變更為 [詳細]**** 或 [診斷]****。

    ![工具選項對話方塊的螢幕擷取畫面](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>在重新建立控制器之後重新執行服務

在將與此叢集關聯的 Azure Dev Spaces 控制器移除並再予以重新建立之後，嘗試重新執行服務時，您會收到「服務無法啟動」** 的錯誤。 在此情況下，詳細資訊輸出會包含下列文字：

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

出現此錯誤的原因是刪除開發人員空間控制器不會刪除該控制器以前安裝的服務。 重新建立控制器後再嘗試使用新控制器來執行服務會失敗，因為舊服務仍在原位。

若要解決此問題，請使用 `kubectl delete` 命令來手動從叢集中移除舊服務，然後重新執行 Dev Spaces 以安裝新服務。

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>錯誤"無法啟動服務"。 使用多級 Dockerfile 時

在使用多階段 Dockerfile 時，您會收到「服務無法啟動」** 的錯誤。 在此情況下，詳細資訊輸出會包含下列文字：

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

出現此錯誤的原因是 Azure 開發空間當前不支援多階段生成。 若要避免多階段組建，請重寫 Dockerfile。

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>連接開發機器時，網路流量不會轉發到 AKS 群集

當使用[Azure 開發人員空間將 AKS 群集連接到開發電腦時](how-to/connect.md)，可能會遇到開發電腦和 AKS 群集之間未轉發網路流量的問題。

將開發電腦連接到 AKS 群集時，Azure 開發人員空間通過修改開發電腦`hosts`的檔，轉發 AKS 群集和開發電腦之間的網路流量。 Azure 開發人員空間在 中`hosts`創建一個條目，該條目將替換為主機名的 Kubernetes 服務的位址。 此條目與埠轉發一起使用，用於開發電腦和 AKS 群集之間的直接網路流量。 如果開發電腦上的服務與要替換的 Kubernetes 服務的埠衝突，則 Azure 開發人員空間無法轉發 Kubernetes 服務的網路流量。 例如 *，Windows BranchCache*服務通常綁定到*0.0.0.0：80，* 這衝突將導致所有本地 IP 上的埠 80 衝突。

要解決此問題，您需要停止與您嘗試替換的 Kubernetes 服務埠衝突的任何服務或進程。 您可以使用*netstat*等工具來檢查開發電腦上的哪些服務或進程發生衝突。

例如，要停止和禁用*Windows 分支緩存*服務：
* 從`services.msc`命令提示符運行。
* 按右鍵*分支緩存*並選擇*屬性*。
* 按一下 [停止]**。
* 或者，您可以通過將 *"啟動"類型*設置為 *"已禁用*"來禁用它。
* 按一下 [確定]**。

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>使用具有 Azure 開發空間的視覺化工作室和視覺化工作室代碼的常見問題

### <a name="error-required-tools-and-configurations-are-missing"></a>錯誤"缺少所需的工具和配置"

啟動 VS Code 時可能會發生下列錯誤：「[Azure Dev Spaces] 遺失建置 '[專案名稱]' 及對其進行偵錯的必要工具和組態」。
此錯誤訊息表示 azds.exe 不在 PATH 環境變數中，如同 VS Code 中所示。

嘗試從正確設置 PATH 環境變數的命令提示符啟動 VS 代碼。

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>錯誤「建置 'projectname' 並對其進行偵錯所需的工具已過時。」

如果您有適用於 Azure Dev Spaces 的新版 VS Code 延伸模組，但 Azure Dev Spaces CLI 為舊版，就會在 Visual Studio Code 中看到此錯誤。

嘗試下載並安裝最新版本的 Azure 開發空間 CLI：

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>錯誤："找不到類型：coreclr 的調試器擴展失敗"

運行視覺化工作室代碼調試器時，您可能會看到此錯誤。 您可能沒有在開發電腦上安裝 C# 的 VS 代碼擴展。 C# 擴展包括對 .NET Core （CoreCLR） 的調試支援。

要解決此問題，請安裝[C# 的 VS 代碼擴展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>錯誤"不支援配置的調試類型"coreclr"

運行視覺化工作室代碼調試器時，您可能會看到此錯誤。 您可能沒有在開發電腦上安裝 Azure 開發空間的 VS 代碼擴展。

要解決此問題，請[安裝 Azure 開發空間的 VS 代碼擴展](get-started-netcore.md)。

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>錯誤"不正確"cwd"值"/src"。 系統找不到指定的檔案。」 或「launch: program '/src/[專案二進位檔案路徑]' 不存在」

運行視覺化工作室代碼調試器時，您可能會看到此錯誤。 VS Code 延伸模組預設會使用 `src` 作為容器上專案的工作目錄。 如果您已將 `Dockerfile` 更新成指定不同的工作目錄，就可能看到此錯誤。

要解決此問題，`launch.json`請更新專案資料夾子`.vscode`目錄下的檔。 將 `configurations->cwd` 指示詞變更成指向與您專案之 `Dockerfile`.中所定義 `WORKDIR` 相同的目錄。 您可能也需要更新 `configurations->program` 指示詞。

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>錯誤"管道程式'azds'意外退出與代碼126。

運行視覺化工作室代碼調試器時，您可能會看到此錯誤。

要解決此問題，請關閉並重新打開視覺化工作室代碼。 重新開機調試器。

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>將調試附加到 Node.js 應用程式時出錯"內部監視失敗：監視 ENOSPC"

當使用使用調試器嘗試附加到的 Node.js 應用程式運行 pod 的節點超過*fs.inotify.max_user_watches*值時，將發生此錯誤。 在某些情況下[*，fs.inotify.max_user_watches*的預設值可能太小，無法處理將調試器直接附加到 pod](https://github.com/Azure/AKS/issues/772)。

此問題的臨時解決方法是增加群集中每個節點上的*fs.inotify.max_user_watches*的值，並重新啟動該節點，使更改生效。

## <a name="other-common-issues"></a>其他常見問題

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>錯誤"azds"不識別為內部或外部命令、可操作程式或批次檔

如果未`azds.exe`正確安裝或配置，則可能發生此錯誤。

若要修正此問題：

1. 檢查位置 %程式檔%%/微軟 SDK\Azure_Azure 開發空間 CLI。 `azds.exe` 如果有的話，請將該位置新增至 PATH 環境變數中。
2. 如果未`azds.exe`安裝，請運行以下命令：

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>授權錯誤"微軟.Devspace/註冊/操作"

您需要 Azure 訂用帳戶中的「擁有者」** 或「參與者」** 權限才能管理 Azure Dev Spaces。 如果您嘗試管理開發空間，並且沒有*擁有者*或*參與者*對關聯的 Azure 訂閱的存取權限，則可能會看到授權錯誤。 例如：

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

要解決此問題，請使用具有對 Azure 訂閱*的擁有者*或*參與者存取權限的*帳戶，請手動註冊`Microsoft.DevSpaces`命名空間：

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>新窗格未啟動

由於 RBAC 許可權更改為群集中的*群集管理員*角色，Kubernetes 初始化器無法為新 pod 應用 PodSpec。 新窗格可能還有不正確 PodSpec，例如與該窗格關聯的服務帳戶不再存在。 要查看由於初始化問題而處於*掛起*狀態的 pod，請使用以下`kubectl get pods`命令：

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

此問題可能會影響群集*中所有命名空間*中的窗格，包括未啟用 Azure 開發空間的命名空間。

要解決此問題，請[將開發空間 CLI 更新到最新版本，](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools)然後從 Azure 開發人員空間控制器中刪除*azds 初始化器配置*：

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

從 Azure 開發人員空間控制器中刪除*azds 初始化器配置*後，`kubectl delete`使用 來刪除處於*掛起*狀態的任何窗格。 刪除所有掛起的窗格後，重新部署您的窗格。

如果重新部署後新窗格仍停留在 *"掛起*"狀態，請使用`kubectl delete`來刪除處於*掛起*狀態的任何窗格。 刪除所有掛起的窗格後，從群集中刪除控制器並重新安裝它：

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

重新安裝控制器後，重新部署窗格。

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>調用開發人員空間控制器和 API 的 RBAC 許可權不正確

訪問 Azure 開發人員空間控制器的使用者必須有權訪問 AKS 群集上的管理員*庫貝康格*。 例如，此許可權在內置 Azure[庫伯奈斯服務群集管理員角色](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)中可用。 訪問 Azure 開發人員空間控制器的使用者還必須具有控制器*的參與者*或*擁有者*RBAC 角色。 有關更新使用者對 AKS 群集的許可權的更多詳細資訊[，請在此處](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)獲取。

要更新控制器的使用者 RBAC 角色，請執行以下操作：

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 導航到包含控制器的資源組，該控制器通常與您的 AKS 群集相同。
1. 啟用"*顯示隱藏類型*"核取方塊。
1. 按一下控制器。
1. 打開*存取控制 （IAM）* 窗格。
1. 按一下"*角色指派"* 選項卡。
1. 按一下"*添加*"然後*添加角色指派*。
    * 對於*角色*，請選擇 *"參與者*"或"*擁有者*"。
    * 在 [存取權指派對象為]** 中，選取 [Azure AD 使用者、群組或服務主體]**。
    * 對於 *"選擇*"，搜索要授予許可權的使用者。
1. 按一下 [儲存]**。

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>與 Dev Spaces 服務相關聯的公用 URL 進行 DNS 名稱解析失敗

您可以藉由對 `azds prep` 命令指定 `--enable-ingress` 參數，或藉由在 Visual Studio 中選取 `Publicly Accessible` 核取方塊，來為服務設定公用 URL 端點。 當您在 Dev Spaces 中執行服務時，公用 DNS 名稱會自動完成註冊。 如果此 DNS 名稱未完成註冊，您就會在連線至公用 URL 時，於網頁瀏覽器中看到「頁面無法顯示」** 或「網站無法連線」** 的錯誤。

若要修正此問題：

* 檢查與您的開發空間服務關聯的所有 URL 的狀態：

  ```console
  azds list-uris
  ```

* 如果 URL 處於*掛起*狀態，則開發人員空間仍在等待 DNS 註冊完成。 有時需要幾分鐘的時間才能完成註冊。 Dev Spaces 也會為每個服務開啟一個 localhost 通道，您在等候 DNS 登錄完成時可以使用此通道。
* 如果 URL 處於「擱置」** 狀態超過 5 分鐘，可能表示建立公用端點的外部 DNS Pod 或取得公用端點的 nginx 輸入控制器 Pod 有問題。 使用以下命令刪除這些窗格並允許 AKS 自動重新創建它們：
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>錯誤"上行連接錯誤或標頭前斷開連接/重置"

您在嘗試存取服務時，可能會看到這個錯誤。 例如，當您在瀏覽器中移至服務的 URL 時。 此錯誤表示容器埠不可用。 這可能是出於以下原因：

* 容器仍處於建置和部署程序。 如果您執行 `azds up` 或啟動偵錯工具，然後在容器成功部署之前嘗試存取容器，就有可能發生這個問題。
* 埠配置在整個_Dockerfile、Helm_Chart 和打開端口的任何伺服器代碼之間不一致。

若要修正此問題：

1. 如果容器處於建置/部署程序，您可以等待 2-3 秒，然後再次嘗試存取服務。 
1. 檢查埠配置（以下資產）：
    * **[赫爾姆圖](https://docs.helm.sh)：** `service.port`由`deployment.containerPort`和 值 指定.yaml 腳手架，由`azds prep`命令指定。
    * 在應用程式程式碼中開啟的任何連接埠，例如在 Node.js 中：`var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>找不到類型或命名空間名稱"我的庫"

找不到正在使用的庫專案。 使用開發人員空間時，預設情況下生成上下文位於專案/服務等級。  

若要修正此問題：

1. 修改檔`azds.yaml`以將生成上下文設置為解決方案級別。
2. 修改`Dockerfile`和`Dockerfile.develop`檔以引用專案檔案，例如`.csproj`，相對於新的生成上下文正確。
3. 在同一`.dockerignore`目錄中添加 與檔相同的`.sln`目錄中的 。
4. 根據需要`.dockerignore`更新 其他條目。

你可以[在這裡](https://github.com/sgreenmsft/buildcontextsample)找到一個例子。

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>水準窗格自動縮放在開發空間中不起作用

在開發空間中運行服務時，該服務的窗格將[注入用於檢測的其他容器](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster)，並且 Pod 中的所有容器都需要為水準 Pod 自動縮放設置資源限制和請求。

要解決此問題，請應用資源請求並限制注入的開發人員空間容器。 通過將`azds.io/proxy-resources`注釋添加到 pod 規範，可以針對注入的容器（開發空間代理）應用資源請求和限制。該值應設置為表示代理容器規範的資源部分的 JSON 物件。

下面是要應用於 pod 規範的代理資源注釋的示例。
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>在現有命名空間上啟用 Azure 開發人員空間，並運行窗格

您可能有一個現有的 AKS 群集和命名空間，其中具有正在運行的窗格，您希望在其中啟用 Azure 開發空間。

要在 AKS 群集的現有命名空間上啟用 Azure 開發空間`use-dev-spaces`，請`kubectl`運行並用於重新開機該命名空間中的所有窗格。

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

重新開機窗格後，可以開始使用現有命名空間和 Azure 開發空間。

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>在 AKS 群集上啟用 Azure 開發空間，叢集節點的傳出流量受限

要在限制叢集節點出口流量的 AKS 群集上啟用 Azure 開發空間，必須允許以下 FQDN：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS：443 | 拉 linux 高山和其他 Azure 開發人員空間映射 |
| gcr.io | HTTP：443 | 拉舵/牽引器圖像|
| storage.googleapis.com | HTTP：443 | 拉舵/牽引器圖像|
| 阿茲茲-.<guid><location>.azds.io | HTTPS：443 | 與控制器的 Azure 開發空間後端服務進行通信。 確切的 FQDN 可在 %USERPROFILE%\.azds_s_json 中的"資料平面Fqdn"中找到。|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>錯誤\<"在訂閱\>\<Id\>中找不到群集"

如果 kubeconfig 檔的目標與嘗試與 Azure Dev Spaces 用戶端工具一起使用不同的群集或訂閱，則可能會看到此錯誤。 Azure 開發人員空間用戶端工具複製*kubectl*的行為，該函數使用[一個或多個庫貝康菲克檔](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)來選擇群集並與之通信。

若要修正此問題：

* 用於`az aks use-dev-spaces -g <resource group name> -n <cluster name>`更新當前上下文。 如果尚未啟用，此命令還會在 AKS 群集上啟用 Azure 開發空間。 或者，您可以使用`kubectl config use-context <cluster name>`更新當前上下文。
* 用於`az account show`顯示要定位的當前 Azure 訂閱並驗證這是否正確。 您可以使用 更改要定位的訂閱`az account set`。

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>旋轉 AKS 證書後使用開發空間的錯誤

[旋轉 AKS 群集中的證書](../aks/certificate-rotation.md)後，某些操作（如`azds space list`和`azds up`將失敗）。 在旋轉群集上的證書後，還需要刷新 Azure 開發人員空間控制器上的證書。

要解決此問題，請確保*kubeconfig*具有更新的證書，然後`az aks get-credentials`運行該`azds controller refresh-credentials`命令。 例如：

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
