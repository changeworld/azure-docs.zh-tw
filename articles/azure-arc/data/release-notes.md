---
title: Azure Arc 啟用的資料服務-版本資訊
description: 最新版本資訊
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: 94074c2c5e11187252084832e5a20a197f6723fd
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359811"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>版本資訊-Azure Arc 啟用的資料服務 (預覽) 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

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

* 遙測上傳會提供使用者下列任一項：

   * 上傳至 Azure 的點數

     或 

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

## <a name="known-limitations-and-issues"></a>已知限制及問題

- 實例名稱不能超過13個字元
- 沒有 Azure Arc 資料控制器或資料庫實例的就地升級。
- 未簽署已啟用 Arc 的資料服務容器映像。  您可能需要設定 Kubernetes 節點，以允許提取未簽署的容器映像。  例如，如果您使用 Docker 作為容器執行時間，您可以設定 DOCKER_CONTENT_TRUST = 0 環境變數並重新啟動。  其他容器執行階段具有類似的選項，例如 [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration) 中就是如此。
- 無法從 Azure 入口網站建立 Azure Arc 啟用的 SQL 受控實例或于 postgresql 超大規模伺服器群組。
- 現在，如果您使用 NFS，則在 `allowRunAsRoot` `true` 建立 Azure Arc 資料控制器之前，您必須在部署設定檔檔案中將設定為。
- 僅限 SQL 和于 postgresql 登入驗證。  不支援 Azure Active Directory 或 Active Directory。
- 在 OpenShift 上建立資料控制器需要寬鬆的安全性條件約束。  如需詳細資料，請參閱文件。
- 如果您使用 Azure Kubernetes Service 引擎 (AKS 引擎) Azure Stack Hub 使用 Azure Arc 資料控制器和資料庫實例，則不支援升級至較新的 Kubernetes 版本。 升級 Kubernetes 叢集之前，請先卸載 Azure Arc 資料控制器和所有資料庫實例。
- Azure Kubernetes Service (AKS) ，Azure Arc 啟用的資料服務目前不支援跨越 [多個可用性區域](../../aks/availability-zones.md) 的叢集。 若要避免這個問題，當您在 Azure 入口網站中建立 AKS 叢集時，如果您選取區域可用的區域，請清除選取專案控制項中的所有區域。 請見下圖：

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="清除每個區域的核取方塊，以指定 [無]。":::


### <a name="known-issues-for-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc Enabled 于 postgresql 超大規模的已知問題   

- 預覽不支援于 postgresql 11 版引擎的備份/還原。 它只支援于 postgresql 12 版的備份/還原。
- `azdata arc dc debug copy-logs` 節點不會在 Windows 上收集于 postgresql 引擎記錄。
- 以剛刪除之伺服器群組的名稱重新建立伺服器群組可能會失敗或停止回應。 
   - **Workaround** 因應措施當您重新建立伺服器群組或等候先前刪除之伺服器群組的負載平衡器/外部服務時，請勿重複使用相同的名稱。 假設您已刪除的伺服器組名是並裝載 `postgres01` 在命名空間中 `arc` ，則在您重新建立具有相同名稱的伺服器群組之前，請等到 `postgres01-external-svc` kubectl 命令的輸出未顯示 `kubectl get svc -n arc` 。
 - 在 Azure Data Studio 中載入 [總覽] 頁面和 [計算 + 儲存體設定] 頁面的速度很慢。 



## <a name="next-steps"></a>後續步驟
  
> **只想試試看嗎？**  
> 在 Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中快速開始使用 [Azure Arc 快速入門](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)。

[安裝用戶端工具](install-client-tools.md)

[建立 Azure Arc 資料控制器](create-data-controller.md) (需要先安裝用戶端工具)

[在 Azure Arc 上建立 Azure SQL 受控執行個體](create-sql-managed-instance.md) (需要先建立 Azure Arc 資料控制項)

[在 Azure Arc 上建立適用於 PostgreSQL 的超大規模 Azure 資料庫伺服器群組](create-postgresql-hyperscale-server-group.md) (必須先建立 Azure Arc 資料控制項)
