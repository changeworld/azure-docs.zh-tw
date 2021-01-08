---
title: 常見問題集 - Azure Synapse Analytics
description: Azure Synapse Analytics 的常見問題集
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 0d833c32000eb91511782184ef4455bc1973a714
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807904"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Azure Synapse Analytics 的常見問題

您將在本指南中找到 Azure Synapse Analytics 最常遇到的問題。

## <a name="general"></a>一般

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>問：如何使用 RBAC 角色來保護我的工作區？

A：Azure Synapse 導入了許多角色及其指派範圍，為您簡化保護工作區的作業。

Synapse RBAC 角色：
* Synapse 管理員
* Synapse SQL 管理員
* Synapse Spark 管理員
* Synapse 參與者 (預覽)
* Synapse 成品發行者 (預覽)
* Synapse 成品使用者 (預覽)
* Synapse 計算操作員 (預覽)
* Synapse 認證使用者 (預覽)

若要保護您的 Synapse 工作區，請將 RBAC 角色指派給這些 RBAC 範圍：
* 工作區
* Spark 集區
* 整合執行階段
* 連結的服務
* 認證

此外，透過專用 SQL 集區，您可以使用您所熟悉和喜愛的所有安全性功能。

### <a name="q-how-do-i-control-cont-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>問：如何持續控制專用的 SQL 集區、無伺服器 SQL 集區，以及無伺服器 Spark 集區？

A：首先，您可以將 Azure Synapse 與 Azure 訂用帳戶層級適用的內建成本分析和成本警示搭配使用。

- 專用 SQL 集區 - 您可以直接查看成本和對成本的控制，因為專用 SQL 集區的大小會由您建立並指定。 您可以進一步控制哪些使用者可使用 Azure RBAC 角色來建立或調整專用 SQL 集區。

- 無伺服器 SQL 集區 - 您有監視和成本管理控制項，可讓您限制每日、每週和每個月的費用。 如需詳細資訊，請參閱[無伺服器 SQL 集區的成本管理](./sql/data-processed.md)。 

- 無伺服器 Spark 集區 - 您可以限制可使用 Synapse RBAC 角色來建立 Spark 集區的人員。  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>問：在正式發行後，Synapse 工作區是否支援中進行物件和細微性的資料夾組織？

A：Synapse 工作區支援使用者定義的資料夾。

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>問：我是否可將多個 Power BI 工作區連結至單一 Azure Synapse 工作區？
    
A：目前，您只能將單一 Power BI 工作區連結至一個 Azure Synapse 工作區。 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>問：Cosmos DB 的 Synapse Link 是否已正式發行？

A：Apache Spark 的 Synapse Link 已正式發行。 無伺服器 SQL 集區的 Synapse Link 處於公開預覽階段。

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>問：Azure Synapse 工作區是否支援 CI/CD？ 

A：是的！ 所有的管線成品、筆記本、SQL 指令碼和 Spark 作業定義都會提供於 Git 中。 所有集區定義都會以 ARM 範本的形式儲存在 Git 中。 專用 SQL 集區物件 (結構描述、資料表、檢視等) 將會以具有 CI/CD 支援的資料庫專案進行管理。

## <a name="pipelines"></a>Pipelines

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>問：我如何確知要使用哪個認證來執行管線？ 

A：Synapse 管線中的每個活動都會使用在連結服務內指定的認證來執行。

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>問：Synapse 整合是否支援 SSIS IR？

 答：目前沒有。 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>問：如何將現有的管線從 Azure Data Factory 遷移至 Azure Synapse 工作區？

A：目前，您必須手動重新建立 Azure Data Factory 管線和相關成品，方法是從原始管線匯出 JSON，並將其匯入至 Synapse 工作區。

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>問：適用於 Synapse 的 Apache Spark 與 Apache Spark 之間有何差異？

A：適用於 Synapse 的 Apache Spark 本質上就是 Apache Spark，但新增了與其他服務 (AAD、AzureML 等等) 進行整合的支援，以及額外的程式庫 (mssparktuils、Hummingbird) 和預先調整的效能設定。

