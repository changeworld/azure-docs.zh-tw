---
title: 常見問題集
description: 與 Azure 容器註冊表服務相關的常見問題的解答
author: sajayantony
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: c0d51c9c31e4e6859eaedce371efeafaa5fd4f46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403225"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>有關 Azure 容器註冊表的常見問題

本文涉及有關 Azure 容器註冊表的常見問題和已知問題。

## <a name="resource-management"></a>資源管理

- [是否可以使用資源管理器範本創建 Azure 容器註冊表？](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [是否有安全性漏洞掃描 ACR 中的圖像？](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [如何使用 Azure 容器註冊表配置庫伯奈斯？](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [如何獲取容器註冊表的管理員憑據？](#how-do-i-get-admin-credentials-for-a-container-registry)
- [如何在資源管理器範本中獲取管理員憑據？](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [刪除複製失敗，禁止狀態，儘管複製使用 Azure CLI 或 Azure PowerShell 刪除](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [防火牆規則已成功更新，但它們不生效](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>是否可以使用資源管理器範本創建 Azure 容器註冊表？

是。 下面是[一個範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry)，可用於創建註冊表。

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>是否有安全性漏洞掃描 ACR 中的圖像？

是。 請參閱 Azure[安全中心](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)[、Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/)和[Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)的文檔。

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>如何使用 Azure 容器註冊表配置庫伯奈斯？

請參閱[庫伯內斯文檔](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr)和[Azure 庫伯奈斯服務](../aks/cluster-container-registry-integration.md)的步驟。

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>如何獲取容器註冊表的管理員憑據？

> [!IMPORTANT]
> 管理員使用者帳戶專為單個使用者訪問註冊表而設計，主要用於測試目的。 不建議將管理帳戶認證與其他使用者共用。 針對遠端控制案例的使用者和服務主體，建議使用個人身分識別。 請參閱[身份驗證概述](container-registry-authentication.md)。

在獲取管理員憑據之前，請確保啟用了註冊表的管理員使用者。

要使用 Azure CLI 獲取憑據，請使用以下操作：

```azurecli
az acr credential show -n myRegistry
```

使用 Azure 電源外殼：

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>如何在資源管理器範本中獲取管理員憑據？

> [!IMPORTANT]
> 管理員使用者帳戶專為單個使用者訪問註冊表而設計，主要用於測試目的。 不建議將管理帳戶認證與其他使用者共用。 針對遠端控制案例的使用者和服務主體，建議使用個人身分識別。 請參閱[身份驗證概述](container-registry-authentication.md)。

在獲取管理員憑據之前，請確保啟用了註冊表的管理員使用者。

要獲取第一個密碼：

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

要獲取第二個密碼：

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>刪除複製失敗，禁止狀態，儘管複製使用 Azure CLI 或 Azure PowerShell 刪除

當使用者對註冊表具有許可權，但對訂閱沒有讀取器級別許可權時，將看到該錯誤。 要解決此問題，請向使用者分配訂閱上的 Reader 許可權：


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>防火牆規則已成功更新，但它們不生效

傳播防火牆規則更改需要一些時間。 更改防火牆設置後，請等待幾分鐘，然後驗證此更改。


## <a name="registry-operations"></a>登錄作業

- [如何訪問 Docker 註冊表 HTTP API V2？](#how-do-i-access-docker-registry-http-api-v2)
- [如何刪除存儲庫中任何標記未引用的所有清單？](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [為什麼刪除圖像後註冊表配額使用量不會減少？](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [如何驗證存儲配額更改？](#how-do-i-validate-storage-quota-changes)
- [在容器中運行 CLI 時，如何使用註冊表進行身份驗證？](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [如何啟用 TLS 1.2？](#how-to-enable-tls-12)
- [Azure 容器註冊表是否支援內容信任？](#does-azure-container-registry-support-content-trust)
- [如何在未經許可的情況下授予對圖像進行拉取或推送的存取權限以管理註冊表資源？](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [如何為註冊表啟用自動映射隔離](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>如何訪問 Docker 註冊表 HTTP API V2？

ACR 支援 Docker 註冊表 HTTP API V2。 可以在 訪問 API。 `https://<your registry login server>/v2/` 範例： `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>如何刪除存儲庫中任何標記未引用的所有清單？

如果您處於 bash：

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

對於電源外殼：

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

注意：您可以添加`-y`刪除命令以跳過確認。

有關詳細資訊，請參閱在[Azure 容器註冊表中刪除容器映射](container-registry-delete.md)。

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>為什麼刪除圖像後註冊表配額使用量不會減少？

如果基礎層仍被其他容器映射引用，則可能發生這種情況。 如果刪除沒有引用的圖像，註冊表使用方式將在幾分鐘內更新。

### <a name="how-do-i-validate-storage-quota-changes"></a>如何驗證存儲配額更改？

使用以下 docker 檔創建具有 1GB 圖層的圖像。 這可確保映射具有不由註冊表中的任何其他映射共用的圖層。

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

使用 docker CLI 生成映射並將映射推送到註冊表。

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

您應該能夠看到 Azure 門戶中的存儲使用量有所增加，或者可以使用 CLI 查詢使用方式。

```azurecli
az acr show-usage -n myregistry
```

使用 Azure CLI 或閘戶刪除映射，並在幾分鐘內檢查更新的使用方式。

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>在容器中運行 CLI 時，如何使用註冊表進行身份驗證？

您需要通過安裝 Docker 通訊端來運行 Azure CLI 容器：

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

在容器中，安裝`docker`：

```bash
apk --update add docker
```

然後使用註冊表進行身份驗證：

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>如何啟用 TLS 1.2？

使用任何最近的 docker 用戶端（版本 18.03.0 及以上）啟用 TLS 1.2。 

> [!IMPORTANT]
> 從 2020 年 1 月 13 日開始，Azure Container Registry 將要求所有安全連線來自伺服器和應用程式，才能使用 TLS 1.2。 TLS 1.0 和 1.1 的支援將會淘汰。

### <a name="does-azure-container-registry-support-content-trust"></a>Azure 容器註冊表是否支援內容信任？

可以，您可以在 Azure 容器註冊表中使用受信任的映射，因為[Docker 公證已](https://docs.docker.com/notary/getting_started/)集成並可以啟用。 有關詳細資訊，請參閱[Azure 容器註冊表中的內容信任](container-registry-content-trust.md)。


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>指紋的檔位於何處？

在`~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`下 ：

* 所有角色的公開金鑰和證書（委派角色除外）都存儲在 中`root.json`。
* 委派角色的公開金鑰和憑證存放區在其父角色的 JSON 檔中（例如`targets.json`該`targets/releases`角色）。

建議在 Docker 和公證用戶端完成總體 TUF 驗證後驗證這些公開金鑰和證書。

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>如何在未經許可的情況下授予對圖像進行拉取或推送的存取權限以管理註冊表資源？

ACR 支援提供不同級別許可權的[自訂角色](container-registry-roles.md)。 具體來說，`AcrPull`角色`AcrPush`允許使用者在未經 Azure 管理註冊表資源的許可權的情況下提取和/或推送映射。

* Azure 門戶：註冊表 ->存取控制 （IAM） ->`AcrPull`添加`AcrPush`（選擇或用於角色）。
* Azure CLI：通過運行以下命令查找註冊表的資源識別碼：

  ```azurecli
  az acr show -n myRegistry
  ```
  
  然後，`AcrPull`您可以將 或`AcrPush`角色指派給使用者（以下示例使用`AcrPull`）：

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  或者，將角色指派給其應用程式 ID 標識的服務原則：

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

然後，受讓人能夠對註冊表中的映射進行身份驗證和訪問。

* 要向註冊表進行身份驗證：
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 要列出存儲庫：

  ```azurecli
  az acr repository list -n myRegistry
  ```

* 要拉取圖像：

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

僅使用 或`AcrPull``AcrPush`角色時，受讓人沒有在 Azure 中管理註冊表資源的許可權。 例如，`az acr list`或`az acr show -n myRegistry`不會顯示註冊表。

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>如何為註冊表啟用自動映射隔離？

圖像隔離目前是 ACR 的預覽功能。 您可以啟用註冊表的隔離模式，以便普通使用者只能看到成功通過安全掃描的圖像。 有關詳細資訊，請參閱[ACR GitHub 存儲庫](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)。

## <a name="diagnostics-and-health-checks"></a>診斷和運行狀況檢查

- [檢查運行狀況`az acr check-health`](#check-health-with-az-acr-check-health)
- [Docker 拉拔失敗，出現錯誤：net/HTTP： 在等待連接時已取消請求（等待標頭時超過用戶端.超時）](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [Docker 推送成功，但 Docker 拉拔失敗，出現錯誤：未授權：需要身份驗證](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`成功，但 Docker 命令失敗，錯誤：未經授權的身份驗證要求](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [啟用並獲取 docker 守護進程調試日誌](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [更新後，新使用者許可權可能不會立即生效](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [在直接 REST API 呼叫上，身份驗證資訊未以正確的格式提供](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [為什麼 Azure 門戶不列出所有存儲庫或標記？](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [為什麼 Azure 門戶無法獲取存儲庫或標記？](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [為什麼我的拉或推送請求在不允許的操作下失敗？](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [如何在 Windows 上收集 HTTP 跟蹤？](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>檢查運行狀況`az acr check-health`

要解決常見環境和註冊表問題，請參閱[檢查 Azure 容器註冊表的運行狀況](container-registry-check-health.md)。

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>Docker 拉拔失敗，出現錯誤：net/HTTP： 在等待連接時已取消請求（等待標頭時超過用戶端.超時）

 - 如果此錯誤是暫時性問題，則重試將成功。
 - 如果`docker pull`連續失敗，則 Docker 守護進程可能有問題。 此問題通常可以通過重新開機 Docker 守護進程來緩解。 
 - 如果在重新開機 Docker 守護進程後繼續看到此問題，則問題可能是電腦存在一些網路連接問題。 要檢查電腦上的常規網路是否正常，請運行以下命令以測試終結點連接。 包含此`az acr`連接檢查命令的最小版本為 2.2.9。 如果使用舊版本，請升級 Azure CLI。
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - 您應該始終對所有 Docker 用戶端操作具有重試機制。

### <a name="docker-pull-is-slow"></a>碼頭拉慢
使用[此工具](http://www.azurespeed.com/Azure/Download)測試機器網路下載速度。 如果電腦網路速度較慢，請考慮在註冊表相同的區域中使用 Azure VM。 這通常能加快網路速度。

### <a name="docker-push-is-slow"></a>碼頭推進速度緩慢
使用[此工具](http://www.azurespeed.com/Azure/Upload)測試機器網路上傳速度。 如果電腦網路速度較慢，請考慮在註冊表相同的區域中使用 Azure VM。 這通常能加快網路速度。

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker 推送成功，但 Docker 拉拔失敗，出現錯誤：未授權：需要身份驗證

預設情況下啟用的 Docker 守護進程`--signature-verification`的紅色帽子版本可能發生此錯誤。 您可以通過運行以下命令來檢查紅帽企業 Linux （RHEL） 或 Fedora 的 Docker 守護進程選項：

```bash
grep OPTIONS /etc/sysconfig/docker
```

例如，Fedora 28 伺服器具有以下 docker 守護進程選項：

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

如果`--signature-verification=false`缺少，`docker pull`則失敗時出錯，錯誤類似于：

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

若要解決此錯誤：
1. 將該選項`--signature-verification=false`添加到 Docker 守護進程設定檔`/etc/sysconfig/docker`。 例如：
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. 通過運行以下命令重新開機 Docker 守護進程服務：
   
   ```bash
   sudo systemctl restart docker.service
   ```

詳細資訊`--signature-verification`可以通過運行`man dockerd`找到。

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr 登錄成功，但 Docker 失敗，出錯：未經授權的身份驗證要求

請確保使用所有小寫伺服器 URL，例如 ，`docker push myregistry.azurecr.io/myimage:latest`即使註冊表資源名稱是大寫或混合大小寫，如`myRegistry`。

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>啟用並獲取 Docker 守護進程調試日誌  

從`dockerd`選項開始`debug`。 首先，創建 Docker 守護進程設定檔`/etc/docker/daemon.json`（ ） 如果不存在，並添加選項`debug`：

```json
{   
    "debug": true   
}
```

然後，重新開機守護進程。 例如，使用 Ubuntu 14.04：

```bash
sudo service docker restart
```

詳細資訊可在[Docker 文檔中](https://docs.docker.com/engine/admin/#enable-debugging)找到。 

 * 日誌可能在不同的位置生成，具體取決於您的系統。 例如，對於 Ubuntu 14.04，它是`/var/log/upstart/docker.log`。   
有關詳細資訊，請參閱[Docker 文檔](https://docs.docker.com/engine/admin/#read-the-logs)。    

 * 對於 Windows 的 Docker，日誌在 %LOCALAPPDATA%/docker/下生成。 但是，它可能還不包含所有調試資訊。   

   為了訪問完整的守護進程日誌，您可能需要一些額外的步驟：

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    現在，您可以訪問正在運行的`dockerd`VM 的所有檔。 日誌位於`/var/log/docker.log`。

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>更新後，新使用者許可權可能不會立即生效

當您向服務主體授予新許可權（新角色）時，更改可能不會立即生效。 可能有二個原因：

* Azure 活動目錄角色指派延遲。 通常它的速度很快，但由於傳播延遲，可能需要幾分鐘時間。
* ACR 權杖伺服器上的許可權延遲。 這最多可能需要花費 10 分鐘。 為了緩解，您可以在`docker logout`1 分鐘後與同一使用者再次進行身份驗證：

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

目前 ACR 不支援使用者刪除家庭複製。 解決方法是在範本中包括家庭複製創建，但通過添加`"condition": false`示例跳過其創建，如下所示：

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>在直接 REST API 呼叫上，身份驗證資訊未以正確的格式提供

可能會遇到錯誤`InvalidAuthenticationInfo`，特別是使用帶有選項`curl``-L`的該工具 （`--location`跟隨重定向）。
例如，使用`curl``-L`選項和基本驗證獲取 blob：

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

可能導致以下回應：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

根本原因是某些`curl`實現遵循從原始請求的標頭重定向。

要解決此問題，您需要手動按照重定向操作，而無需標頭。 使用`-D -`選項`curl`列印回應標頭，然後提取：`Location`標頭：

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>為什麼 Azure 門戶不列出所有存儲庫或標記？ 

如果您使用的是 Microsoft 邊緣/IE 瀏覽器，您最多隻能看到 100 個存儲庫或標記。 如果您的註冊表包含超過 100 個存儲庫或標籤，我們建議您使用 Firefox 或 Chrome 瀏覽器列出所有存儲庫或標籤。

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>為什麼 Azure 門戶無法獲取存儲庫或標記？

瀏覽器可能無法向伺服器發送提取存儲庫或標記的請求。 可能有多種原因，例如：

* 缺乏網路連接
* 防火牆
* 廣告攔截器
* DNS 錯誤

請與您的網路系統管理員聯繫或檢查您的網路設定和連接。 嘗試使用`az acr check-health -n yourRegistry`Azure CLI 運行，以檢查環境是否能夠連接到容器註冊表。 此外，您還可以在瀏覽器中嘗試隱身或私人會話，以避免任何陳舊的瀏覽器緩存或 Cookie。

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>為什麼我的拉或推送請求在不允許的操作下失敗？

下面是一些可能不允許操作的情況：
* 不再支援經典註冊表。 請使用[az acr 更新](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update)或 Azure 門戶升級到受支援的[SKU。](https://aka.ms/acr/skus)
* 映射或存儲庫可能已鎖定，因此無法刪除或更新。 您可以使用 az [acr 顯示存儲庫](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock)命令來查看當前屬性。
* 如果映射處於隔離狀態，則不允許執行某些操作。 瞭解有關[隔離](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)的更多。

### <a name="how-do-i-collect-http-traces-on-windows"></a>如何在 Windows 上收集 HTTP 跟蹤？

#### <a name="prerequisites"></a>Prerequisites

- 在小提琴中啟用解密 HTTPs：<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- 使 Docker 能夠通過 Docker ui 使用代理：<https://docs.docker.com/docker-for-windows/#proxies>
- 請務必在完成時恢復。  Docker 不會使用啟用此功能，並且 fiddler 不會運行。

#### <a name="windows-containers"></a>Windows 容器

將 Docker 代理配置為 127.0.0.1：8888

#### <a name="linux-containers"></a>Linux 容器

查找 Docker vm 虛擬交換器的 ip：

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

將 Docker 代理配置為前一命令和埠 8888 的輸出（例如 10.0.75.1：8888）

## <a name="tasks"></a>工作

- [如何批量取消運行？](#how-do-i-batch-cancel-runs)
- [如何在 az acr 生成命令中包括 .git 資料夾？](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [任務是否支援 GitLab 源觸發器？](#does-tasks-support-gitlab-for-source-triggers)
- [任務支援什麼 git 存儲庫管理服務？](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>如何批量取消運行？

以下命令取消指定註冊表中的所有正在運行的任務。

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>如何在 az acr 生成命令中包括 .git 資料夾？

如果將本地源資料夾傳遞給命令`az acr build`，預設情況下，`.git`該資料夾將從上載的包中排除。 您可以創建具有`.dockerignore`以下設置的檔。 它告訴命令還原上載包中的所有`.git`檔。 

`!.git/**`

此設置也適用于命令`az acr run`。

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>任務是否支援 GitLab 源觸發器？

我們目前不支援 GitLab 源觸發器。

### <a name="what-git-repository-management-service-does-tasks-support"></a>任務支援什麼 git 存儲庫管理服務？

| Git 服務 | 源上下文 | 手動生成 | 通過提交觸發器自動生成 |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | 是 | 是 |
| Azure Repos | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | 是 | 是 |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | 是 | 否 |
| BitBucket | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | 是 | 否 |

## <a name="run-error-message-troubleshooting"></a>運行錯誤訊息故障排除

| 錯誤訊息 | 疑難排解指南 |
|---|---|
|未為 VM 配置任何訪問，因此未找到訂閱|如果您在 ACR 任務中使用`az login --identity`，則可能發生此情況。 這是一個暫時性錯誤，在託管標識的角色指派尚未傳播時發生。 等待幾秒鐘後再重試工作。|

## <a name="cicd-integration"></a>CI/CD 集成

- [圓環](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub 操作](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>後續步驟

* [瞭解有關](container-registry-intro.md)Azure 容器註冊表的更多詳細資訊。
