---
title: 在已啟用 Arc 的 Kubernetes 叢集上使用 GitOps 部署設定 (預覽)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: '使用 Gitops) 將來設定啟用 Azure Arc 的 Kubernetes 叢集 (預覽) '
keywords: Gitops) 將、Kubernetes、K8s、Azure、Arc、Azure Kubernetes Service、AKS、容器
ms.openlocfilehash: a068ed90ea53b3b25a1f41cebd9a5b8e607afa54
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737179"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>在已啟用 Arc 的 Kubernetes 叢集上使用 GitOps 部署設定 (預覽)

Gitops) 將與 Kubernetes 相關，是在 Git 存放庫中宣告 (Kubernetes 設定的預期狀態，然後使用操作員進行這些設定至叢集的輪詢和提取型部署的做法，這是 ) 。 本檔涵蓋 Azure Arc 啟用的 Kubernetes 叢集上的這類工作流程設定。

叢集與 Git 存放庫之間的連線會在 Azure Resource Manager 中建立為 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` 擴充功能資源。 `sourceControlConfiguration` 資源屬性代表 Kubernetes 資源應該從 Git 流向您叢集的位置和方式。 `sourceControlConfiguration`資料會以待用加密儲存在 Azure Cosmos DB 資料庫中，以確保資料機密性。

在您的叢集中 `config-agent` 執行的會負責在 `sourceControlConfiguration` 啟用 Azure Arc 的 Kubernetes 資源上監看新的或更新的延伸模組資源、部署 Flux 操作員以監看每個的 Git 存放庫 `sourceControlConfiguration` ，以及套用任何對任何更新的更新 `sourceControlConfiguration` 。 您可以 `sourceControlConfiguration` 在相同的 Azure Arc 啟用的 Kubernetes 叢集上建立多個資源，以達成多租使用者。 您可以使用不同的範圍來建立每個， `sourceControlConfiguration` `namespace` 以將部署限制在各自的命名空間內。

Git 存放庫可以包含 YAML 格式的資訊清單，以描述任何有效的 Kubernetes 資源，包括命名空間、ConfigMaps、部署、Daemonset 等等。 它也可能包含用來部署應用程式的 Helm 圖表。 常見的一組案例包括定義組織的基準設定，其中可能包括常見的 Azure 角色和系結、監視或記錄代理程式，或全叢集服務。

您可以使用相同的模式來管理較大的叢集集合，這些叢集可能會跨不同的環境進行部署。 例如，您可能有一個存放庫會定義貴組織的基準設定，並一次將該設定套用至數十個 Kubernetes 叢集。 [Azure 原則可](use-azure-policy.md) 在 `sourceControlConfiguration`)  (訂用帳戶或資源群組的範圍下，以一組特定 Azure Arc 參數自動建立。

此快速入門手冊將逐步引導您在叢集管理範圍內套用一組設定。

## <a name="before-you-begin"></a>開始之前

本文假設您具有現有已啟用 Azure Arc 的 Kubernetes 已連線叢集。 如果您需要已連線的叢集，請參閱[連線叢集快速入門](./connect-cluster.md)。

## <a name="create-a-configuration"></a>建立設定

本檔中使用的 [範例存放庫](https://github.com/Azure/arc-k8s-demo) 是以叢集操作員的角色為結構，而該角色需要布建一些命名空間、部署一般工作負載，並提供一些小組專屬的設定。 使用此存放庫，會在您的叢集上建立下列資源：

**命名空間：** `cluster-config`、`team-a`、`team-b`
**部署：** `cluster-config/azure-vote`
**ConfigMap：** `team-a/endpoints`

`config-agent`會每隔30秒輪詢 Azure 以進行新增或更新 `sourceControlConfiguration` ，這是 `config-agent` 挑選新的或更新的設定所花費的時間上限。
如果您要將私人存放庫與建立關聯 `sourceControlConfiguration` ，請確定您也已完成 [從私用 Git 存放庫](#apply-configuration-from-a-private-git-repository)套用設定中的步驟。

### <a name="using-azure-cli"></a>使用 Azure CLI

使用的 Azure CLI 延伸模組，將 `k8sconfiguration` 已連接的叢集連結至 [範例 Git 存放庫](https://github.com/Azure/arc-k8s-demo)。 我們會將此設定命名為 `cluster-config`、指示代理程式在 `cluster-config` 命名空間中部署操作員，並為該操作員授與 `cluster-admin` 權限。

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**輸出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Pending",
    "lastConfigApplied": "0001-01-01T00:00:00",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": null,
  "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
  "resourceGroup": "MyRG",
  "sshKnownHostsContents": "",
  "systemData": {
    "createdAt": "2020-11-24T21:22:01.542801+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
  },
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
  ```

