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
ms.openlocfilehash: 5908083be4e6ed389b606754ffef41a4a371c3e3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934166"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>版本資訊-Azure Arc 啟用的資料服務 (預覽) 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>2020 年 9 月

已發行 Azure Arc 啟用的資料服務以供公開預覽。 啟用 Arc 的資料服務可讓您在任何地方管理資料服務。

- SQL 受控執行個體
- PostgreSQL Hyperscale

如需相關指示，請參閱 [什麼是 Azure Arc 啟用的資料服務？](overview.md)

## <a name="next-steps"></a>後續步驟

> **只想試試看嗎？**  
> 快速開始使用 [Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) 快速入門 AZURE KUBERNETES SERVICE (AKS) 、AWS 彈性 Kubernetes 服務 (EKS) 、Google Cloud Kubernetes 引擎 (GKE) 或 Azure VM。

[安裝用戶端工具](install-client-tools.md)

[建立 Azure Arc 資料控制器](create-data-controller.md) (需要先安裝用戶端工具) 

[在 Azure Arc 上建立 AZURE SQL 受控實例](create-sql-managed-instance.md) (需要先建立 Azure Arc 資料控制器) 

[在 Azure Arc (上建立適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組](create-postgresql-hyperscale-server-group.md) 需要先建立 Azure Arc 資料控制器) 

## <a name="known-limitations-and-issues"></a>已知限制及問題

- SQL 受控實例名稱不能超過13個字元
- 沒有 Azure Arc 資料控制器或資料庫實例的就地升級。
- 已啟用 Arc 的資料服務容器映射未簽署。  您可能需要設定 Kubernetes 節點，以允許提取未簽署的容器映射。  例如，如果您使用 Docker 作為容器執行時間，您可以設定 DOCKER_CONTENT_TRUST = 0 環境變數並重新啟動。  其他容器執行時間具有類似的選項，例如在 [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration)中。
- 無法從 Azure 入口網站建立 Azure Arc 啟用的 SQL 受控實例或于 postgresql 超大規模伺服器群組。
- 現在，如果您使用 NFS，則在建立 Azure Arc 資料控制器之前，您必須先將部署設定檔檔案中的 allowRunAsRoot 設定為 true。
- 僅限 SQL 和于 postgresql 登入驗證。  不支援 Azure Active Directory 或 Active Directory。
- 在 OpenShift 上建立資料控制器需要寬鬆的安全性條件約束。  如需詳細資料，請參閱文件。
- 不支援調整 Postgres 超大規模背景 _工作節點的_ 數目。
- 如果您使用 Azure Kubernetes Service 引擎 (AKS 引擎) Azure Stack Hub 使用 Azure Arc 資料控制器和資料庫實例，則不支援升級至較新的 Kubernetes 版本。 升級 Kubernetes 叢集之前，請先卸載 Azure Arc 資料控制器和所有資料庫實例。
- 預覽不支援 Postgres 11 版引擎的備份/還原。 它只支援 Postgres 12 版的備份/還原。
