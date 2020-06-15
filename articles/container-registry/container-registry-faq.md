---
title: 常見問題集
description: Azure Container Registry 服務相關常見問題的解答
author: sajayantony
ms.topic: article
ms.date: 03/18/2020
ms.author: sajaya
ms.openlocfilehash: 005c035468a4225f96e8ef69b2ef31a82bf7eedb
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682823"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>關於 Azure Container Registry 的常見問題集

本文說明 Azure Container Registry 的常見問題和已知問題。

## <a name="resource-management"></a>資源管理

- [我可以使用 Resource Manager 範本建立 Azure container registry 嗎？](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [ACR 中的映像是否有安全性弱點掃描？](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [如何使用 Azure Container Registry 設定 Kubernetes？](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [如何取得容器登錄的系統管理員認證？](#how-do-i-get-admin-credentials-for-a-container-registry)
- [如何取得 Resource Manager 範本中的管理員認證？](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [雖然使用 Azure CLI 或 Azure PowerShell 刪除複寫，但是刪除複寫失敗，並出現「禁止」狀態](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [已成功更新防火牆規則，但並未生效](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>我可以使用 Resource Manager 範本建立 Azure container registry 嗎？

是。 以下是您可以用來建立登錄的[範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry)。

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>ACR 中的映像是否有安全性弱點掃描？

是。 請參閱 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)、[Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) 和 [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) 中的文件。

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>如何使用 Azure Container Registry 設定 Kubernetes？

請參閱 [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) 的文件和 [Azure Kubernetes Service](../aks/cluster-container-registry-integration.md) 的步驟。

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>如何取得容器登錄的系統管理員認證？

> [!IMPORTANT]
> 管理使用者帳戶是專為讓單一使用者存取登錄而設計，主要用於測試。 不建議將管理帳戶認證與其他使用者共用。 針對遠端控制案例的使用者和服務主體，建議使用個人身分識別。 請參閱 [AD 驗證概觀](container-registry-authentication.md)。

取得系統管理員認證之前，請確定已啟用登錄的系統管理員使用者。

若要使用 Azure CLI 取得認證：

```azurecli
az acr credential show -n myRegistry
```

使用 Azure PowerShell：

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>如何取得 Resource Manager 範本中的管理員認證？

> [!IMPORTANT]
> 管理使用者帳戶是專為讓單一使用者存取登錄而設計，主要用於測試。 不建議將管理帳戶認證與其他使用者共用。 針對遠端控制案例的使用者和服務主體，建議使用個人身分識別。 請參閱 [AD 驗證概觀](container-registry-authentication.md)。

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

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>雖然使用 Azure CLI 或 Azure PowerShell 刪除複寫，但是刪除複寫失敗，並出現「禁止」狀態

使用者具有登錄的權限，但沒有訂閱的讀取者層級權限時，就會出現此錯誤。 若要解決此問題，請將訂閱的讀取者權限指派給使用者：


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>已成功更新防火牆規則，但並未生效

散佈防火牆規則變更需要一些時間。 變更防火牆設定後，請稍候幾分鐘，再確認此變更。


## <a name="registry-operations"></a>登錄作業

- [如何存取 Docker Registry HTTP API V2？](#how-do-i-access-docker-registry-http-api-v2)
- [如何刪除存放庫中所有未由任何標籤參考的資訊清單？](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [為何在刪除映像之後，登錄配額使用量不會降低？](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [如何驗證儲存體配額變更？](#how-do-i-validate-storage-quota-changes)
- [在容器中執行 CLI 時，如何向我的登錄進行驗證？](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [如何啟用 TLS 1.2？](#how-to-enable-tls-12)
- [Azure Container Registry 是否支援內容信任？](#does-azure-container-registry-support-content-trust)
- [如何授與提取或推送映像的存取權，但不授與管理登錄資源的權限？](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [如何啟用登錄的自動映像隔離功能？](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [如何啟用匿名提取存取？](#how-do-i-enable-anonymous-pull-access)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>如何存取 Docker Registry HTTP API V2？

ACR 支援 Docker Registry HTTP API V2。 可以在 `https://<your registry login server>/v2/` 存取 API。 範例： `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>如何刪除存放庫中所有未由任何標籤參考的資訊清單？

如果您在 Bash 上：

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

如果是 PowerShell：

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

注意:您可以在 delete 命令中新增 `-y`，以略過確認。

如需詳細資訊，請參閱[刪除 Azure Container Registry 中的容器映像](container-registry-delete.md)。

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>為何在刪除映像之後，登錄配額使用量不會降低？

如果基礎層仍被其他容器映像參考，就可能發生這種情況。 如果您刪除沒有參考的映像，登錄使用會在幾分鐘內更新。

### <a name="how-do-i-validate-storage-quota-changes"></a>如何驗證儲存體配額變更？

使用下列 Docker 檔案，建立具有 1GB 圖層的映像。 這可確保映像具有未由登錄中的其他任何映像共用的圖層。

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

使用 Docker CLI 建立映像並將其推送至您的登錄。

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

您應該會看到 Azure 入口網站中的儲存體使用量已增加，您也可以使用 CLI 查詢使用方式。

```azurecli
az acr show-usage -n myregistry
```

使用 Azure CLI 或入口網站刪除映像，並在幾分鐘內檢查已更新的使用方式。

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>在容器中執行 CLI 時，如何向我的登錄進行驗證？

您需要掛接 Docker 通訊端來執行 Azure CLI 容器：

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

在容器中，安裝 `docker`：

```bash
apk --update add docker
```

然後使用您的登錄進行驗證：

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>如何啟用 TLS 1.2？

使用任何最近的 Docker 用戶端 (18.03.0 版和更新版本) 來啟用 TLS 1.2。 

> [!IMPORTANT]
> 從 2020 年 1 月 13 日開始，Azure Container Registry 將要求所有安全連線來自伺服器和應用程式，才能使用 TLS 1.2。 TLS 1.0 和 1.1 的支援將會淘汰。

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry 是否支援內容信任？

是，您可以在 Azure Container Registry 中使用受信任的映像，因為 [Docker Notary](https://docs.docker.com/notary/getting_started/) 已經整合，而且可以啟用。 如需詳細資訊，請參閱[Azure Container Registry 中的內容信任](container-registry-content-trust.md)。


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>指紋的檔案位於何處？

在 `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` 底下：

* 所有角色的公開金鑰和憑證 (委派角色除外) 都會儲存在 `root.json` 中。
* 委派角色的公開金鑰和憑證會儲存在其上層角色的 JSON 檔案中 (例如，`targets/releases` 角色的 `targets.json`)。

建議在 Docker 和 Notary 用戶端完成整體 TUF 驗證之後，驗證這些公開金鑰和憑證。

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>如何授與提取或推送映像的存取權，但不授與管理登錄資源的權限？

ACR 支援提供不同權限層級的[自訂角色](container-registry-roles.md)。 具體而言，`AcrPull` 和 `AcrPush` 角色可讓使用者提取和/或推送映像，而不需要在 Azure 中管理登錄資源。

* Azure 入口網站：您的登錄 -> [存取控制 (IAM)] -> [新增] (選取角色的 `AcrPull` 或 `AcrPush`)。
* Azure CLI：執行下列命令來尋找登錄的資源識別碼：

  ```azurecli
  az acr show -n myRegistry
  ```
  
  接著，您可以將 `AcrPull` 或 `AcrPush` 角色指派給使用者 (下列範例會使用 `AcrPull`)：

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  或者，將角色指派給其應用程式識別碼所識別的服務主體：

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

然後，該受託人就能夠驗證和存取登錄中的映像。

* 若要向登錄進行驗證：
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 若要列出存放庫：

  ```azurecli
  az acr repository list -n myRegistry
  ```

* 若要提取映像：

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

只要使用 `AcrPull` 或 `AcrPush` 角色，則受託人就沒有在 Azure 中管理登錄資源的權限。 例如，`az acr list` 或 `az acr show -n myRegistry` 不會顯示登錄。

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>如何啟用登錄的自動映像隔離功能？

映像隔離目前是 ACR 的預覽功能。 您可以啟用登錄的隔離模式，讓一般使用者只能看見已成功通過安全性掃描的映像。 如需詳細資訊，請參閱 [ACR GitHub 存放庫](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)。

### <a name="how-do-i-enable-anonymous-pull-access"></a>如何啟用匿名提取存取？

設定匿名 (公用) 提取存取的 Azure 容器登錄目前為預覽功能。 若要啟用公用存取，請在 https://aka.ms/acr/support/create-ticket 開啟支援票證。 如需詳細資訊，請參閱 [Azure 意見反應論壇](https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/32517127-enable-anonymous-access-to-registries)。


## <a name="diagnostics-and-health-checks"></a>診斷和健康情況檢查

- [使用 `az acr check-health` 檢查健全狀況](#check-health-with-az-acr-check-health)
- [docker pull 失敗，並出現錯誤：net/http：要求在等候連接時取消 (用戶端等待標題時超過逾時時間)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [Docker push 成功，但 docker pull 失敗，發生錯誤：未經授權：需要驗證](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` 成功，但 Docker 命令失敗，發生錯誤：未經授權：需要驗證](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [啟用並取得 Docker 精靈的偵錯工具記錄檔](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [新的使用者權限在更新之後可能不會立即生效](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [未在直接 REST API 呼叫上以正確的格式提供驗證資訊](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [為什麼 Azure 入口網站不會列出我所有的存放庫或標籤？](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [為什麼 Azure 入口網站無法提取存放庫或標籤？](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [為什麼我的提取或推送要求失敗，且不允許作業？](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [如何在 Windows 上收集 HTTP 追蹤？](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>使用 `az acr check-health` 檢查健全狀況

若要針對常見的環境和登錄問題進行疑難排解，請參閱[檢查 Azure 容器登錄的健全狀況](container-registry-check-health.md)。

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>Docker pull 失敗，並出現錯誤：net/http：要求在等候連接時取消 (用戶端等待標題時超過逾時時間)

 - 如果此錯誤是暫時性的問題，則重試將會成功。
 - 如果 `docker pull` 持續失敗，則 Docker 精靈可能發生問題。 重新啟動 Docker 精靈通常可以減輕問題。 
 - 如果您在重新啟動 Docker 精靈之後繼續看到此問題，則問題可能是電腦的網路連線問題。 若要檢查電腦上的一般網路是否狀況良好，請執行下列命令來測試端點連線能力。 包含此連線檢查命令的最低 `az acr` 版本為 2.2.9。 如果您使用的是較舊的版本，請升級您的 Azure CLI。
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - 在所有 Docker 用戶端操作上都應該一律有重試機制。

### <a name="docker-pull-is-slow"></a>Docker pull 的速度很慢
使用[此](http://www.azurespeed.com/Azure/Download)工具來測試您的電腦網路下載速度。 如果電腦網路速度很慢，請考慮在與您的登錄相同的區域中使用 Azure VM。 這通常可提供更快的網路速度。

### <a name="docker-push-is-slow"></a>Docker push 的速度很慢
使用[此](http://www.azurespeed.com/Azure/Upload)工具來測試電腦的網路上傳速度。 如果電腦網路速度很慢，請考慮在與您的登錄相同的區域中使用 Azure VM。 這通常可提供更快的網路速度。

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker push 成功，但 docker pull 失敗，發生錯誤：未經授權：需要驗證

此錯誤可能會發生在 Red Hat 版本的 Docker 精靈上，其中 `--signature-verification` 預設為啟用。 您可以執行下列命令來檢查 Red Hat Enterprise Linux (RHEL) 或 Fedora 的 Docker 精靈選項：

```bash
grep OPTIONS /etc/sysconfig/docker
```

例如，Fedora 28 伺服器具有下列 Docker 精靈選項：

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

由於 `--signature-verification=false` 遺失，`docker pull` 會失敗並出現類似以下的錯誤：

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

若要解決此錯誤：
1. 將選項 `--signature-verification=false` 新增至 Docker 精靈 設定檔 `/etc/sysconfig/docker`。 例如：
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. 執行下列命令來重新啟動 Docker 精靈服務：
   
   ```bash
   sudo systemctl restart docker.service
   ```

可以找到 `--signature-verification` 的詳細資料，方法為執行 `man dockerd`。

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login 成功，但 Docker 失敗，發生錯誤為：未經授權：需要驗證

即使登錄資源名稱為大寫或混合大小寫，例如 `myRegistry`，請確定您使用的是所有小寫伺服器 URL，例如 `docker push myregistry.azurecr.io/myimage:latest`。

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>啟用並取得 Docker 精靈的偵錯工具記錄檔    

使用 [`debug`] 選項啟動 `dockerd`。 首先，建立 Docker 精靈設定檔 (`/etc/docker/daemon.json`) (如果不存在)，然後新增 `debug` 選項：

```json
{    
    "debug": true    
}
```

然後，重新啟動精靈。 例如，使用 Ubuntu 14.04：

```bash
sudo service docker restart
```

如需詳細資料，請參閱 [Docker文件](https://docs.docker.com/engine/admin/#enable-debugging)。    

 * 視您的系統而定，可能會在不同的位置產生記錄。 例如，針對 Ubuntu 14.04，則為 `/var/log/upstart/docker.log`。    
如需詳細資料，請參閱 [Docker 文件](https://docs.docker.com/engine/admin/#read-the-logs)。    

 * 針對適用於 Windows 的 Docker，記錄檔會在 %LOCALAPPDATA%/docker/ 之下產生。 不過，它可能尚未包含所有的偵錯資訊。    

   若要存取完整的背景程式記錄檔，您可能需要一些額外的步驟：

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    現在您可以存取執行 `dockerd` 的 VM 中所有的檔案。 記錄檔位於 `/var/log/docker.log`。

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>新的使用者權限在更新之後可能不會立即生效

您將新的權限 (新角色) 授與服務主體時，變更可能不會立即生效。 有兩個可能的原因：

* Azure Active Directory 角色指派延遲。 一般來說速度很快，但可能由於散佈延遲而需要幾分鐘的時間。
* ACR 權杖伺服器上的權限延遲。 這項作業可能需要 10 分鐘的時間。 若要減輕問題，您可以 `docker logout`，然後在 1 分鐘後再以相同的使用者進行驗證：

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

目前 ACR 不支援使用者刪除主要複寫。 因應措施是在範本中包含「主要複寫建立」，但藉由新增 `"condition": false` 略過建立，如下所示：

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

您可能會遇到 `InvalidAuthenticationInfo` 錯誤，特別是使用 `curl` 工具搭配選項 `-L`、`--location` (以遵循重新導向)。
例如，使用 `curl` 搭配 `-L` 選項和基本驗證來提取 Blob：

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

可能會出現下列回應：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

根本原因是某些 `curl` 的實施會遵循從原始要求的標題重新導向。

若要解決這個問題，您需要以手動方式追蹤不含標題的重新導向。 使用 `curl` 的 `-D -` 選項來列印回應標題，然後解壓縮：`Location` 標題：

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>為什麼 Azure 入口網站不會列出我所有的存放庫或標籤？ 

如果您使用 Microsoft Edge/IE 瀏覽器，您最多可以看到 100 個存放庫或標籤。 如果您的登錄有 100 個以上的存放庫或標籤，建議您使用 Firefox 或 Chrome 瀏覽器來列出所有的存放庫或標籤。

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>為什麼 Azure 入口網站無法提取存放庫或標籤？

瀏覽器可能無法將提取存放庫或標籤的要求傳送至伺服器。 可能的原因有很多種，例如：

* 沒有網路連線
* 防火牆
* 廣告封鎖程式
* DNS 錯誤

請洽詢您的網路系統管理員，或檢查您的網路設定和連線能力。 請嘗試使用您的 Azure CLI 執行 `az acr check-health -n yourRegistry`，以檢查您的環境是否能夠連線至容器登錄。 此外，您也可以在瀏覽器中嘗試 incognito 或私用工作階段，以避免任何過時的瀏覽器快取或 Cookie。

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>為什麼我的提取或推送要求失敗，且不允許作業？

以下是可能不允許作業的一些案例：
* 已不再支援傳統登錄。 請使用 [az acr update](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) 或 Azure 入口網站升級至支援的[服務層級](https://aka.ms/acr/skus)。
* 映像或存放庫可能已鎖定，所以無法刪除或更新。 您可以使用 [az acr show repository](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) 命令來查看目前的屬性。
* 如果映像處於隔離中，則不允許某些作業。 深入瞭解[隔離](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)。

### <a name="how-do-i-collect-http-traces-on-windows"></a>如何在 Windows 上收集 HTTP 追蹤？

#### <a name="prerequisites"></a>Prerequisites

- 在 fiddler 中啟用解密 https：<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- 啟用 Docker 以透過 Docker ui 使用 Proxy：<https://docs.docker.com/docker-for-windows/#proxies>
- 完成時，請務必還原。  Docker 無法使用此功能且 Fiddler 不會執行。

#### <a name="windows-containers"></a>Windows 容器

將 Docker Proxy 設定為 127.0.0.1:8888

#### <a name="linux-containers"></a>Linux 容器

尋找 Docker VM 虛擬交換器的 IP：

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

將 Docker Proxy 設定為上一個命令和連接埠 8888 的輸出 (例如 10.0.75.1:8888)

## <a name="tasks"></a>工作

- [如何批次取消執行？](#how-do-i-batch-cancel-runs)
- [如何在 az acr build 命令中包含 git 資料夾？](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [工作是否支援來源觸發的 GitLab？](#does-tasks-support-gitlab-for-source-triggers)
- [哪些 Git 存放庫管理服務支援工作？](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>如何批次取消執行？

下列命令會取消指定登錄中所有正在執行的工作。

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>如何在 az acr build 命令中包含 Git 資料夾？

如果您將本機源資料夾傳遞至 `az acr build` 命令，預設會從上傳的封裝中排除 `.git` 資料夾。 您可以使用下列設定建立 `.dockerignore` 檔案。 它會指示命令還原已上傳套件中 `.git` 下的所有檔案。 

`!.git/**`

此設定也適用於 `az acr run` 命令。

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>工作是否支援來源觸發的 GitLab？

我們目前不支援來源觸發的 GitLab。

### <a name="what-git-repository-management-service-does-tasks-support"></a>哪些 git 存放庫管理服務支援工作？

| Git 服務 | 來源內容 | 手動組建 | 透過認可觸發自動建立 |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | 是 | 是 |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | 是 | 是 |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | 是 | 否 |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | 是 | 否 |

## <a name="run-error-message-troubleshooting"></a>執行錯誤訊息的疑難排解

| 錯誤訊息 | 疑難排解指南 |
|---|---|
|未設定 VM 的存取權，因此找不到任何訂閱|如果您在 ACR 工作中使用 `az login --identity`，就會發生這種情況。 這是暫時性錯誤，當您的受控識別的角色指派尚未散佈時，就會發生這種情況。 請稍候幾秒，再重試工作。|

## <a name="cicd-integration"></a>CI/CD 整合

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub 動作](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>後續步驟

* [深入瞭解](container-registry-intro.md) Azure Container Registry。
