---
title: Azure Arc 啟用的資料服務-版本資訊
description: 最新版本資訊
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 12/09/2020
ms.topic: conceptual
ms.openlocfilehash: 935ba888352d2454a609a40866ef10ccf13a2dac
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605457"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>版本資訊-Azure Arc 啟用的資料服務 (預覽) 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="december-2020"></a>2020 年 12 月

### <a name="new-capabilities--features"></a>新功能 & 功能

Azure Data CLI (`azdata`) 版本號碼：20.2.5。 下載時間 [https://aka.ms/azdata](https://aka.ms/azdata) 。

使用 Azure Data CLI () 搭配和命令來查看 SQL 受控執行個體和于 postgresql 超大規模的端點 `azdata` `azdata arc sql endpoint list` `azdata arc postgres endpoint list` 。

使用 Azure Data Studio 編輯 SQL 受控執行個體資源 (CPU 核心和記憶體) 要求和限制。
     
Azure Arc 啟用的于 postgresql 超大規模現在除了于 postgresql 的11和12版本之外，還支援還原時間點。 時間點還原功能可讓您指定要還原至的特定日期和時間。

Azure Arc 啟用的于 postgresql 超大規模之 pod 的命名慣例已變更。 它現在的格式如下： ServergroupName {r，s}-_n_。 例如，具有三個節點的伺服器群組、一個協調器節點和兩個背景工作節點都會表示為：
- `postgres02r-0` (協調器節點) 
- `postgres02s-0` (背景工作節點) 
- `postgres02s-1` (背景工作節點) 

### <a name="breaking-change"></a>重大變更

#### <a name="new-resource-provider"></a>新的資源提供者

此版本導入了名為的更新 [資源提供者](../../azure-resource-manager/management/azure-services-resource-providers.md) `Microsoft.AzureArcData` 。 在您可以使用這項功能之前，您必須先註冊此資源提供者。 

若要註冊此資源提供者： 

1. 在 Azure 入口網站中，選取 [**訂閱**] 
2. 選擇您的訂用帳戶
3. 在 [**設定**] 底下，選取 [**資源提供者**] 
4. 搜尋 `Microsoft.AzureArcData` 並選取 **註冊** 

您可以在 [Azure 資源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md)上查看詳細步驟。 這種變更也會移除您已上傳至 Azure 入口網站的所有現有 Azure 資源。 若要使用資源提供者，您需要更新資料控制器，並使用最新的 `azdata` CLI。  

### <a name="platform-release-notes"></a>平台版本資訊

#### <a name="direct-connectivity-mode"></a>直接連接模式

此版本導入了直接連接模式。 直接連線模式可讓資料控制器自動將使用量資訊上傳至 Azure。 在使用上傳的過程中，會在入口網站中自動建立 Arc 資料控制器資源（如果尚未透過 `azdata` 上傳建立）。  

您可以在建立資料控制站時指定直接連線。 下列範例會 `azdata arc dc create` `arc` 使用直接連線模式 () 來建立名為的資料控制器 `connectivity-mode direct` 。 執行範例之前，請 `<subscription id>` 以您的訂用帳戶 ID 取代。

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>已知問題

- 在 Azure Kubernetes Service (AKS) 上，不支援 Kubernetes 版本1.19。
- 在 Kubernetes 1.19 上 `containerd` 不受支援。
- Azure 中的資料控制器資源目前為 Azure 資源。 任何更新（例如刪除）都不會傳播回 kubernetes 叢集。
- 實例名稱不能超過13個字元
- 沒有 Azure Arc 資料控制器或資料庫實例的就地升級。
- 未簽署已啟用 Arc 的資料服務容器映像。  您可能需要設定 Kubernetes 節點，以允許提取未簽署的容器映像。  例如，如果您使用 Docker 作為容器執行時間，您可以設定 DOCKER_CONTENT_TRUST = 0 環境變數並重新啟動。  其他容器執行階段具有類似的選項，例如 [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration) 中就是如此。
- 無法從 Azure 入口網站建立 Azure Arc 啟用的 SQL 受控實例或于 postgresql 超大規模伺服器群組。
- 現在，如果您使用 NFS，則在 `allowRunAsRoot` `true` 建立 Azure Arc 資料控制器之前，您必須在部署設定檔檔案中將設定為。
- 僅限 SQL 和于 postgresql 登入驗證。  不支援 Azure Active Directory 或 Active Directory。
- 在 OpenShift 上建立資料控制器需要寬鬆的安全性條件約束。  如需詳細資料，請參閱文件。
- 如果您使用 Azure Kubernetes Service (使用 Azure Arc 資料控制器和資料庫實例的 Azure Stack Hub 來 AKS) 引擎，則不支援升級至較新的 Kubernetes 版本。 升級 Kubernetes 叢集之前，請先卸載 Azure Arc 資料控制器和所有資料庫實例。
- Azure Arc 啟用的資料服務目前不支援跨越 [多個可用性區域](../../aks/availability-zones.md) 的 AKS 叢集。 若要避免這個問題，當您在 Azure 入口網站中建立 AKS 叢集時，如果您選取區域可用的區域，請清除選取專案控制項中的所有區域。 請見下圖：

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="清除每個區域的核取方塊，以指定 [無]。":::

#### <a name="postgresql"></a>PostgreSQL

- Azure Arc 啟用的于 postgresql 超大規模無法還原到您所指出的相對時間點時，會傳回不正確的錯誤訊息。 例如，如果您指定的還原時間點早于備份所包含的時間點，則還原將會失敗，並顯示如下的錯誤訊息：錯誤： (404) 。 原因：找不到。 HTTP 回應主體： {"code"：404，"internalStatus"： "NOT_FOUND"，"reason"： "無法還原伺服器的備份 ...}
發生這種情況時，請在指出您有備份的日期範圍內的時間點之後，重新開機命令。 您將藉由列出備份並查看其取得日期，來判斷此範圍。
- 只有跨伺服器群組支援時間點還原。 還原時間點作業的目標伺服器不可以是您進行備份的來源伺服器。 它必須是不同的伺服器群組。 但是，相同的伺服器群組也支援完整還原。
- 執行完整還原時需要備份識別碼。 根據預設，如果您沒有指出備份識別碼，將會使用最新的備份。 這在此版本中無法運作。

## <a name="october-2020"></a>2020 年 10 月 

Azure Data CLI (`azdata`) 版本號碼：20.2.3。 下載時間 [https://aka.ms/azdata](https://aka.ms/azdata) 。

### <a name="breaking-changes"></a>重大變更

此版本引進了下列重大變更： 

* 在於 postgresql 自訂資源定義中 (.CRD) ，此詞彙 `shards` 已重新命名為 `workers` 。 這個詞彙 (`workers`) 符合命令列參數名稱。

* `azdata arc postgres server delete` 在刪除 postgres 實例之前提示您確認。  使用 `--force` 略過提示。

### <a name="additional-changes"></a>其他變更

* 已加入新的選擇性參數，並被 `azdata arc postgres server create` 呼叫 `--volume-claim mounts` 。 值是以逗號分隔的磁片區索取裝載清單。 磁片區宣告掛接是一對磁片區類型和 PVC 名稱。 目前唯一支援的磁片區類型為 `backup` 。  在於 postgresql 中，當磁片區類型為時 `backup` ，會將 PVC 載入至 `/mnt/db-backups` 。  這可讓您在 PostgresSQL 實例之間共用備份，以便在另一個實例中還原一個 PostgresSQL 實例的備份。

* PostgresSQL 自訂資源定義的新簡短名稱： 
  * `pg11` 
  * `pg12`
* 遙測上傳可為使用者提供下列其中一項：
   * 上傳至 Azure 或的點數 
   * 如果未將任何資料載入 Azure，則會出現一次重試的提示。
* `azdata arc dc debug copy-logs` 現在也會從 `/var/opt/controller/log` 資料夾讀取，並收集 Linux 上的于 postgresql 引擎記錄。
*   使用於 postgresql 超大規模建立及還原備份時，顯示作用中的指標。
* `azdata arc postrgres backup list` 現在包含備份大小資訊。
* [SQL 受控執行個體管理員名稱] 屬性已新增至 Azure 入口網站中的 [總覽] 分頁的右欄資料行。
* Azure Data Studio 支援設定于 postgresql 超大規模的背景工作節點、vCore 和記憶體設定數目。 
* Preview 支援 Postgres 11 和12版的備份/還原。

## <a name="september-2020"></a>2020 年 9 月

已發行 Azure Arc 啟用的資料服務以供公開預覽。 啟用 Arc 的資料服務可讓您在任何地方管理資料服務。

- SQL 受控執行個體
- PostgreSQL Hyperscale

如需相關指示，請參閱 [什麼是 Azure Arc 啟用的資料服務？](overview.md)

## <a name="next-steps"></a>後續步驟
  
> **只想試試看嗎？**  
> 在 AKS、AWS 彈性 Kubernetes 服務 (EKS) 、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中快速開始使用 [Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) 快速入門。

- [安裝用戶端工具](install-client-tools.md)
- [建立 Azure Arc 資料控制器](create-data-controller.md) (需要先安裝用戶端工具)
- [在 Azure Arc 上建立 Azure SQL 受控執行個體](create-sql-managed-instance.md) (需要先建立 Azure Arc 資料控制項)
- [在 Azure Arc 上建立適用於 PostgreSQL 的超大規模 Azure 資料庫伺服器群組](create-postgresql-hyperscale-server-group.md) (必須先建立 Azure Arc 資料控制項)
- [Azure 服務的資源提供者](../../azure-resource-manager/management/azure-services-resource-providers.md) \(部分機器翻譯\)
