---
title: 什麼是已啟用 Azure Arc 的資料服務
description: 已啟用 Azure Arc 的資料服務簡介
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: 47fef490c5ece577823a14e3fa4c415f0f613ccb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943881"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>什麼是已啟用 Azure Arc 的資料服務 (預覽)？

無論是在內部部署、邊緣和公用雲端中，Azure Arc 都可讓您在所選基礎結構上使用 Kubernetes 執行 Azure 資料服務。

目前，下列已啟用 Azure Arc 的資料服務可供預覽：

- SQL 受控執行個體
- PostgreSQL Hyperscale

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>永遠保持最新版本

啟用 Azure Arc 的資料服務 (例如啟用 Azure Arc 的 SQL 受控執行個體和啟用 Azure Arc 的 PostgreSQL 超大規模資料庫) 會頻繁地接收更新，包括服務修補檔和新功能，與您在 Azure 中的體驗類似。 系統會提供 Microsoft Container Registry 的更新，並根據您的原則設定部署步調。 如此一來，內部部署資料庫可以保持最新狀態，同時確保您保有控制權。 因為啟用 Azure Arc 的資料服務是訂用帳戶服務，因此您不會再遇到終止資料庫支援的情況。

## <a name="elastic-scale"></a>彈性擴縮

內部部署的類似雲端彈性可讓您根據基礎結構的可用容量，以動態方式擴大或縮減資料庫，就像在 Azure 中一樣。 這項功能可以滿足具有變動性需求的高載案例，包括需要即時以任何規模內嵌及查詢資料的案例，並提供不到一秒的回應時間。 此外，您也可以使用適用於 PostgreSQL 的超大規模 Azure 資料庫的唯一超大規模部署選項來擴增資料庫執行個體。 這項功能可使用獨特的「擴增」讀取和寫入，讓資料工作負載有額外空間可提升產能。

## <a name="self-service-provisioning"></a>自助服務佈建

Azure Arc 也提供其他雲端優勢，例如大規模的快速部署和自動化。 由於採用以 Kubernetes 為基礎的協調流程，您可以使用 GUI 或 CLI 工具，在數秒內部署資料庫。

## <a name="unified-management"></a>整合式管理

您現在可以使用熟悉的工具 (例如 Azure 入口網站、Azure Data Studio 和 Azure 資料 CLI)，更進一步了解以 Azure Arc 部署的所有資料資產。您不僅可以跨環境和 Azure 檢視和管理各種關聯式資料庫，還能從 Kubernetes API 取得記錄和遙測資料，以分析基礎結構的容量和健康情況。 除了擁有當地語系化的記錄分析和效能監視，您現在還可以利用 Azure 監視器，取得整個資產的完整營運見解。

## <a name="disconnected-scenario-support"></a>中斷案例支援

許多服務 (例如自助佈建、自動備份/還原和監視) 都可以在您的基礎結構中於本機執行，無論是否直接連線到 Azure。 直接連線到 Azure 會開啟與其他 Azure 服務 (例如 Azure 監視器) 整合的其他選項，以及可從全球各地使用 Azure 入口網站和 Azure Resource Manager API 管理啟用 Azure Arc 的資料服務功能。

## <a name="next-steps"></a>後續步驟

> **只想試試看嗎？**  
> 在 Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中快速開始使用 [Azure Arc 快速入門](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)。

[安裝用戶端工具](install-client-tools.md)

[建立 Azure Arc 資料控制器](create-data-controller.md) (需要先安裝用戶端工具)

[在 Azure Arc 上建立 Azure SQL 受控執行個體](create-sql-managed-instance.md) (需要先建立 Azure Arc 資料控制項)

[在 Azure Arc 上建立適用於 PostgreSQL 的超大規模 Azure 資料庫伺服器群組](create-postgresql-hyperscale-server-group.md) (必須先建立 Azure Arc 資料控制項)
