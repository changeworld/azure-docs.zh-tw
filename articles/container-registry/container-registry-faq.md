---
title: 常見問題集
description: Azure Container Registry 服務相關常見問題的解答
author: sajayantony
ms.topic: article
ms.date: 03/18/2020
ms.author: sajaya
ms.openlocfilehash: 39b543c5f886b22d488198873b75cf76555692fa
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731639"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>關於 Azure Container Registry 的常見問題

本文說明 Azure Container Registry 的常見問題和已知問題。

## <a name="resource-management"></a>資源管理

- [我可以使用 Resource Manager 範本建立 Azure container registry 嗎？](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [ACR 中的映射是否有安全性弱點掃描？](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [如何? 使用 Azure Container Registry 設定 Kubernetes 嗎？](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [如何? 取得容器登錄的系統管理員認證？](#how-do-i-get-admin-credentials-for-a-container-registry)
- [如何? 取得 Resource Manager 範本中的管理員認證？](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [雖然複寫會使用 Azure CLI 或 Azure PowerShell 刪除，但是刪除複寫失敗，並出現「禁止」狀態](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [已成功更新防火牆規則，但不會生效](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>我可以使用 Resource Manager 範本建立 Azure Container Registry 嗎？

可以。 以下是您可以用來建立登錄的[範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry)。

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>ACR 中的映射是否有安全性弱點掃描？

可以。 請參閱[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)、 [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/)和[淺綠色](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)的檔。

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>如何? 使用 Azure Container Registry 設定 Kubernetes 嗎？

請參閱[Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr)的檔和[Azure Kubernetes Service](../aks/cluster-container-registry-integration.md)的步驟。

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>如何? 取得容器登錄的系統管理員認證？

> [!IMPORTANT]
> 管理使用者帳戶是專為單一使用者存取登錄而設計的，主要是為了測試目的。 不建議將管理帳戶認證與其他使用者共用。 針對遠端控制案例的使用者和服務主體，建議使用個人身分識別。 請參閱[驗證總覽](container-registry-authentication.md)。

取得系統管理員認證之前，請確定已啟用登錄的系統管理員使用者。

若要使用 Azure CLI 取得認證：

```azurecli
az acr credential show -n myRegistry
```

使用 Azure Powershell：

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>如何? 取得 Resource Manager 範本中的管理員認證？

> [!IMPORTANT]
> 管理使用者帳戶是專為單一使用者存取登錄而設計的，主要是為了測試目的。 不建議將管理帳戶認證與其他使用者共用。 針對遠端控制案例的使用者和服務主體，建議使用個人身分識別。 請參閱[驗證總覽](container-registry-authentication.md)。

取得系統管理員認證之前，請確定已啟用登錄的系統管理員使用者。

若要取得第一個密碼：

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

若要取得第二個密碼：

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>雖然複寫會使用 Azure CLI 或 Azure PowerShell 刪除，但是刪除複寫失敗，並出現「禁止」狀態

當使用者具有登錄的許可權，但沒有訂用帳戶的讀取者層級許可權時，就會出現此錯誤。 若要解決此問題，請將訂用帳戶的讀者許可權指派給使用者：


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>已成功更新防火牆規則，但不會生效

傳播防火牆規則變更需要一些時間。 變更防火牆設定後，請稍候幾分鐘，再確認此變更。


## <a name="registry-operations"></a>登錄作業

- [如何? 存取 Docker Registry HTTP API V2？](#how-do-i-access-docker-registry-http-api-v2)
- [如何? 刪除存放庫中的任何標記未參考的所有資訊清單嗎？](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [為何在刪除映射之後，登錄配額使用量不會降低？](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [如何? 驗證儲存體配額變更嗎？](#how-do-i-validate-storage-quota-changes)
- [在容器中執行 CLI 時，如何? 向我的登錄進行驗證嗎？](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [如何啟用 TLS 1.2？](#how-to-enable-tls-12)
- [Azure Container Registry 支援內容信任嗎？](#does-azure-container-registry-support-content-trust)
- [如何? 授與提取或推送映射的存取權，但沒有管理登錄資源的許可權？](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [如何? 啟用登錄的自動映射隔離功能嗎？](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [如何? 啟用匿名提取存取？](#how-do-i-enable-anonymous-pull-access)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>如何? 存取 Docker Registry HTTP API V2？

ACR 支援 Docker Registry HTTP API V2。 您可以在`https://<your registry login server>/v2/`存取 api。 範例：`https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>如何? 刪除存放庫中的任何標記未參考的所有資訊清單嗎？

如果您在 bash 上：

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

針對 Powershell：

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

注意：您可以在`-y`刪除命令中新增以略過確認。

如需詳細資訊，請參閱[在 Azure Container Registry 中刪除容器映射](container-registry-delete.md)。

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>為何在刪除映射之後，登錄配額使用量不會降低？

如果基礎層仍被其他容器映射參考，就可能發生這種情況。 如果您刪除沒有參考的映射，登錄使用會在幾分鐘內更新。

### <a name="how-do-i-validate-storage-quota-changes"></a>如何? 驗證儲存體配額變更嗎？

使用下列 docker 檔案，建立具有1GB 層的映射。 這可確保映射具有未由登錄中任何其他映射共用的圖層。

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

使用 docker CLI 建立映射並將其推送至您的登錄。

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

您應該會看到 Azure 入口網站中的儲存體使用量已增加，或者您也可以使用 CLI 來查詢使用方式。

```azurecli
az acr show-usage -n myregistry
```

使用 Azure CLI 或入口網站刪除映射，並在幾分鐘內檢查更新過的使用方式。

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>在容器中執行 CLI 時，如何? 向我的登錄進行驗證嗎？

您需要裝載 Docker 通訊端來執行 Azure CLI 容器：

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

在容器中，安裝`docker`：

```bash
apk --update add docker
```

然後使用您的登錄進行驗證：

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>如何啟用 TLS 1.2？

使用任何最近的 docker 用戶端（18.03.0 版和更新版本）來啟用 TLS 1.2。 

> [!IMPORTANT]
> 從 2020 年 1 月 13 日開始，Azure Container Registry 將要求所有安全連線來自伺服器和應用程式，才能使用 TLS 1.2。 TLS 1.0 和 1.1 的支援將會淘汰。

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry 支援內容信任嗎？

是，您可以在 Azure Container Registry 中使用受信任的映射，因為[Docker Notary](https://docs.docker.com/notary/getting_started/)已整合並可啟用。 如需詳細資訊，請參閱[Azure Container Registry 中的內容信任](container-registry-content-trust.md)。


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>指紋的檔案位於何處？

在`~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`下列底下：

* 所有角色的公開金鑰和憑證（委派角色除外）都會儲存在中`root.json`。
* 委派角色的公開金鑰和憑證會儲存在其父系角色（ `targets.json`例如`targets/releases`角色）的 JSON 檔案中。

建議在 Docker 和 Notary 用戶端完成整體 TUF 驗證之後，驗證這些公開金鑰和憑證。

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>如何? 授與提取或推送映射的存取權，但沒有管理登錄資源的許可權？

ACR 支援提供不同許可權層級的[自訂角色](container-registry-roles.md)。 具體來說`AcrPull` ， `AcrPush`和角色可讓使用者提取和/或推送映射，而不需要在 Azure 中管理登錄資源。

* Azure 入口網站：您的登錄 > 存取控制（IAM）-> 新增（針對`AcrPull`角色`AcrPush`選取或）。
* Azure CLI：執行下列命令來尋找登錄的資源識別碼：

  ```azurecli
  az acr show -n myRegistry
  ```
  
  接著，您可以將`AcrPull`或`AcrPush`角色指派給使用者（下列範例會使用`AcrPull`）：

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  或者，將角色指派給其應用程式識別碼所識別的服務主體：

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

然後，該受託人就能夠驗證和存取登錄中的映射。

* 若要向登錄進行驗證：
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 列出存放庫：

  ```azurecli
  az acr repository list -n myRegistry
  ```

* 若要提取映射：

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

只要使用`AcrPull`或`AcrPush`角色，受託人就不會有在 Azure 中管理登錄資源的許可權。 例如， `az acr list`或`az acr show -n myRegistry`不會顯示登錄。

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>如何? 啟用登錄的自動映射隔離功能嗎？

映射隔離目前是 ACR 的預覽功能。 您可以啟用登錄的隔離模式，讓一般使用者只能看見已成功通過安全性掃描的映射。 如需詳細資訊，請參閱[ACR GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)存放庫。

### <a name="how-do-i-enable-anonymous-pull-access"></a>如何? 啟用匿名提取存取？

設定匿名（公用）提取存取的 Azure container registry 目前為預覽功能。 若要啟用公用存取，請在上https://aka.ms/acr/support/create-ticket開啟支援票證。 如需詳細資訊，請參閱[Azure 意見反應論壇](https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/32517127-enable-anonymous-access-to-registries)。


## <a name="diagnostics-and-health-checks"></a>診斷和健康狀態檢查

- [檢查健全狀況`az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull 失敗，並出現錯誤： net/HTTP：要求在等候連接時取消（用戶端等待標頭時超過超時時間）](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push 成功，但 docker pull 失敗，發生錯誤：未經授權：需要驗證](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`成功，但 docker 命令失敗，發生錯誤：未經授權：需要驗證](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [啟用並取得 docker daemon 的偵錯工具記錄檔](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [新的使用者權限在更新之後可能不會立即生效](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [未在直接 REST API 呼叫上以正確的格式提供驗證資訊](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [為什麼 Azure 入口網站不會列出我的所有存放庫或標籤？](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [為什麼 Azure 入口網站無法提取存放庫或標籤？](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [為什麼我的提取或推送要求失敗，但不允許作業？](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [如何? 在 Windows 上收集 HTTP 追蹤？](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>檢查健全狀況`az acr check-health`

若要針對常見的環境和登錄問題進行疑難排解，請參閱[檢查 Azure container registry 的健全狀況](container-registry-check-health.md)。

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull 失敗，並出現錯誤： net/HTTP：要求在等候連接時取消（用戶端等待標頭時超過超時時間）

 - 如果此錯誤是暫時性的問題，則重試將會成功。
 - 如果`docker pull`持續失敗，則 Docker daemon 可能發生問題。 藉由重新開機 Docker daemon，通常可以減輕問題。 
 - 如果您在重新開機 Docker daemon 之後繼續看到此問題，則問題可能是電腦的一些網路連線問題。 若要檢查電腦上的一般網路是否狀況良好，請執行下列命令來測試端點連線能力。 包含此`az acr`連線檢查命令的最低版本為2.2.9。 如果您使用的是較舊的版本，請升級您的 Azure CLI。
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - 在所有 Docker 用戶端操作上，您應該一律具有重試機制。

### <a name="docker-pull-is-slow"></a>Docker pull 的速度很慢
使用[此](http://www.azurespeed.com/Azure/Download)工具來測試電腦的網路下載速度。 如果電腦網路速度很慢，請考慮在與您的登錄相同的區域中使用 Azure VM。 這通常可為您提供更快的網路速度。

### <a name="docker-push-is-slow"></a>Docker push 的速度很慢
使用[此](http://www.azurespeed.com/Azure/Upload)工具來測試電腦的網路上傳速度。 如果電腦網路速度很慢，請考慮在與您的登錄相同的區域中使用 Azure VM。 這通常可為您提供更快的網路速度。

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker push 成功，但 docker pull 失敗，發生錯誤：未經授權：需要驗證

這項錯誤可能會隨著 Docker daemon 的 Red Hat 版本而發生， `--signature-verification`其中預設為啟用。 您可以執行下列命令來檢查 Red Hat Enterprise Linux （RHEL）或 Fedora 的 Docker daemon 選項：

```bash
grep OPTIONS /etc/sysconfig/docker
```

例如，Fedora 28 伺服器具有下列 docker daemon 選項：

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

`--signature-verification=false`遺失時， `docker pull`會失敗並出現類似以下的錯誤：

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

若要解決此錯誤：
1. 將選項`--signature-verification=false`新增至 Docker daemon 設定檔`/etc/sysconfig/docker`。 例如：
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. 執行下列命令來重新開機 Docker daemon 服務：
   
   ```bash
   sudo systemctl restart docker.service
   ```

您可以`--signature-verification`藉由執行來找到`man dockerd`的詳細資料。

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login 成功，但 docker 失敗，發生錯誤：未經授權：需要驗證

請確定您使用的是所有小寫伺服器 URL， `docker push myregistry.azurecr.io/myimage:latest`例如，即使登錄資源名稱為大寫或混合大小寫（如`myRegistry`）。

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>啟用並取得 Docker daemon 的偵錯工具記錄檔    

從`dockerd` `debug`選項開始。 首先，建立 Docker daemon 設定檔（）`/etc/docker/daemon.json`（如果不存在），然後新增`debug`選項：

```json
{    
    "debug": true    
}
```

然後，重新開機此 daemon。 例如，使用 Ubuntu 14.04：

```bash
sudo service docker restart
```

您可以在[Docker 檔](https://docs.docker.com/engine/admin/#enable-debugging)中找到詳細資料。    

 * 視您的系統而定，可能會在不同的位置產生記錄。 例如，針對 Ubuntu 14.04，它是`/var/log/upstart/docker.log`。    
如需詳細資訊，請參閱[Docker 檔](https://docs.docker.com/engine/admin/#read-the-logs)。    

 * 針對適用於 Windows 的 Docker，記錄檔會在% LOCALAPPDATA%/docker/. 之下產生 不過，它可能尚未包含所有的調試資訊。    

   若要存取完整的背景程式記錄檔，您可能需要一些額外的步驟：

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    現在您可以存取執行之 VM 的所有檔案`dockerd`。 記錄檔位於`/var/log/docker.log`。

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>新的使用者權限在更新之後可能不會立即生效

當您將新的許可權（新角色）授與服務主體時，變更可能不會立即生效。 可能有二個原因：

* Azure Active Directory 角色指派延遲。 一般來說，速度很快，但可能需要幾分鐘的時間，因為傳播延遲。
* ACR token 伺服器上的許可權延遲。 這最多可能需要花費 10 分鐘。 若要減輕問題， `docker logout`您可以在1分鐘後使用相同的使用者再次驗證：

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

目前 ACR 不支援使用者刪除主要複寫。 因應措施是在範本中包含「主複寫建立」，但藉由新增`"condition": false`來略過其建立，如下所示：

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>未在直接 REST API 呼叫上以正確的格式提供驗證資訊

您可能會遇到`InvalidAuthenticationInfo`錯誤，特別是搭配`curl`選項`-L`使用工具`--location` （以遵循重新導向）。
例如，使用`curl` with `-L`選項和基本驗證來提取 blob：

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

可能會產生下列回應：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

根本原因是某些`curl`執行會遵循從原始要求的標頭重新導向。

若要解決這個問題，您需要以手動方式追蹤不含標頭的重新導向。 使用的`-D -`選項來`curl`列印回應標頭，然後解壓縮： `Location`標頭：

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>為什麼 Azure 入口網站不會列出我的所有存放庫或標籤？ 

如果您使用 Microsoft Edge/IE 瀏覽器，您最多可以看到100存放庫或標籤。 如果您的登錄有100個以上的存放庫或標籤，建議您使用 Firefox 或 Chrome 瀏覽器來列出所有的儲存機制或標籤。

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>為什麼 Azure 入口網站無法提取存放庫或標籤？

瀏覽器可能無法將提取存放庫或標記的要求傳送至伺服器。 可能的原因有很多種，例如：

* 缺少網路連線能力
* 防火牆
* Ad 封鎖器
* DNS 錯誤

請洽詢您的網路系統管理員，或檢查您的網路設定和連線能力。 請嘗試`az acr check-health -n yourRegistry`使用您的 Azure CLI 來執行，以檢查您的環境是否能夠連線至容器登錄。 此外，您也可以在瀏覽器中嘗試 incognito 或私用會話，以避免任何過時的瀏覽器快取或 cookie。

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>為什麼我的提取或推送要求失敗，但不允許作業？

以下是一些可能不允許作業的案例：
* 不再支援傳統登錄。 請使用[az acr update](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update)或 Azure 入口網站升級至支援的[sku](https://aka.ms/acr/skus) 。
* 映射或存放庫可能已鎖定，所以無法刪除或更新。 您可以使用[az acr show repository](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock)命令來查看目前的屬性。
* 如果映射處於隔離中，則不允許某些作業。 深入瞭解[隔離](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)。

### <a name="how-do-i-collect-http-traces-on-windows"></a>如何? 在 Windows 上收集 HTTP 追蹤？

#### <a name="prerequisites"></a>Prerequisites

- 在 fiddler 中啟用解密 HTTPs：<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- 啟用 Docker 以透過 Docker ui 使用 proxy：<https://docs.docker.com/docker-for-windows/#proxies>
- 完成時，請務必還原。  Docker 無法使用此功能並 fiddler 未執行。

#### <a name="windows-containers"></a>Windows 容器

將 Docker proxy 設定為127.0.0.1：8888

#### <a name="linux-containers"></a>Linux 容器

尋找 Docker vm 虛擬交換器的 ip：

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

將 Docker proxy 設定為上一個命令和埠8888的輸出（例如為10.0.75.1：8888）

## <a name="tasks"></a>工作

- [如何? 批次取消執行？](#how-do-i-batch-cancel-runs)
- [如何? 在 az acr build 命令中包含 git 資料夾嗎？](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [工作是否支援來源觸發程式的 GitLab？](#does-tasks-support-gitlab-for-source-triggers)
- [哪些 git 存放庫管理服務支援工作？](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>如何? 批次取消執行？

下列命令會取消指定登錄中所有正在執行的工作。

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>如何? 在 az acr build 命令中包含 git 資料夾嗎？

如果您將本機源資料夾傳遞至`az acr build`命令，預設會`.git`將資料夾從上傳的封裝中排除。 您可以使用下列`.dockerignore`設定來建立檔案。 它會指示命令還原已上傳之`.git`套件中的所有檔案。 

`!.git/**`

此設定也適用于`az acr run`命令。

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>工作是否支援來源觸發程式的 GitLab？

我們目前不支援來源觸發程式的 GitLab。

### <a name="what-git-repository-management-service-does-tasks-support"></a>哪些 git 存放庫管理服務支援工作？

| Git 服務 | 來源內容 | 手動組建 | 透過認可觸發程式自動建立 |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | 是 | 是 |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | 是 | 是 |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | 是 | 否 |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | 是 | 否 |

## <a name="run-error-message-troubleshooting"></a>執行錯誤訊息的疑難排解

| 錯誤訊息 | 疑難排解指南 |
|---|---|
|未設定 VM 的存取權，因此找不到任何訂用帳戶|如果您在 ACR 工作中使用`az login --identity` ，就會發生這種情況。 這是暫時性錯誤，當您的受控識別的角色指派尚未傳播時，就會發生這種情況。 請稍候幾秒，再重試工作。|

## <a name="cicd-integration"></a>CI/CD 整合

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub 動作](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>後續步驟

* [深入瞭解](container-registry-intro.md)Azure Container Registry。
