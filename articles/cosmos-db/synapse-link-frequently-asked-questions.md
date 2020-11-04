---
title: 關於適用於 Azure Cosmos DB 的 Azure Synapse Link 常見問題
description: 取得有關適用於 Azure Cosmos DB 的 Synapse Link 各方面常見問題的解答，例如計費、分析存放區、安全性、分析存放區上的存留時間。
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 0791ed6882feedeab47b75eff6a69bf0a49ab7ee
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341281"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>關於適用於 Azure Cosmos DB 的 Azure Synapse Link 常見問題
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

適用於 Azure Cosmos DB 的 Azure Synapse Link 會在 Azure Cosmos DB 與 Azure Synapse Analytics 之間建立緊密的整合。 可讓客戶對其操作資料執行近即時分析，並將分析與交易的工作負載完全隔離，且不需要 ETL (擷取、轉換和下載) 管線。 本文會回答有關 Azure Cosmos DB 的 Synapse Link 常見問題。

## <a name="general-faq"></a>一般常見問題

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>是否支援所有 Azure Cosmos DB Api 的 Azure Synapse Link？

在公開預覽版本中，Azure Cosmos DB SQL (Core) API 和適用于 MongoDB 的 Azure Cosmos DB API 都支援 Azure Synapse 連結。 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>是否支援多區域 Azure Cosmos DB 帳戶的 Azure Synapse Link？

是，在多區域 Azure Cosmos 帳戶中，儲存在分析存放區中的資料也會全域散發。 不論是單一寫入區域或多個寫入區域，從 Azure Synapse Analytics 執行的分析查詢都可以從最接近的本地區域提供服務。

當您規劃設定多區域 Azure Cosmos DB 具有分析存放區支援的帳戶時，建議您在建立帳戶時新增所有必要的區域。

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>我可以選擇只針對特定區域啟用 Azure Synapse 連結，而不是在設定多區域帳戶中的所有區域嗎？

在預覽版本中，針對多區域帳戶啟用 Azure Synapse 連結時，會在所有區域中建立分析存放區。 基礎資料已針對交易存放區中的輸送量和交易一致性進行最佳化。

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Azure Synapse Link 啟用的帳戶是否支援備份和還原？

針對已開啟分析存放區的容器，目前不支援在分析存放區中自動備份及還原您的資料。 

在資料庫帳戶上啟用 Synapse 連結時，Azure Cosmos DB 將繼續在交易式存放區中自動 [備份](./online-backup-and-restore.md) 您的資料， (只依排程備份間隔的容器) ，一律如此。 請務必注意，當已開啟分析存放區的容器還原至新的帳戶時，只會使用交易式存放區還原容器，而不會啟用任何分析存放區。 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>我可以停用 Azure Cosmos DB 帳戶的 Azure Synapse Link 功能嗎？

目前，在帳戶層級啟用 Synapse Link 功能之後，您就無法將其停用。 請了解，如果已在帳戶層級啟用 Synapse Link 功能，且分析存放區沒有啟用容器，您就不會有任何隱含計費。 

如果您需要關閉此功能，有 2 個選項可供使用。 第一個是刪除並重新建立新的 Azure Cosmos DB 帳戶，並視需要遷移資料。 第二個是開啟支援票證，以取得將資料遷移至另一個帳戶的說明。

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 分析存放區

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>我可以在現有的容器上啟用分析存放區嗎？

目前，只能為 (新的和現有的帳戶中) 新的容器啟用分析存放區。

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>在建立容器期間啟用分析存放區之後，我是否可以停用其 Azure Cosmos DB 容器上的分析存放區？

目前，在 Azure Cosmos DB 容器的建立期間啟用分析存放區後，便無法在容器上停用。

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>支援自動調整布建輸送量的 Azure Cosmos DB 容器是否支援分析存放區？

是，您可以在具有自動調整佈建輸送量功能的容器上啟用分析存放區。

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>對 Azure Cosmos DB 交易存放區佈建的 RU 是否會有任何影響？

