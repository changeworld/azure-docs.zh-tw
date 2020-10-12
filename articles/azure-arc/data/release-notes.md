---
title: Azure Arc 啟用的資料服務-版本資訊
description: 最新版本資訊
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d22976254cc804ca53060fb284abde8e80a684e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319719"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>版本資訊-Azure Arc 啟用的資料服務 (預覽) 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>2020 年 9 月

已發行 Azure Arc 啟用的資料服務以供公開預覽。 啟用 Arc 的資料服務可讓您在任何地方管理資料服務。

- SQL 受控執行個體
- PostgreSQL Hyperscale

如需相關指示，請參閱 [什麼是 Azure Arc 啟用的資料服務？](overview.md)

### <a name="known-issues"></a>已知問題

下列問題適用于此版本：

* **刪除于 postgresql 超大規模伺服器群組**：如果您已變更伺服器群組或實例的設定，請先等候編輯作業完成，再刪除于 postgresql 超大規模伺服器群組。

* ** `azdata notebook run` 可能會失敗**：若要解決此問題，請 `azdata notebook run` 在 Python 虛擬環境中執行。 此問題也會在嘗試使用 Azure Data Studio deployment wizard 建立 SQL 受控實例或于 postgresql 超大規模伺服器群組時失敗。 在此情況下，您可以開啟筆記本，然後按一下筆記本頂端的 [ **全部執行** ] 按鈕。

## <a name="next-steps"></a>後續步驟

> **只想試試看嗎？**  
> 在 Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中快速開始使用 [Azure Arc 快速入門](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)。

[安裝用戶端工具](install-client-tools.md)

[建立 Azure Arc 資料控制器](create-data-controller.md) (需要先安裝用戶端工具)

[在 Azure Arc 上建立 Azure SQL 受控執行個體](create-sql-managed-instance.md) (需要先建立 Azure Arc 資料控制項)

[在 Azure Arc 上建立適用於 PostgreSQL 的超大規模 Azure 資料庫伺服器群組](create-postgresql-hyperscale-server-group.md) (必須先建立 Azure Arc 資料控制項)

## <a name="known-limitations-and-issues"></a>已知限制及問題

- SQL 受控實例名稱不能超過13個字元
- 沒有 Azure Arc 資料控制器或資料庫實例的就地升級。
- 未簽署已啟用 Arc 的資料服務容器映像。  您可能需要設定 Kubernetes 節點，以允許提取未簽署的容器映像。  例如，如果您使用 Docker 作為容器執行時間，您可以設定 DOCKER_CONTENT_TRUST = 0 環境變數並重新啟動。  其他容器執行階段具有類似的選項，例如 [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration) 中就是如此。
- 無法從 Azure 入口網站建立 Azure Arc 啟用的 SQL 受控實例或于 postgresql 超大規模伺服器群組。
- 現在，如果您使用 NFS，則在建立 Azure Arc 資料控制器之前，您必須先將部署設定檔檔案中的 allowRunAsRoot 設定為 true。
- 僅限 SQL 和于 postgresql 登入驗證。  不支援 Azure Active Directory 或 Active Directory。
- 在 OpenShift 上建立資料控制器需要寬鬆的安全性條件約束。  如需詳細資料，請參閱文件。
- 不支援調整 Postgres 超大規模背景 _工作節點的_ 數目。
- 如果您使用 Azure Kubernetes Service 引擎 (AKS 引擎) Azure Stack Hub 使用 Azure Arc 資料控制器和資料庫實例，則不支援升級至較新的 Kubernetes 版本。 升級 Kubernetes 叢集之前，請先卸載 Azure Arc 資料控制器和所有資料庫實例。
- 預覽不支援 Postgres 11 版引擎的備份/還原。 它只支援 Postgres 12 版的備份/還原。
