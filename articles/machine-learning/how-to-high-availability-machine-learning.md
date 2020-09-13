---
title: 復原 & 高可用性
titleSuffix: Azure Machine Learning
description: 瞭解如何使用高可用性設定，讓您的 Azure Machine Learning 資源更能獲得中斷的復原能力。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/16/2020
ms.openlocfilehash: abb24ef1cc6a693acbaf6561a185fd5ae536a66d
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89668797"
---
# <a name="increase-azure-machine-learning-resiliency"></a>提高 Azure Machine Learning 的復原能力

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何使用高可用性設定，讓您的 Microsoft Azure Machine Learning 資源更具復原性。 您可以設定 Azure Machine Learning 相依于高可用性的 Azure 服務。 本文會識別您可以設定高可用性的服務，以及有關設定這些資源的其他資訊連結。

> [!NOTE]
> Azure Machine Learning 本身不會提供嚴重損壞修復選項。

## <a name="understand-azure-services-for-azure-machine-learning"></a>瞭解適用于 Azure Machine Learning 的 Azure 服務

Azure Machine Learning 取決於多項 Azure 服務，而且有數個層級。 其中有些服務是在您的 (客戶) 訂用帳戶中布建。 您必須負責這些服務的高可用性設定。 其他服務是在 Microsoft 訂用帳戶中建立，並由 Microsoft 管理。 

Azure 服務包括：

* **Azure Machine Learning 基礎結構**：適用于 Azure Machine Learning 工作區的 Microsoft 管理環境。

* **相關聯的資源**： Azure Machine Learning 工作區建立期間布建在您訂用帳戶中的資源。 這些資源包括 Azure 儲存體、Azure Key Vault、Azure Container Registry 和 Application Insights。 您必須負責設定這些資源的高可用性設定。
  * 預設儲存體有資料，例如模型、定型記錄資料和資料集。
  * Key Vault 具有 Azure 儲存體、Container Registry 和資料存放區的認證。
  * Container Registry 具有用於定型和推斷環境的 Docker 映射。
  * Application Insights 用於監視 Azure Machine Learning。

* **計算資源**：您在工作區部署之後建立的資源。 例如，您可以建立計算實例或計算叢集來訓練 Machine Learning 模型。
  * 計算實例和計算叢集： Microsoft 管理的模型開發環境。
  * 其他資源：您可以附加至 Azure Machine Learning 的 Microsoft 計算資源，例如 Azure Kubernetes Service (AKS) 、Azure Databricks、Azure 容器實例和 Azure HDInsight。 您必須負責設定這些資源的高可用性設定。

* **其他資料存放區**： Azure Machine Learning 可以掛接額外的資料存放區，例如用於定型資料的 Azure 儲存體、Azure Data Lake Storage 和 Azure SQL Database。  這些資料存放區會布建在您的訂用帳戶內。 您要負責設定其高可用性設定。

下表顯示由 Microsoft 管理的 Azure 服務（由您管理），以及預設為高可用性的 Azure 服務。

| 服務 | 管理者 | 預設為高可用性 |
| ----- | ----- | ----- |
| **Azure Machine Learning 基礎結構** | Microsoft | |
| **相關聯的資源** |
| Azure 儲存體 | 您 | |
| Key Vault | 您 | ✓ |
| Container Registry | 您 | |
| Application Insights | 您 | NA |
| **計算資源** |
| 計算執行個體 | Microsoft |  |
| 計算叢集 | Microsoft |  |
| 其他計算資源，例如 AKS、 <br>Azure Databricks，容器實例，HDInsight | 您 |  |
| **其他資料存放區** ，例如 Azure 儲存體、SQL Database、<br> 適用於 PostgreSQL 的 Azure 資料庫、適用於 MySQL 的 Azure 資料庫、 <br>Azure Databricks 檔案系統 | 您 | |

本文的其餘部分將說明讓這些服務的每一項都具有高度可用性所需採取的動作。

## <a name="associated-resources"></a>相關聯的資源

> [!IMPORTANT]
> Azure Machine Learning 不支援使用異地複寫儲存體的預設儲存體帳戶容錯移轉 (GRS) 、異地區域冗余儲存體 (GZRS) 、讀取權限異地複寫儲存體 (GRS) ，或讀取權限地理區域冗余儲存體 (GZRS) 。

請務必參考下列檔，以設定每個資源的高可用性設定：

* **Azure 儲存體**：若要設定高可用性設定，請參閱 [Azure 儲存體冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。
* **Key Vault**： Key Vault 預設會提供高可用性，且不需要使用者動作。  請參閱 [Azure Key Vault 可用性和冗余](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)。
* **Container registry**：選擇用於異地複寫的 Premium 登錄選項。 請參閱 [Azure Container Registry 中的異地](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)複寫。
* **Application Insights**： Application Insights 不提供高可用性設定。 若要調整資料保留期限和詳細資料，請參閱 [Application Insights 中的資料收集、保留和儲存](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept)。

## <a name="compute-resources"></a>計算資源

請務必參考下列檔，以設定每個資源的高可用性設定：

* **Azure Kubernetes Service**：請參閱 [Azure Kubernetes Service (AKS 中的商務持續性和嚴重損壞修復的最佳作法) ](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) 並 [建立使用可用性區域的 Azure Kubernetes Service (AKS) ](https://docs.microsoft.com/azure/aks/availability-zones)叢集。 如果使用 Azure Machine Learning Studio、SDK 或 CLI 來建立 AKS 叢集，則不支援跨區域高可用性。
* **Azure Databricks**：請參閱 [Azure Databricks 叢集的區域](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)嚴重損壞修復。
* **容器實例**：協調器負責進行容錯移轉。 請參閱 [Azure 容器實例和容器協調器](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship)。
* **HDInsight**：請參閱 [Azure HDInsight 所支援的高可用性服務](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components)。

## <a name="additional-data-stores"></a>其他資料存放區

請務必參考下列檔，以設定每個資源的高可用性設定：

* **Azure Blob 容器/Azure 檔案儲存體/Data Lake Storage Gen2**：與預設儲存體相同。
* **Data Lake Storage Gen1**：請參閱 [Data Lake Storage Gen1 的高可用性和嚴重損壞修復指南](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance)。
* **SQL Database**：查看 [Azure SQL Database 和 SQL 受控執行個體的高可用性](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability)。
* **適用於 PostgreSQL 的 Azure 資料庫**： [適用於 PostgreSQL 的 Azure 資料庫單一伺服器中查看高可用性概念](https://docs.microsoft.com/azure/postgresql/concepts-high-availability)。
* **適用於 MySQL 的 Azure 資料庫**：請參閱 [瞭解適用於 MySQL 的 Azure 資料庫中的商務持續性](https://docs.microsoft.com/azure/mysql/concepts-business-continuity)。
* **Azure Databricks 檔案系統**：請參閱 [Azure Databricks 叢集的區域](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)嚴重損壞修復。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

如果您提供自己的客戶管理金鑰來部署 Azure Machine Learning 工作區，則也會在您的訂用帳戶中布建 Azure Cosmos DB。 在此情況下，您要負責設定其高可用性設定。 請參閱 [Azure Cosmos DB 的高可用性](https://docs.microsoft.com/azure/cosmos-db/high-availability)。

## <a name="next-steps"></a>接下來的步驟

若要使用與高可用性設定相關聯的資源來部署 Azure Machine Learning，請使用 [Azure Resource Manager 的範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)。