Azure Cosmos DB 保證會將交易和分析的工作負載效能隔離。 在容器上啟用分析存放區，並不會影響 Azure Cosmos DB 交易存放區上佈建的 RU/秒。 分析存放區的交易 (讀取和寫入) 和儲存成本將分開計費。 如需詳細資訊，請參閱 [Azure Cosmos DB 分析存放區的定價](analytical-store-introduction.md#analytical-store-pricing)。

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>交易存放區上的刪除和更新作業會反映在分析存放區中嗎？

是，交易存放區中的資料刪除和更新會反映在分析存放區中。 您可以在容器上設定存留時間 (TTL) 以包含歷程記錄資料，讓分析存放區保留符合分析 TTL 準則的所有項目版本。 如需詳細資訊，請參閱[分析 TTL 概觀](analytical-store-introduction.md#analytical-ttl)。

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>我可以從 Azure Synapse Analytics 以外的分析引擎連線到分析存放區嗎？

您只能使用 Azure Synapse Analytics 提供的各種執行階段來存取分析存放區，及對其執行查詢。 您可以使用下列各項對分析存放區執行查詢和分析：

* 完全支援 Scala、Python、SparkSQL、C# 的 Synapse Spark。 Synapse Spark 是資料工程和科學案例的核心
* SQL 無伺服器搭配 T-SQL 語言，並支援熟悉的 BI 工具 (例如 Power BI Premium 等)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>我可以從已佈建的 Synapse SQL 連線到分析存放區嗎？

目前無法從佈建的 Synapse SQL 存取分析存放區。

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>我可以將 Synapse 的查詢彙總結果寫回分析存放區嗎？

分析存放區是 Azure Cosmos DB 容器中的唯讀存放區。 因此，您無法將彙總結果直接回寫分析存放區，但可以將其寫入另一個容器的 Azure Cosmos DB 交易存放區，之後就可以加以利用做為服務層。

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>從交易存放區到分析存放區的自動同步複寫是非同步或同步，其延遲為何？

自動同步處理延遲通常是在2分鐘內。 如果有大量容器的共用輸送量資料庫，則個別容器的自動同步延遲可能會較高，且最多需要5分鐘的時間。 我們想要深入瞭解此延遲如何符合您的案例。 若要這樣做，請與 [Azure Cosmos DB 團隊](mailto:cosmosdbsynapselink@microsoft.com)聯繫。

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>有沒有任何情況，交易存放區中的項目不會自動傳播到分析存放區嗎？

如果容器中的特定項目違反了[妥善定義的分析結構描述](analytical-store-introduction.md#analytical-schema)，就不會被放入分析存放區。 如果您有被妥善定義的分析結構描述封鎖的案例，請傳送電子郵件至 [Azure Cosmos DB 小組](mailto:cosmosdbsynapselink@microsoft.com)。

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>我可以將分析存放區中的資料分割的和交易存放區不同嗎？

分析存放區中的資料會根據交易存放區中分區的水平資料分割進行分割。 目前，您無法為分析存放區選擇不同的資料分割策略。

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>我可以在分析存放區中，自訂或覆寫交易資料轉換成單欄式格式的方式嗎？

目前您無法在資料項目從交易存放區自動傳播到分析存放區時，轉換這些資料項目。 如果您有被此限制所封鎖的案例，請傳送電子郵件至 [Azure Cosmos DB 小組](mailto:cosmosdbsynapselink@microsoft.com)。

### <a name="is-analytical-store-supported-by-terraform"></a>Terraform 支援分析存放區嗎？

目前 Terraform 不支援分析存放區容器。 如需詳細資訊，請參閱 [Terraform GitHub 問題](https://github.com/hashicorp/terraform/issues)。

## <a name="analytical-time-to-live-ttl"></a>分析存留時間 (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>是否在容器和項目層級皆支援分析資料的 TTL？

現階段，只能在容器層級設定分析資料的 TTL，不支援在項目層級設定分析 TTL。

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>在 Azure Cosmos DB 容器上設定容器層級的分析 TTL 之後，可以在之後變更為其他的值嗎？

是，分析 TTL 可以更新為任何有效的值。 如需分析 TTL 的詳細資訊，請參閱[分析 TTL](analytical-store-introduction.md#analytical-ttl) 一文。

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>在交易存放區中的項目過了 TTL 之後，我可以在分析存放區中更新或刪除該項目嗎？

所有的交易更新和刪除都會複製到分析存放區，但如果已從交易存放區中清除項目，就無法在分析存放區中進行更新。 若要深入瞭解，請參閱[分析 TTL](analytical-store-introduction.md#analytical-ttl) 一文。

## <a name="billing"></a>計費

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>適用于 Azure Cosmos DB 的 Azure Synapse 連結的計費模型為何？

[Azure Cosmos DB 分析存放區](analytical-store-introduction.md)會在公開預覽中提供，直到 2020 年 8 月 30 日為止，分析存放區不會有任何費用。 Synapse Spark 和 Synapse SQL 會透過 [Synapse 服務耗用量](https://azure.microsoft.com/pricing/details/synapse-analytics/)計費。

## <a name="security"></a>安全性

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>有哪些方式能向分析存放區驗證？

分析存放區的驗證與交易存放區相同。 針對指定的資料庫，您可以使用主要或唯讀金鑰進行驗證。 您可以利用 Azure Synapse Studio 中的連結服務，避免將 Azure Cosmos DB 金鑰貼入 Spark 筆記本。 具有工作區存取權的每個人都可以存取此連結服務。

## <a name="synapse-run-times"></a>Synapse 執行階段

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>目前支援使用哪些 Synapse 執行階段來存取 Azure Cosmos DB 分析存放區？

|Azure Synapse 執行時間 |目前的支援 |
|---------|---------|
|Azure Synapse Spark 集區 | 讀取、寫入 (透過交易存放區)、資料表、暫存檢視 |
|Azure Synapse SQL 無伺服器集區    | 讀取、觀看 |
|已布建 Azure Synapse SQL   |  無法使用 |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>我的 Azure Synapse Spark 資料表與我的 Azure Synapse SQL 無伺服器資料表進行同步處理時，與 Azure Data Lake 的方式相同？

目前沒有此功能。

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>我可以從分析存放區進行 Spark 結構化串流嗎？

目前，是使用交易存放區的變更摘要功能來執行 Azure Cosmos DB 的 Spark 結構化串流支援，而分析存放區尚未支援此功能。

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>在 Azure Synapse Studio 中，如何辨識是否已連線到已啟用分析存放區的 Azure Cosmos DB 容器？

已啟用分析存放區的 Azure Cosmos DB 容器會有下列圖示：

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="啟用分析存放區的 Azure Cosmos DB 容器圖示":::

交易存放區容器將會以下列圖示表示：

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="啟用交易存放區的 Azure Cosmos DB 容器圖示":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>如何從 Azure Synapse Studio 傳遞 Azure Cosmos DB 認證？

目前 Azure Cosmos DB 認證是在建立連結服務時由具有 Azure Cosmos DB 資料庫存取權的使用者傳遞。 其他能存取工作區的使用者也會具有該工作區的存取權。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Azure Synapse 連結的優點](synapse-link.md#synapse-link-benefits)

* 深入瞭解 [Azure Synapse 連結與 Azure Cosmos DB 之間的整合](synapse-link.md#synapse-link-integration)。