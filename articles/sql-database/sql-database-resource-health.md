---
title: 使用 Azure 資源健康狀態來監視資料庫健全狀況
description: 使用 Azure 資源健康情況監視 SQL Database 的健康情況，可協助您進行診斷，並在 Azure 問題影響您的 SQL 資源時取得支援。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 9e19e904b47d69444b491dd88ffe49ff812aafc3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208878"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>使用資源健康情況對 Azure SQL Database 的連線問題進行疑難排解

## <a name="overview"></a>概觀

SQL Database 的[資源健康情況](../service-health/resource-health-overview.md#get-started)可協助您進行診斷，並在 Azure 問題影響您的資源時取得支援。 它會通知您資源的目前及過去的健康狀態，並協助您解決問題。 資源健康狀態會在您需要解決 Azure 服務問題時提供技術支援。

![概觀](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>健康情況檢查

資源健康情況會藉由檢查是否成功登入資源，來判斷 SQL 資源的健康情況。 目前，SQL DB 資源的資源健康情況只會檢查因系統錯誤而導致的登入失敗，而不會檢查使用者錯誤。 Azure 資源健康狀態每 1-2 分鐘會更新一次。

## <a name="health-states"></a>健康狀態

### <a name="available"></a>可用

若狀態為 [可用]，表示資源健康情況未偵測到因 SQL 資源的系統錯誤而造成的登入失敗。

![可用](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>已降級

若狀態為 [已降級]，表示資源健康情況大多偵測到成功的登入，但也有一些失敗。 這些很可能是暫時性的登入錯誤。 若要減少暫時性登入錯誤所導致的連線問題影響，請在程式碼中實作[重試邏輯](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)。

![已降級](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>無法使用

若狀態為 [無法使用]，表示資源健康情況偵測到持續性的 SQL 資源登入失敗。 如果您的資源長時間處於此狀態，請連絡支援人員。

![無法使用](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Unknown

[不明] 健康狀態表示資源健康狀態超過 10 分鐘未收到此資源的相關資訊。 雖然此狀態並非資源狀態的明確指示，卻是疑難排解程序中的重要資料點。 如果資源如預期般執行，幾分鐘後資源的狀態會變更為 [可用]。 如果您遇到資源問題，[不明] 健康狀態可能暗示資源受到平台事件影響。

![Unknown](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>歷程記錄資訊

您可以在 [資源健康情況] 的 [健康情況歷程記錄] 區段中存取最多 14 天的健康情況歷程記錄。 此區段也會針對資源健康情況所報告的停止運作狀況，顯示停止運作的原因 (如果有的話)。 目前，Azure 會以兩分鐘為單位顯示 SQL 資料庫資源的停止運作時間。 實際的停止運作時間可能少於一分鐘 – 平均值為 8 秒。

### <a name="downtime-reasons"></a>停止運作的原因

當您的 SQL Database 發生停止運作的狀況時，系統會執行分析以判斷原因。 找到的停止運作原因會報告在 [資源健康狀態] 的 [健康情況歷程記錄] 區段中。 停止運作的原因通常會在事件發生後的 30 分鐘發佈。

#### <a name="planned-maintenance"></a>預定的維修

Azure 基礎結構會定期執行規劃的維護 – 資料中心的軟硬體元件升級。 在資料庫進行維護的期間，SQL 可能會終止某些現有的連線，並拒絕新連線。 在計劃性維護期間發生的登入失敗通常是暫時性的，[重試邏輯](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)可有效降低其影響。 如果持續發生登入，請連絡支援人員。

#### <a name="reconfiguration"></a>Reconfiguration

重新設定應視為暫時性的狀況，且不時會發生。 負載平衡或軟體/硬體失敗都可能觸發這些事件。 任何連線到雲端資料庫的用戶端生產應用程式均應實作健全的連線[重試邏輯](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)，因為這有助於減輕這些狀況的影響，且通常會讓使用者更清楚了解這些錯誤。

## <a name="next-steps"></a>後續步驟

- 深入了解[暫時性錯誤的重試邏輯](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [排解、診斷和防止 SQL 連線錯誤](./sql-database-connectivity-issues.md)
- 深入了解如何[設定資源健康情況警示](../service-health/resource-health-alert-arm-template-guide.md)
- 取得[資源健康情況](../service-health/resource-health-overview.md)的概觀
- [資源健康情況常見問題集](../service-health/resource-health-faq.md)
