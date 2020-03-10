---
title: 在 Azure 資訊安全中心整合安全性解決方案 | Microsoft Docs
description: 了解 Azure 資訊安全中心如何與夥伴整合，以提高您 Azure 資源的整體安全性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 23a00c766dbb38853c57c91e7f59ec364390c44b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392501"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>在 Azure 資訊安全中心整合安全性解決方案
這份文件可協助您管理已連線到 Azure 資訊安全中心的安全性解決方案，並且新增新的項目。

> [!NOTE]
> 安全性解決方案的子集已于2019年7月31日淘汰。 如需詳細資訊和替代服務，請參閱[淘汰資訊安全中心功能（2019年7月）](security-center-features-retirement-july2019.md#menu_solutions)。

## <a name="integrated-azure-security-solutions"></a>整合式 Azure 安全性解決方案
資訊安全中心可以使得在 Azure 中啟用整合式安全性解決方案變得簡單。 優點包括：

- **簡化部署**：資訊安全中心提供整合式合作夥伴解決方案的精簡佈建。 針對反惡意程式碼和弱點評估之類的解決方案，資訊安全中心可以在您的虛擬機器上布建代理程式。 對於防火牆設備，資訊安全中心可以處理所需的許多網路設定。
- **整合偵測**：來自合作夥伴解決方案的安全性事件會自動收集、彙總以及顯示為資訊安全中心警示和事件的一部分。 這些事件也會與來自其他來源的偵測整合，以提供進階的威脅偵測功能。
- **統一的健全狀況監視與管理**：客戶可以使用整合式的健康情況事件，一眼就能監視所有合作夥伴解決方案。 提供基本管理功能，而且可以讓您輕鬆使用夥伴解決方案存取進階設定。

目前，整合式安全性解決方案包含[Qualys](https://www.qualys.com/public-cloud/#azure)和[Rapid7](https://www.rapid7.com/products/insightvm/)和 Microsoft 應用程式閘道 Web 應用程式防火牆的弱點評量。

> [!NOTE]
> 資訊安全中心不會在合作夥伴虛擬應用裝置上安裝 Microsoft Monitoring Agent，因為大部分的安全性廠商會禁止在其應用裝置上執行的外部代理程式。
>
>

## <a name="how-security-solutions-are-integrated"></a>安全性解決方案如何整合
從資訊安全中心部署的 Azure 安全性解決方案會自動連線。 您也可以連接其他安全性資料來源，包括內部部署或其他雲端中執行的電腦。

![夥伴解決方案整合](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>管理整合式 Azure 安全性解決方案和其他資料來源

1. 登入 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)。

2. 在 [Microsoft Azure] 功能表中，選取 [資訊安全中心]。 [資訊安全中心 - 概觀] 隨即開啟。

3. 在 [資訊安全中心] 功能表下，選取 [安全性解決方案]。

   ![資訊安全中心概觀](./media/security-center-partner-integration/overview.png)

在 [**安全性解決方案**] 中，您可以看到整合式 Azure 安全性解決方案的健康情況，並執行基本管理工作。

### <a name="connected-solutions"></a>連線的解決方案

[**已連接的解決方案**] 區段包含目前連接到資訊安全中心的安全性解決方案。 它也會顯示每個解決方案的健全狀況狀態。  

![連線的解決方案](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

合作夥伴解決方案的狀態可以是︰

* 狀況良好（綠色）-沒有健康情況問題。
* 狀況不良（紅色）-有一個需要立即注意的健康情況問題。
* 健康情況問題 (橘色) - 解決方案已停止報告其健康情況。
* 未報告（灰色）-解決方案尚未報告任何專案，且沒有可用的健康情況資料。 如果解決方案的狀態是最近連線且仍在部署中，則可能未報告。

> [!NOTE]
> 如果健康情況狀態資料無法使用，資訊安全中心就會顯示最後收到之事件的日期和時間，以指出解決方案是否進行報告。 如果沒有可用的健康情況資料，而且在過去14天內未收到任何警示，資訊安全中心會指出解決方案狀況不良或未報告。
>
>

1. 選取 [ **VIEW** ] 取得其他資訊和選項，例如：

   - **解決方案主控台**。 開啟這個解決方案的管理體驗。
   - **連結 VM**。 開啟 [連結應用程式] 頁面。 您可以在這裡將資源連接到合作夥伴解決方案。
   - **刪除解決方案**。
   - **設定**。

   ![合作夥伴解決方案詳細資料](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>探索到的解決方案

資訊安全中心會自動探索在 Azure 中執行但未連線到資訊安全中心的安全性解決方案，並在 [探索到的**解決方案**] 區段中顯示解決方案。 這些解決方案包括 Azure 解決方案，例如[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)和合作夥伴解決方案。

> [!NOTE]
> 已探索解決方案功能的訂用帳戶層級需要資訊安全中心的標準層。 若要深入瞭解定價層，請參閱[定價](security-center-pricing.md)。
>
>

選取解決方案下的 **[連線]** 以與資訊安全中心整合，並收到安全性警示的通知。

### <a name="add-data-sources"></a>新增資料來源

[新增資料來源] 區段包含可以連線的其他可用資料來源。 如需從這些來源新增資料的指示，請按一下 [新增]。

![資料來源](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>將資料匯出至 SIEM

> [!NOTE]
> 如需將資料匯出至 SIEM 較簡單的方法（目前處於預覽階段）的詳細資訊，請參閱[匯出安全性警示和建議（預覽）](continuous-export.md)。 新方法不會使用「活動記錄」做為中間人，並允許從資訊安全中心直接匯出至事件中樞（然後再到 SIEM），它也支援匯出安全性建議。


您可以設定您的 Siem 或其他監視工具來接收 Azure 資訊安全中心事件。

Azure 資訊安全中心中的所有事件都會發佈至 Azure 監視器的 Azure[活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。 Azure 監視器會使用[合併管線](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)將資料串流至事件中樞，然後再將其提取到您的監視工具中。

以下幾節說明如何設定資料以串流到事件中樞。 這些步驟假設您已經在 Azure 訂用帳戶中設定 Azure 資訊安全中心。

### <a name="high-level-overview"></a>高階概述

![高階概述](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>公開至 SIEM 的 Azure 安全性資料為何？

在此版本中，我們會公開[安全性警示。](../security-center/security-center-managing-and-responding-alerts.md) 在即將發行版本中，我們將使用安全性建議來擴充此資料集。

### <a name="how-to-set-up-the-pipeline"></a>如何設定管線

#### <a name="create-an-event-hub"></a>建立事件中心

開始之前，請先[建立事件中樞命名空間](../event-hubs/event-hubs-create.md)-所有監視資料的目的地。

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>將 Azure 活動記錄檔串流至事件中樞

請參閱下列文章將[活動記錄串流至事件中樞](../azure-monitor/platform/activity-logs-stream-event-hubs.md)。

#### <a name="install-a-partner-siem-connector"></a>安裝合作夥伴 SIEM 連接器 

使用 Azure 監視器將監視資料路由傳送到事件中樞，可讓您輕鬆地與合作夥伴 SIEM 和監視工具整合。

如需[支援的 siem](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)清單，請參閱下列文章。

### <a name="example-for-querying-data"></a>查詢資料範例 

以下是一些您可以用來提取警示資料的 Splunk 查詢：

| **查詢描述** | **查詢** |
|----|----|
| 所有警示| index=main Microsoft.Security/locations/alerts|
| 依作業名稱對作業計數進行總結| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| 取得警示資訊：時間、名稱、狀態、識別碼和訂用帳戶 | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何在資訊安全中心中整合夥伴解決方案。 如要深入了解資訊安全中心，請參閱下列文章：

* [資訊安全中心的安全性健康情況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。