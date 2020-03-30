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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245378"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>在 Azure 資訊安全中心整合安全性解決方案
這份文件可協助您管理已連線到 Azure 資訊安全中心的安全性解決方案，並且新增新的項目。

> [!NOTE]
> 2019 年 7 月 31 日，安全解決方案子集已停用。 有關詳細資訊和替代服務，請參閱[安全中心停用功能（2019 年 7 月）。](security-center-features-retirement-july2019.md#menu_solutions)

## <a name="integrated-azure-security-solutions"></a>整合式 Azure 安全性解決方案
資訊安全中心可以使得在 Azure 中啟用整合式安全性解決方案變得簡單。 優點包括：

- **簡化部署**：資訊安全中心提供整合式合作夥伴解決方案的精簡佈建。 對於反惡意軟體和漏洞評估等解決方案，安全中心可以在虛擬機器上預配代理。 對於防火牆設備，安全中心可以處理所需的大部分網路設定。
- **集成檢測**：合作夥伴解決方案中的安全事件將自動收集、聚合，並作為安全中心警報和事件的一部分顯示。 這些事件也會與來自其他來源的偵測整合，以提供進階的威脅偵測功能。
- **統一的健全狀況監視與管理**：客戶可以使用整合式的健康情況事件，一眼就能監視所有合作夥伴解決方案。 提供基本管理功能，而且可以讓您輕鬆使用夥伴解決方案存取進階設定。

目前，集成安全解決方案包括[Qualys](https://www.qualys.com/public-cloud/#azure)和[Rapid7](https://www.rapid7.com/products/insightvm/)和 Microsoft 應用程式閘道 Web 應用程式防火牆的漏洞評估。

> [!NOTE]
> 安全中心不會在合作夥伴虛擬裝置上安裝 Microsoft 監視代理，因為大多數安全供應商禁止在其設備上運行外部代理。
>
>

## <a name="how-security-solutions-are-integrated"></a>安全性解決方案如何整合
從資訊安全中心部署的 Azure 安全性解決方案會自動連線。 您還可以連接其他安全資料來源，包括本地或其他雲中運行的電腦。

![夥伴解決方案整合](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>管理整合式 Azure 安全性解決方案和其他資料來源

1. 登錄到 Azure[門戶](https://azure.microsoft.com/features/azure-portal/)。

2. 在 [Microsoft Azure]**** 功能表中，選取 [資訊安全中心]****。 [資訊安全中心 - 概觀]**** 隨即開啟。

3. 在 [資訊安全中心] 功能表下，選取 [安全性解決方案]****。

   ![資訊安全中心概觀](./media/security-center-partner-integration/overview.png)

在**安全解決方案**中，您可以看到集成 Azure 安全解決方案的運行狀況並運行基本管理工作。

### <a name="connected-solutions"></a>連線的解決方案

**"已連接的解決方案**"部分包括當前連接到安全中心的安全解決方案。 它還顯示每個解決方案的運行狀況。  

![連線的解決方案](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

合作夥伴解決方案的狀態可以是︰

* 健康（綠色） - 沒有健康問題。
* 不健康（紅色） - 有一個健康問題，需要立即關注。
* 健康情況問題 (橘色) - 解決方案已停止報告其健康情況。
* 未報告（灰色） - 解決方案尚未報告任何內容，並且沒有可用的健康資料。 如果解決方案最近連接且仍在部署，則其狀態可能未報告。

> [!NOTE]
> 如果健康情況狀態資料無法使用，資訊安全中心就會顯示最後收到之事件的日期和時間，以指出解決方案是否進行報告。 如果沒有可用的運行狀況資料，並且在過去 14 天內未收到警報，則安全中心指示解決方案不正常或不報告。
>
>

1. 選擇 **"查看"** 以獲取其他資訊和選項，例如：

   - **解決方案主控台**。 開啟這個解決方案的管理體驗。
   - **連結 VM**。 打開連結應用程式頁面。 您可以在這裡將資源連接到合作夥伴解決方案。
   - **刪除解決方案**。
   - **配置**。

   ![合作夥伴解決方案詳細資料](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>探索到的解決方案

安全中心會自動探索在 Azure 中運行但未連接到安全中心的安全解決方案，並在 **"發現的解決方案"** 部分中顯示這些解決方案。 這些解決方案包括 Azure 解決方案，如[Azure AD 標識保護](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)以及合作夥伴解決方案。

> [!NOTE]
> 已探索解決方案功能的訂用帳戶層級需要資訊安全中心的標準層。 請參閱[定價](security-center-pricing.md)以瞭解有關定價層的更多情況。
>
>

選擇解決方案下的**CONNECT**以與安全中心集成，並通知安全警報。

### <a name="add-data-sources"></a>新增資料來源

[新增資料來源]**** 區段包含可以連線的其他可用資料來源。 如需從這些來源新增資料的指示，請按一下 [新增]****。

![資料來源](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>將資料匯出至 SIEM

> [!NOTE]
> 有關將資料匯出到 SIEM 的更簡單方法（當前處於預覽版）的詳細資訊，請參閱[匯出安全警報和建議（預覽）。](continuous-export.md) 新方法不使用活動日誌作為仲介，允許從安全中心直接匯出到事件中心（然後匯出到您的 SIEM），它還支援安全建議的出口。


您可以配置 SIEM 或其他監視工具以接收 Azure 安全中心事件。

Azure 安全中心的所有事件都發佈到 Azure 監視器的 Azure[活動日誌](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。 Azure 監視器使用[整合管道](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)將資料流程式傳輸到事件中心，然後將其拉入監視工具。

以下幾節說明如何設定資料以串流到事件中樞。 這些步驟假設您已經在 Azure 訂用帳戶中設定 Azure 資訊安全中心。

### <a name="high-level-overview"></a>高階概述

![高階概述](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>公開至 SIEM 的 Azure 安全性資料為何？

在此版本中，我們公開[安全警報。](../security-center/security-center-managing-and-responding-alerts.md) 在即將發行版本中，我們將使用安全性建議來擴充此資料集。

### <a name="how-to-set-up-the-pipeline"></a>如何設置管道

#### <a name="create-an-event-hub"></a>建立事件中心

開始之前，[請創建事件中心命名空間](../event-hubs/event-hubs-create.md)- 所有監視資料的目標。

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>將 Azure 活動記錄檔串流至事件中樞

請參閱以下文章[流活動日誌到事件中心](../azure-monitor/platform/activity-logs-stream-event-hubs.md)。

#### <a name="install-a-partner-siem-connector"></a>安裝合作夥伴 SIEM 連接器 

使用 Azure 監視器將監視資料路由傳送到事件中樞，可讓您輕鬆地與合作夥伴 SIEM 和監視工具整合。

有關[受支援的 SIEM](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)的清單，請參閱以下文章。

### <a name="example-for-querying-data"></a>查詢資料範例 

下面是一些可用於提取警報資料的 Splunk 查詢：

| **查詢描述** | **查詢** |
|----|----|
| 所有警示| index=main Microsoft.Security/locations/alerts|
| 依作業名稱對作業計數進行總結| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| 取得警示資訊：時間、名稱、狀態、識別碼和訂用帳戶 | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何在資訊安全中心中整合夥伴解決方案。 如要深入了解資訊安全中心，請參閱下列文章：

* [資訊安全中心的安全性健康情況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。