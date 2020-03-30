---
title: Azure 宇宙 DB 的高級威脅保護
description: 瞭解 Azure Cosmos DB 如何提供靜態資料加密，以及如何實現資料。
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614840"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Azure 宇宙 DB 的高級威脅保護（預覽版）

Azure Cosmos DB 的高級威脅保護提供了一層額外的安全智慧，用於檢測訪問或利用 Azure Cosmos DB 帳戶的異常和潛在有害嘗試。 此保護層允許您處理威脅，即使無需成為安全專家，也將其與中央安全監控系統集成。

當活動異常發生時，將觸發安全警報。 這些安全警報與 Azure[安全中心](https://azure.microsoft.com/services/security-center/)集成，並且也通過電子郵件發送給訂閱管理員，其中詳細介紹了可疑活動，並就如何調查和修復威脅提出了建議。

> [!NOTE]
>
> * Azure Cosmos DB 的高級威脅保護當前僅適用于 SQL API。
> * Azure Cosmos DB 的高級威脅保護當前不在 Azure 政府和主權雲區域中。

為了全面瞭解安全警報，我們建議[在 Azure Cosmos DB 中啟用診斷日誌記錄](https://docs.microsoft.com/azure/cosmos-db/logging)，該 DB 會記錄資料庫本身的操作，包括對所有文檔、容器和資料庫的 CRUD 操作。

## <a name="threat-types"></a>威脅類型

Azure Cosmos DB 的高級威脅保護可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。 它當前可以觸發以下警報：

- **從異常位置進行訪問**：當 Azure Cosmos 帳戶的訪問模式發生更改時觸發此警報，其中有人從異常地理位置連接到 Azure Cosmos DB 終結點。 在某些情況下，警報會檢測到合法操作，這意味著新應用程式或開發人員的維護操作。 在其他情況下，警報會檢測來自前員工、外部攻擊者等的惡意操作。

- **異常資料提取**：當用戶端從 Azure Cosmos DB 帳戶中提取異常數量的資料時，將觸發此警報。 這可能是為將帳戶中存儲的所有資料傳輸到外部資料存儲而執行的某些資料外泄的症狀。

## <a name="set-up-advanced-threat-protection"></a>設置高級威脅防護

### <a name="set-up-atp-using-the-portal"></a>使用門戶設置 ATP

1. 在 啟動 Azure[https://portal.azure.com](https://portal.azure.com/)門戶。

2. 從 Azure Cosmos DB 帳戶中，從 **"設置"** 功能表中選擇 **"高級安全性**"。

    ![設置 ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. 在**高級安全**配置邊欄選項卡中：

    * 按一下 **"高級威脅防護"** 選項將其設置為**ON**。
    * 按一下 [儲存]****，以儲存新的或更新的進階威脅防護原則。   

### <a name="set-up-atp-using-rest-api"></a>使用 REST API 設置 ATP

使用 Rest API 命令為特定的 Azure Cosmos DB 帳戶創建、更新或獲取高級威脅保護設置。

* [高級威脅防護 - 創建](https://go.microsoft.com/fwlink/?linkid=2099745)
* [高級威脅防護 - 獲取](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>使用 Azure PowerShell 設置 ATP

使用以下 PowerShell Cmdlet：

* [啟用進階威脅防護](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [獲得高級威脅防護](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [禁用高級威脅防護](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本

使用 Azure 資源管理器範本在啟用了高級威脅保護後設置 Cosmos DB。
有關詳細資訊，請參閱[創建具有高級威脅保護的 CosmosDB 帳戶](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)。

### <a name="using-azure-policy"></a>使用 Azure 策略

使用 Azure 策略為 Cosmos DB 啟用高級威脅保護。

1. 啟動 Azure**策略 - 定義**頁，並搜索**部署 Cosmos DB 策略的高級威脅保護**。

    ![搜索策略](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. 按一下"**部署 CosmosDB 策略的高級威脅保護**"，然後按一下"**分配**"。

    ![選擇訂閱或組](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. 在 **"範圍"** 欄位中，按一下三個點，選擇 Azure 訂閱或資源組，然後按一下"**選擇**"。

    ![策略定義頁面](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. 輸入其他參數，然後按一下 **"分配**"。

## <a name="manage-atp-security-alerts"></a>管理 ATP 安全警報

當 Azure Cosmos DB 活動異常發生時，將觸發安全警報，並包含有關可疑安全事件的資訊。 

 從 Azure 安全中心，您可以查看和管理當前[安全警報](../security-center/security-center-alerts-overview.md)。  按一下[安全中心](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0)中的特定警報，查看可能的原因和建議的操作，以調查和緩解潛在威脅。 下圖顯示了安全中心提供的警報詳細資訊示例。

 ![威脅詳細資訊](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

還會發送電子郵件通知，並包含警報詳細資訊和建議的操作。 下圖顯示了警報電子郵件的示例。

 ![警示詳細資料](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>宇宙 DB ATP 警報

 要查看監視 Azure Cosmos DB 帳戶時生成的警報清單，請參閱 Azure 安全中心文檔中的[Cosmos DB 警報](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos)部分。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[Azure 宇宙 DB 中的診斷日誌記錄](cosmosdb-monitor-resource-logs.md)的更多資訊
* 瞭解有關[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
