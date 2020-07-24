---
title: 如何提高復原能力
titleSuffix: Azure Machine Learning
description: 瞭解如何使用高可用性設定，讓您的 Azure Machine Learning 相關資源更有彈性地復原中斷。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096461"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>增加 Azure Machine Learning 的復原能力

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何使用高可用性設定，讓您的 Azure Machine Learning 相關資源更具彈性。 Azure Machine Learning 相依的 Azure 服務可設定為高可用性。 本文提供可針對高可用性設定哪些服務的相關資訊，以及有關設定這些資源之資訊的連結。

> [!NOTE]
> Azure Machine Learning 本身並不提供嚴重損壞修復選項。

## <a name="understand-azure-services-for-azure-machine-learning"></a>瞭解適用于 Azure Machine Learning 的 Azure 服務

Azure Machine Learning 取決於多個 Azure 服務，並有數個層級。 其中一些會在您的（客戶）訂用帳戶中布建。 您必須負責這些服務的高可用性設定。 有些是在 Microsoft 訂用帳戶中建立，並由 Microsoft 管理。

* **Azure Machine Learning 基礎結構**：適用于 Azure Machine Learning 工作區的 Microsoft 管理環境。

* **相關聯的資源**： Azure Machine Learning 工作區建立期間布建在您訂用帳戶中的資源。 其中包括 Azure 儲存體、Azure Key Vault、Azure Container Registry （ACR）和 App Insights。 您必須負責這些資源的高可用性設定。
  * 預設儲存體具有模型、定型記錄資料和資料集之類的資料。
  * Key Vault 具有儲存體、ACR、資料存放區的認證。
  * ACR 具有用於定型和推斷環境的 docker 映射。
  * App Insights 適用于監視 Azure Machine Learning。

* **計算資源**：您在工作區部署後建立的資源。 例如，您可以建立計算實例或計算叢集，以將機器學習模型定型。
  * 計算實例和計算叢集： Microsoft 管理的模型開發環境。
  * 其他資源：這些是運算資源可以附加至 Azure Machine Learning，例如 Azure Kubernetes Service （AKS）、Azure Databricks、Azure 容器實例（ACI）和 HDInsight。 您必須負責高可用性設定。

* **其他資料存放區**： Azure Machine Learning 可以掛接額外的資料存放區，例如用於定型資料的 Azure 儲存體、Azure Data Lake Storage 和 Azure SQL Database。  它們是在您的訂用帳戶內，而且您必須負責高可用性設定。

下表顯示由 Microsoft 管理的服務，這些服務由您管理，且預設為高可用性：

| 服務 | 管理者 | 預設為 HA |
| ----- | ----- | ----- |
| **Azure Machine Learning 基礎結構** | Microsoft | |
| **相關聯的資源** |
| Azure 儲存體 | 您 | |
| Azure 金鑰保存庫 | 您 | ✓ |
| Azure Container Registry | 您 | |
| Application Insights | 您 | NA |
| **計算資源** |
| 計算實例 | Microsoft |  |
| 計算叢集 | Microsoft |  |
| 其他資源，例如 Azure Kubernetes Service、 <br>Azure Databricks、Azure 容器實例 Azure HDInsight | 您 |  |
| **其他資料存放區**，例如 Azure 儲存體、Azure SQL Database、<br> 適用於 PostgreSQL 的 Azure 資料庫、適用於 MySQL 的 Azure 資料庫、 <br>Azure Databricks 檔案系統 | 您 | |

使用本檔其餘部分中的資訊，來瞭解讓每個服務都具有高度可用性所需採取的動作。

## <a name="associated-resources"></a>相關聯的資源

> [!IMPORTANT]
> Azure Machine Learning 不支援使用異地多餘儲存體（GRS）或異地區域冗余儲存體（切換）或讀取權限異地多餘儲存體（RA-GRS）或讀取權限異地區域冗余儲存體（RA-切換）的預設儲存體帳戶容錯移轉。

請使用下列資訊來確定每個資源的高可用性設定。

* **Azure 儲存體**：若要設定高可用性設定，請參閱[Azure 儲存體冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。
* **Azure Key Vault**：它會提供預設的高可用性服務，而且不需要任何使用者動作。  請參閱[Azure Key Vault 可用性和冗余](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)。
* **Azure Container Registry**：選擇 [適用于異地複寫的 Premium SKU]。 請參閱[Azure Container Registry 中的異地](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)複寫。
* **Application Insights**：不提供高可用性設定。 您可以[在 Application Insights 中，調整資料收集、保留和儲存區](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept)中的資料保留週期和詳細資料。

## <a name="compute-resources"></a>計算資源

請使用下列檔來確定每個資源的高可用性設定。

* **Azure Kubernetes Service**：請參閱[Azure Kubernetes Service （AKS）中商務持續性和嚴重損壞修復的最佳做法](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)，並[建立使用可用性區域的 Azure Kubernetes Service （AKS）](https://docs.microsoft.com/azure/aks/availability-zones)叢集。 如果 AKS 叢集是由 Azure Machine Learning （使用 studio、SDK 或 ML CLI）所建立，則不支援跨區域的高可用性。
* **Azure Databricks**：請參閱[Azure Databricks 叢集的區域性損毀修復](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)。
* **Azure 容器實例**： ACI 協調器負責容錯移轉。 請參閱[Azure 容器實例和容器協調器](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship)。
* **Azure HDInsight**：請參閱[Azure HDInsight 支援的高可用性服務](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components)。

## <a name="additional-data-stores"></a>其他資料存放區

請使用下列檔來確定每個資源的高可用性設定。

* **Azure Blob 容器/azure 檔案共用/Azure Data Lake Gen2**：與預設儲存體相同。
* **Azure Data Lake Gen1**：請參閱[Azure Data Lake Storage Gen1 中資料](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance)的嚴重損壞修復指引。
* **Azure SQL Database**：請參閱[高可用性和 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability)。
* **適用於 PostgreSQL 的 Azure 資料庫**：請參閱[適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的高可用性概念](https://docs.microsoft.com/azure/postgresql/concepts-high-availability)。
* **適用於 MySQL 的 Azure 資料庫**：請參閱[瞭解適用於 MySQL 的 Azure 資料庫中的商務持續性](https://docs.microsoft.com/azure/mysql/concepts-business-continuity)。
* **Databricks 檔案系統**：請參閱[Azure Databricks 叢集的區域性](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)嚴重損壞修復。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

如果您提供自己的金鑰（由客戶管理的金鑰）來部署 Azure Machine Learning 工作區，Cosmos DB 也會布建在您的訂用帳戶內。 在此情況下，您必須負責其高可用性。 查看[Azure Cosmos DB 的高可用性](https://docs.microsoft.com/azure/cosmos-db/high-availability)

## <a name="next-steps"></a>後續步驟

若要使用您的高可用性設定來部署 Azure Machine Learning 與相關聯的資源，請使用[Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)。