目前在 Apache Spark 上執行的任何工作負載都無須變更，即可在適用於 Azure Synapse 的 Apache Spark 上執行。 

### <a name="q-what-versions-of-spark-are-available"></a>問：Spark 有哪些可用版本？

A：Azure Synapse Apache Spark 可完整支援 Spark 2.4。 如需核心元件和目前支援版本的完整清單，請參閱 [Apache Spark 版本支援](./spark/apache-spark-version-support.md)。

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>問：Azure Synapse Spark 中是否有相當於 Dbutils 的項目？

A：是，Azure Synapse Apache Spark 提供 **mssparkutils** 程式庫。 如需公用程式的完整文件，請參閱 [Microsoft Spark 公用程式簡介](./spark/microsoft-spark-utilities.md)。

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>問：如何在 Apache Spark 中設定工作階段參數？

A：若要設定工作階段參數，請使用 %%configure magic available。 必須重新啟動工作階段，參數才會生效。 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>問：如何在無伺服器 Spark 集區中設定叢集層級參數？

A：若要設定叢集層級參數，您可以為 Spark 集區提供 spark.conf 檔案。 此集區將會接受傳入組態檔中的參數。 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>問：我是否可在 Azure Synapse Analytics 中執行多重使用者 Spark 叢集？
 
A：Azure Synapse 針對特定使用案例提供了特殊用途引擎。 適用於 Synapse 的 Apache Spark 是設計為作業服務，而不是叢集模型。 使用者在兩種情況下會需要多重使用者叢集模型。

**案例 #1：許多使用者存取叢集是為了提供 BI 用途所需的資料。**

要完成這項工作，最簡單的方法是為 Spark 調整資料，然後利用 Synapse SQL 的服務功能，將 Power BI 連線至這些資料集。

**案例 2：讓多個開發人員使用單一叢集以節省成本。**
 
為達到此案例的需求，您應為每個開發人員提供一個無伺服器 Spark 集區，並將其設定為使用少量的 Spark 資源。 由於無伺服器 Spark 集區在受到主動使用之前不會產生任何費用，因此在有多個開發人員的情況下，可將成本降到最低。 集區會共用中繼資料 (Spark 資料表)，而能夠輕易搭配運作使用。

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>問：如何包含、管理和安裝程式庫？

A：您可以在建立 Spark 集區時，從 Synapse 工作區或 Azure 入口網站透過 requirements.txt 檔案安裝外部套件。 請參閱[管理 Azure Synapse Analytics 中的 Apache Spark 程式庫](./spark/apache-spark-azure-portal-add-libraries.md)。

## <a name="dedicated-sql-pools"></a>專用 SQL 集區

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>問：專用 SQL 集區與無伺服器集區的功能有何差異？

A：您可以在 [Synapse SQL 中的 T-SQL 功能差異](./sql/overview-features.md)中找到差異的完整清單。

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>問：Azure Synapse 現已正式發行，如何將先前獨立的專用 SQL 集區移至 Azure Synapse？ 

A：並沒有「移動」或「移轉」功能。 您可以選擇在現有的集區上啟用新的工作區功能。 這麼做將不會有重大變更，而是能夠使用 Synapse Studio、Spark 和無伺服器 SQL 集區等新功能。

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>問：專用 SQL 集區現在的預設部署為何？ 

A：根據預設，所有新的專用 SQL 集區都會部署至工作區；但若您有需要，仍可建立獨立形式的專用 SQL 集區 (先前稱為 SQL DW)。 


### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-sql-pools"></a>問：專用 SQL 集區與無伺服器 SQL 集區的功能有何差異？

A：您可以在 [Synapse SQL 中的 T-SQL 功能差異](./sql/overview-features.md)中找到差異的完整清單。

## <a name="next-steps"></a>後續步驟

* [開始使用 Azure Synapse Analytics](get-started.md)
* [建立工作區](quickstart-create-workspace.md)
* [使用無伺服器 SQL 集區](quickstart-sql-on-demand.md)
