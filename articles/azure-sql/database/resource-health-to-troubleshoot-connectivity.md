---
title: 使用 Azure 資源健康狀態來監視資料庫健全狀況
description: 使用 Azure 資源健康狀態來監視 Azure SQL Database 和 Azure SQL 受控執行個體健全狀況，協助您在 Azure 問題影響您的 SQL 資源時診斷並取得支援。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 27865afd356be9eac64083c1ebdeb6ced43dbd18
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986937"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>使用資源健康狀態來疑難排解 Azure SQL Database 和 Azure SQL 受控執行個體的連線能力
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 和 Azure SQL 受控執行個體的[資源健康狀態](../../service-health/resource-health-overview.md#get-started)可協助您在 Azure 問題影響您的 SQL 資源時，進行診斷並取得支援。 它會通知您資源的目前及過去的健康狀態，並協助您解決問題。 當您需要 Azure 服務問題的協助時，資源健康狀態會提供技術支援。

![總覽](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>健康狀態檢查

資源健康情況會藉由檢查是否成功登入資源，來判斷 SQL 資源的健康情況。 目前，SQL Database 資源的資源健康狀態只會檢查因系統錯誤而不是使用者錯誤而導致的登入失敗。 資源健康狀態狀態會每隔1到2分鐘更新一次。

## <a name="health-states"></a>健康狀態

### <a name="available"></a>可用

若狀態為 [**可用**]，表示資源健康狀態因為您的 SQL 資源發生系統錯誤而未偵測到登入失敗。

![可用](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>已降級

若狀態為 [已**降級**]，表示資源健康狀態偵測到大部分的成功登入，但也有一些失敗。 這些很可能是暫時性的登入錯誤。 若要減少暫時性登入錯誤所造成之連線問題的影響，請在您的程式碼中執行[重試邏輯](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。

![已降級](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>無法使用

若狀態為 [無法使用]****，表示資源健康情況偵測到持續性的 SQL 資源登入失敗。 如果您的資源持續處於此狀態長時間，請聯絡支援人員。

![無法使用](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Unknown

[不明]**** 健康狀態表示資源健康狀態超過 10 分鐘未收到此資源的相關資訊。 雖然此狀態並非資源狀態的明確指示，卻是疑難排解程序中的重要資料點。 如果資源如預期般執行，幾分鐘後資源的狀態會變更為 [可用]。 如果您遇到資源問題，[不明] 健康狀態可能暗示資源受到平台事件影響。

![Unknown](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>歷程記錄資訊

在資源健康狀態的 [健康情況歷程記錄] 區段中，您最多可以存取14天的健康情況歷程記錄。 此區段也會針對資源健康情況所報告的停止運作狀況，顯示停止運作的原因 (如果有的話)。 目前，Azure 會以兩分鐘的資料細微性顯示您資料庫資源的停機時間。 實際的停止運作時間可能少於一分鐘。 平均為8秒。

### <a name="downtime-reasons"></a>停止運作的原因

當您的資料庫遇到停機時間時，會執行分析以判斷原因。 找到的停止運作原因會報告在 [資源健康狀態] 的 [健康情況歷程記錄] 區段中。 停止運作的原因通常會在事件發生後的 30 分鐘發佈。

#### <a name="planned-maintenance"></a>預定的維修

Azure 基礎結構會定期執行規劃的維護–資料中心內的硬體或軟體元件的升級。 當資料庫進行維護時，Azure SQL 可能會終止某些現有的連線，並拒絕新的連接。 在規劃的維護期間所經歷的登入失敗通常是暫時性的，[重試邏輯](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)可協助降低影響。 如果您持續遇到登入錯誤，請聯絡支援人員。

#### <a name="reconfiguration"></a>Reconfiguration

重新進行重新進行的重新設會視為暫時性狀況，而且預期會從時間開始。 負載平衡或軟體/硬體失敗都可能觸發這些事件。 任何連線到雲端資料庫的用戶端生產應用程式均應實作健全的連線[重試邏輯](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)，因為這有助於減輕這些狀況的影響，且通常會讓使用者更清楚了解這些錯誤。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[暫時性錯誤的重試邏輯](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。
- 針對[SQL 連接錯誤進行疑難排解、診斷及預防](troubleshoot-common-connectivity-issues.md)。
- 深入瞭解如何設定[資源健康狀態警示](../../service-health/resource-health-alert-arm-template-guide.md)。
- 取得[資源健康狀態](../../application-gateway/resource-health-overview.md)的總覽。
- 請參閱[資源健康狀態常見問題](../../service-health/resource-health-faq.md)。