#### <a name="use-a-public-git-repo"></a>使用公用 Git 存放庫

| 參數 | [格式] |
| ------------- | ------------- |
| --存放庫-url | HTTP [s]：//server/repo [. git] 或 git：//server/repo [. git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>使用私人 Git 存放庫搭配 SSH 和 Flux 建立的金鑰

| 參數 | [格式] | 備註
| ------------- | ------------- | ------------- |
| --存放庫-url | ssh://user@server/repo[git] 或 user@server:repo [git] | `git@` 可能取代為 `user@`

> [!NOTE]
> Flux 所產生的公開金鑰必須加入至您 Git 服務提供者中的使用者帳戶。 如果將金鑰新增至存放庫，而不是 > 使用者帳戶，請使用 `git@` 取代 `user@` URL 中的。 [查看更多詳細資料](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>使用私用 Git 存放庫搭配 SSH 和使用者提供的金鑰

| 參數 | [格式] | 備註 |
| ------------- | ------------- | ------------- |
| --存放庫-url  | ssh://user@server/repo[git] 或 user@server:repo [git] | `git@` 可能取代為 `user@` |
| --ssh-私用金鑰 | [PEM 格式](https://aka.ms/PEMformat)的 base64 編碼金鑰 | 直接提供金鑰 |
| --ssh-私用金鑰-檔案 | 本機檔案的完整路徑 | 提供包含 PEM 格式金鑰之本機檔案的完整路徑

> [!NOTE]
> 直接或在檔案中提供您自己的私密金鑰。 金鑰必須是 [PEM 格式](https://aka.ms/PEMformat) ，且結尾必須是分行符號 ( \n) 。  您必須將相關聯的公開金鑰新增至 Git 服務提供者中的使用者帳戶。 如果金鑰已新增至存放庫而非使用者帳戶，請使用 `git@` 取代 `user@` 。 [查看更多詳細資料](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>使用私人 Git 主機搭配 SSH 和使用者提供的已知主機

| 參數 | [格式] | 備註 |
| ------------- | ------------- | ------------- |
| --存放庫-url  | ssh://user@server/repo[git] 或 user@server:repo [git] | `git@` 可能取代為 `user@` |
| --ssh-已知主機 | base64 編碼 | 直接提供的已知主機內容 |
| --ssh-已知-hosts-file | 本機檔案的完整路徑 | 本機檔案中提供的已知主機內容

> [!NOTE]
> Flux 運算子會在其已知的主機檔案中維護常見的 Git 主機清單，以便在建立 SSH 連線之前驗證 Git 存放庫。 如果您使用的是不尋常的 Git 存放庫或您自己的 Git 主機，您可能需要提供主機金鑰，以確保 Flux 可以識別您的存放庫。 您可以直接或在檔案中提供已知的主機內容。 [查看已知的主機內容格式規格](https://aka.ms/KnownHostsFormat)。
> 您可以搭配上述的其中一個 SSH 金鑰案例使用此功能。

#### <a name="use-a-private-git-repo-with-https"></a>使用具有 HTTPS 的私人 Git 存放庫

| 參數 | [格式] | 備註 |
| ------------- | ------------- | ------------- |
| --存放庫-url | https://server/repo[git] | 使用基本驗證的 HTTPS |
| --HTTPs-使用者 | raw 或 base64 編碼 | HTTPS 使用者名稱 |
| --HTTPs-金鑰 | raw 或 base64 編碼 | HTTPS 個人存取權杖或密碼

> [!NOTE]
> 只有 Helm 運算子圖表版本 >= 1.2.0 才支援 HTTPS Helm release 私用驗證。  預設會使用版本1.2.0。
> 目前不支援 Azure Kubernetes Services 受控叢集的 HTTPS Helm release 私用驗證。
> 如果您需要透過 proxy 存取 Git 儲存機制的 Flux，則必須使用 proxy 設定來更新 Azure Arc 代理程式。 [詳細資訊](./connect-cluster.md#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>其他參數

若要自訂設定，以下是您可以使用的更多參數：

`--enable-helm-operator`：「選擇性」參數，可啟用對 Helm 圖表部署的支援。

`--helm-operator-params`：適用於 Helm 操作員 (如果已啟用) 的「選擇性」圖表值。  例如，'--set helm.versions=v3'。

`--helm-operator-chart-version`：適用於 Helm 操作員 (如果已啟用) 的「選擇性」圖表版本。 預設值： ' 1.2.0 '。

`--operator-namespace`：操作員命名空間的「選擇性」名稱。 預設值： ' default '。 最多23個字元。

`--operator-params`：適用於操作員的「選擇性」參數。 必須在單引號中提供。 例如， ```--operator-params='--git-readonly --git-path=releases --sync-garbage-collection' ```

--operator-params 中支援的選項

| 選項 | 描述 |
| ------------- | ------------- |
| --git-branch  | 用於 Kubernetes 資訊清單的 Git 存放庫分支。 預設值是 'master'。 |
| --git-path  | 適用於 Flux 之 Git 存放庫內的相對路徑，可尋找 Kubernetes 資訊清單。 |
| --git-readonly | Git 存放庫將被視為唯讀；Flux 將不會嘗試寫入至其中。 |
| --manifest-generation  | 啟用時，Flux 將會尋找 .flux.yaml，並執行 Kustomize 或其他資訊清單產生器。 |
| --git-poll-interval  | 輪詢 Git 存放庫以取得新認可的期間。 預設值為 '5m' (5 分鐘)。 |
| --sync-garbage-collection  | 啟用時，Flux 將刪除其已建立但不再出現於 Git 中的資源。 |
| --git-label  | 用來追蹤同步處理進度的標籤，可用來標記 Git 分支。  預設值為 'flux-sync'。 |
| --git-user  | Git 認可的使用者名稱。 |
| --git-email  | 用於 Git 認可的電子郵件。 |

* 如果未設定 '--git-user' 或 '--git-email' (這表示您不希望 Flux 寫入至存放庫)，將會自動設定 --git-readonly (如果您尚未設定)。

如需詳細資訊，請參閱 [Flux 檔](https://aka.ms/FluxcdReadme)。

> [!TIP]
> 您可以在 [已啟用 Azure Arc] Kubernetes 資源的 [ **gitops) 將** ] 索引標籤中，于 Azure 入口網站建立 sourceControlConfiguration。

## <a name="validate-the-sourcecontrolconfiguration"></a>驗證 sourceControlConfiguration

使用 Azure CLI 來驗證是否已成功建立 `sourceControlConfiguration`。

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

請注意，`sourceControlConfiguration` 資源會利用合規性狀態、訊息和偵錯資訊進行更新。

**輸出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

建立 `sourceControlConfiguration` 時，會在幕後發生下列動作：

1. Azure Arc `config-agent` 會監視 Azure Resource Manager 的新設定或更新設定 (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`)
1. `config-agent` 會注意到新的 `Pending` 設定
1. `config-agent` 會讀取設定屬性，並準備部署 `flux` 的受控執行個體
    * `config-agent` 會建立目的地命名空間
    * `config-agent` 會準備一個具有適當權限 (`cluster` 或 `namespace` 範圍) 的 Kubernetes Service 帳戶
    * `config-agent` 會部署 `flux` 的執行個體
    * `flux` 如果搭配使用 SSH 與 Flux 產生的金鑰，則會產生 SSH 金鑰並記錄公開金鑰 () 
1. `config-agent` 將狀態報表回 `sourceControlConfiguration` Azure 中的資源

當佈建程序發生時，`sourceControlConfiguration` 將歷經數個狀態變更。 使用上述 `az k8sconfiguration show ...` 命令來監視進度：

1. `complianceStatus` -> `Pending`：代表初始和進行中狀態
1. `complianceStatus` -> `Installed`：`config-agent` 能夠順利設定叢集並部署 `flux`，而不會發生錯誤
1. `complianceStatus` -> `Failed`：`config-agent` 在部署 `flux` 時發生錯誤，`complianceStatus.message` 回應本文中應該會提供詳細資料

## <a name="apply-configuration-from-a-private-git-repository"></a>從私用 Git 存放庫套用設定

如果您使用的是私用 Git 存放庫，則需要在您的存放庫中設定 SSH 公開金鑰。 您可以在特定的 Git 存放庫或可存取存放庫的 Git 使用者上設定公開金鑰。 SSH 公開金鑰會是您提供的金鑰，或 Flux 產生的公開金鑰。

**取得您自己的公開金鑰**

如果您產生自己的 SSH 金鑰，則您已經擁有私用金鑰和公開金鑰。

**使用 Azure CLI 取得公開金鑰 (在 Flux 產生金鑰時很有用)**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**從 Azure 入口網站取得公開金鑰 (適用于 Flux 產生金鑰的情況)**

1. 在 Azure 入口網站中，瀏覽至連線的叢集資源。
2. 在資源頁面中，選取 [Gitops) 將]，並查看此叢集的設定清單。
3. 選取使用私人 Git 存放庫的設定。
4. 在開啟的內容視窗中，複製視窗底部的 **存放庫公開金鑰**。

如果您使用的是 GitHub，請使用下列2個選項的其中一個：

**選項1：將公開金鑰新增至您的使用者帳戶， (套用至帳戶中的所有存放庫)**

1. 開啟 GitHub，按一下頁面右上角的設定檔圖示。
2. 按一下 [**設定**]
3. 按一下 **SSH 和 GPG 金鑰**
4. 按一下 [**新增 SSH 金鑰**]
5. 提供標題
6. 貼上公開金鑰 (不包括周圍的所有引號)
7. 按一下 [**新增 SSH 金鑰**]

**選項2：將公開金鑰作為部署金鑰新增至 Git 存放庫 (僅適用于此存放庫)**

1. 開啟 GitHub、流覽至您的存放庫、至 **設定**，然後 **部署金鑰**
2. 按一下 [**新增部署金鑰**]
3. 提供標題
4. 檢查 **允許寫入權限**
5. 貼上公開金鑰 (不包括周圍的所有引號)
6. 按一下 [**新增金鑰**]

**如果您使用 Azure DevOps 存放庫，請將金鑰新增至您的 SSH 金鑰**

1. 在右上角的 [使用者設定] 底下 (設定檔影像旁邊)，按一下 [SSH 公開金鑰]
1. 選取 [+ 新增金鑰]。
1. 提供名稱
1. 貼上周圍不含任何引號的公開金鑰
1. 按一下 [新增]

## <a name="validate-the-kubernetes-configuration"></a>驗證 Kubernetes 設定

在 `config-agent` 安裝實例之後 `flux` ，保留在 Git 存放庫中的資源應該會開始流向叢集。 檢查以查看是否已建立命名空間、部署和資源：

```console
kubectl get ns --show-labels
```

**輸出：**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

我們可以看到已建立 `team-a`、`team-b`、`itops` 和 `cluster-config` 命名空間。

`flux` 操作員已部署至 `cluster-config` 命名空間，如我們的 `sourceControlConfig` 所指示：

```console
kubectl -n cluster-config get deploy  -o wide
```

**輸出：**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>進一步探索

您可以瀏覽其他部署為設定存放庫一部分的資源：

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>刪除設定

`sourceControlConfiguration`使用 Azure CLI 或 Azure 入口網站刪除。  在您起始 delete 命令之後， `sourceControlConfiguration` 系統會立即刪除 Azure 中的資源，並在10分鐘內從叢集完全刪除相關聯的物件。  如果在 `sourceControlConfiguration` 刪除時處於失敗狀態，則相關物件的完整刪除可能需要一小時的時間。

> [!NOTE]
> 建立具有命名空間範圍的 sourceControlConfiguration 之後，在命名空間上具有角色系結的使用者就可以在 `edit` 此命名空間上部署工作負載。 當 `sourceControlConfiguration` 刪除具有命名空間範圍的此項時，命名空間會保持不變，且不會刪除，以避免破壞這些其他工作負載。  如有需要，您可以使用 kubectl 手動刪除此命名空間。
> 當刪除時，不會刪除從追蹤的 Git 存放庫部署所產生之叢集的任何變更 `sourceControlConfiguration` 。

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**輸出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>後續步驟

- [使用 Helm 搭配原始檔控制設定](./use-gitops-with-helm.md)
- [使用 Azure 原則來控管叢集設定](./use-azure-policy.md)