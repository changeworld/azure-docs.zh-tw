---
title: Azure Cosmos DB 的進階威脅防護
description: 了解 Azure Cosmos DB 如何提供加密待用資料，以及其實作方式。
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: d8398dbded8753bac193f973026fb63d5f1fc6b3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097714"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB 的進階威脅防護 (預覽)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 提供「進階威脅防護」來增加額外的安全智慧層級，用於偵測 Azure Cosmos DB 帳戶中異常且可能有害的存取意圖或攻擊。 這個防護層讓您不需是安全性專家就能解決威脅，並且可使用中央安全性監視系統整合威脅。

出現異常活動時，就會觸發安全性警示。 這些安全性警示會與 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)整合，也會透過電子郵件將可疑活動的詳細資料，以及如何調查和修復威脅的建議傳送給訂閱系統管理員。

> [!NOTE]
>
> * Azure Cosmos DB 的進階威脅防護目前僅適用於 SQL API。
> * Azure Cosmos DB 進階威脅防護目前不適用於 Azure 政府和主權雲端區域。

如需安全性警示的完整調查體驗，建議啟用 [Azure Cosmos DB 中的診斷記錄](./monitor-cosmos-db.md)，這會記錄資料庫本身的作業，包含所有文件、容器和資料庫上的 CRUD 作業。

## <a name="threat-types"></a>威脅類型

Azure Cosmos DB 進階威脅防護會偵測異常活動，這些活動表示有不尋常及可能有害的活動試圖存取或惡意探索資料庫。 這些活動目前會觸發下列警示：

- **從不尋常的位置存取** ：有人從不尋常的地理位置登入 Azure Cosmos DB 端點，而使 Azure Cosmos 帳戶的存取模式有所變更時，會觸發此警示。 在某些情況下，警示會偵測到合法的動作，代表新的應用程式或開發人員維護作業。 在其他情況下，警示則會偵測惡意動作來 (離職員工、外部攻擊者等)。

- **不尋常的資料擷取** ：當用戶端從 Azure Cosmos DB 帳戶擷取不尋常的資料量時，會觸發此警示。 這可能是部分資料外洩的徵兆，藉此將帳戶中所有儲存資料傳輸到外部資料存放區。



## <a name="configure-advanced-threat-protection"></a>設定進階威脅防護

您可以透過下列各節所述的任一種方式設定進階威脅防護。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在 [https://portal.azure.com](https://portal.azure.com/) 上啟動 Azure 入口網站。

2. 從 [Azure Cosmos DB] 帳戶的 [設定] 功能表中，選取 [進階安全性]。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="設定 ATP":::

3. 在 [進階安全性] 設定刀鋒視窗：

    * 按一下 [進階威脅防護] 選項，將其設定為 [開啟]。
    * 按一下 [儲存]，以儲存新的或更新的進階威脅防護原則。   

### <a name="rest-api"></a>[REST API](#tab/rest-api)

使用 Rest API 命令以建立、更新或取得特定 Azure Cosmos DB 帳戶的「進階威脅防護」設定。

* [進階威脅防護-建立](/rest/api/securitycenter/advancedthreatprotection/create) (機器翻譯)
* [進階威脅防護-取得](/rest/api/securitycenter/advancedthreatprotection/get) (機器翻譯)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用下列 PowerShell Cmdlet：

* [啟用進階威脅防護](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0) (英文)
* [取得進階威脅防護](/powershell/module/az.security/get-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0) (英文)
* [停用進階威脅防護](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0) (英文)

### <a name="arm-template"></a>[ARM 範本](#tab/arm-template)

使用 Azure Resource Manager (ARM) 範本來設定已啟用的 Cosmos DB 與進階威脅防護。
如需詳細資訊，請參閱[建立具有進階威脅防護的 CosmosDB 帳戶](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/) (英文)。

### <a name="azure-policy"></a>[Azure 原則](#tab/azure-policy)

使用 Azure 原則，為 Cosmos DB 啟用進階威脅防護。

1. 啟動 [Azure 原則-定義] 頁面，然後搜尋 [部署 Cosmos DB 的進階威脅防護] 原則。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="設定 ATP"::: 

1. 按一下 [為 CosmosDB 部署進階威脅防護] 原則，然後按一下 [指派]。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="設定 ATP":::


1. 從 [範圍] 欄位中，按一下三個點，選取 Azure 訂閱或資源群組，然後按一下 [選取] 。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="設定 ATP":::


1. 輸入其他參數，然後按一下 [指派]。




## <a name="manage-atp-security-alerts"></a>管理 ATP 安全性警示

當 Azure Cosmos DB 出現異常活動時會觸發安全性警示，並提供可疑安全性事件的相關資訊。 

 您可以從 Azure 資訊安全中心的[安全性警示](../security-center/security-center-alerts-overview.md)，檢閱與管理目前的安全性警示。  按一下 [[資訊安全中心]](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0)的特定警示，檢視可能原因和建議動作的詳細資料，以協助您調查並減輕潛在威脅。 下圖顯示資訊安全中心所提供的警示詳細資料範例。

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="設定 ATP":::

系統也會傳送包含警示詳細資料和建議動作的電子郵件通知。 下圖顯示警示電子郵件的範例。

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="設定 ATP":::

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP 警示

 若要查看監視 Azure Cosmos DB 帳戶時所產生的警示清單，請參閱 Azure 資訊安全中心文件中的 [Cosmos DB 警示](../security-center/alerts-reference.md#alerts-azurecosmos)一節 (機器翻譯)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure Cosmos DB 中的診斷記錄](cosmosdb-monitor-resource-logs.md) (機器翻譯)
* 深入了解 [Azure 資訊安全中心](../security-center/security-center-introduction.md)