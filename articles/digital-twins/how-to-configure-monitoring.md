---
title: 如何設定監視-Azure 數位 Twins |Microsoft Docs
description: 瞭解如何設定 Azure 數位 Twins 的監視和記錄選項。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: e35e18be20af3bd9f6fdc9541f9abfe857a6b87c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76511853"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>如何在 Azure Digital Twins 中設定監視

Azure Digital Twins 支援強固的記錄、監視與分析功能。 解決方案開發人員可以使用 Azure 監視器記錄、診斷記錄、活動記錄和其他服務，以支援 IoT 應用程式的複雜監視需求。 您可以合併記錄選項以查詢或檢視跨多的服務的記錄，並提供許多服務的精細記錄涵蓋範圍。

此文章摘要說明記錄與監視選項，以及如何以適用於 Azure Digital Twins 的特定方式合併它們。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>檢閱活動記錄

Azure [活動記錄](../azure-monitor/platform/platform-logs-overview.md)提供 Azure 每個服務執行個體之訂用帳戶層級事件與作業歷程記錄的快速見解。

訂用帳戶層級事件包括：

* 資源建立
* 資源移除
* 建立應用程式密碼
* 與其他服務整合

Azure Digital Twins 的活動記錄預設會啟用，而且可以透過下列方式在 Azure 入口網站中找到：

1. 選取您的 Azure Digital Twins 執行個體。
1. 選擇 [活動記錄]**** 以帶出顯示面板：

    [![活動記錄檔](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

針對進階活動記錄：

1. 選取 [記錄]**** 選項以顯示 [活動記錄分析概觀]****：

    [![選項](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. [活動記錄分析概觀]**** 會摘要說明基本活動記錄資料：

    [![活動記錄分析概觀]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>使用**活動記錄**來取得訂用帳戶層級事件的快速見解。

## <a name="enable-customer-diagnostic-logs"></a>啟用客戶診斷記錄

您可以為每個 Azure 執行個體設定 Azure [診斷設定](../azure-monitor/platform/platform-logs-overview.md)以彌補活動記錄的不足。 雖然活動記錄與訂用帳戶層級事件有關， 但診斷記錄提供資源本身作業歷程記錄的見解。

診斷記錄的範例包括：

* 使用者定義函式的執行時間
* 成功之 API 要求的回應狀態碼
* 從保存庫擷取應用程式金鑰

針對執行個體啟用診斷記錄：

1. 在 Azure 入口網站中讓資源上線。
1. 選取 [**診斷設定**]：

    [![診斷設定一](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. 選取 [**開啟診斷**以收集資料] （如果先前未啟用）。
1. 填入要求的欄位，並選取如何儲存資料以及要將資料儲存在何處：

    [![診斷設定二](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    診斷記錄通常會使用[Azure 檔案儲存體](../storage/files/storage-files-deployment-guide.md)儲存，並與[Azure 監視器記錄](../azure-monitor/log-query/get-started-portal.md)共用。 您可以同時選取這兩個選項。

>[!TIP]
>使用**診斷記錄**來取得對資源作業的見解。

## <a name="azure-monitor-and-log-analytics"></a>Azure 監視器與記錄分析

IoT 應用程式會將不同的資源、裝置、位置與資料整合在一起。 更精器的記錄提供有關整體應用程式架構中每個特定部分、服務或元件的詳細資訊，但通常需要整合式概觀來進行維護及偵錯。

Azure 監視器包含功能強大的 log analytics 服務，可讓您在一個位置中查看和分析記錄來源。 因此，「Azure 監視器」非常實用，可用來在複雜的 IoT 應用程式內分析記錄。

使用範例包括：

* 查詢多個診斷記錄歷程記錄
* 查看多個使用者定義函式的記錄
* 顯示特定時段內二或多個服務的記錄

完整記錄查詢是透過[Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md)來提供。 設定這些強大的功能：

1. 在 Azure 入口網站中搜尋 **Log Analytics**。
1. 將會顯示您可用的**Log Analytics 工作區**實例。 選擇一個並選取 [記錄]**** 以查詢：

    [![Log analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. 如果您還沒有**Log Analytics 工作區**實例，可以藉由選取 [**新增**] 按鈕來建立工作區：

    [![建立 OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

布建您的**Log Analytics 工作區**實例之後，您可以使用強大的查詢來尋找多個記錄檔中的專案，或使用**記錄管理**來搜尋特定準則：

   [![記錄管理](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

如需有關強大的查詢作業的詳細資訊，請參閱[開始使用查詢](../azure-monitor/log-query/get-started-queries.md)。

> [!NOTE]
> 第一次將事件傳送到**Log Analytics 工作區**時，您可能會經歷5分鐘的延遲。

Azure 監視器記錄也提供強大的錯誤和警示通知服務，可以藉由選取 [**診斷並解決問題**] 來查看：

   [![警示與錯誤通知](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>使用**Log Analytics 工作區**來查詢多個應用程式功能、訂閱或服務的記錄歷程記錄。

## <a name="other-options"></a>其他選項

Azure Digital Twins 也支援應用程式特定記錄與安全性稽核。 如需深入瞭解 Azure 數位 Twins 實例可用的所有 Azure 記錄選項，請閱讀[azure 記錄檔 audit](../security/fundamentals/log-audit.md)文章。

## <a name="next-steps"></a>後續步驟

- 深入了解 Azure [活動記錄](../azure-monitor/platform/platform-logs-overview.md)。

- 透過閱讀[診斷記錄概觀](../azure-monitor/platform/platform-logs-overview.md)以深入了解 Azure 診斷設定。

- 深入瞭解[Azure 監視器記錄](../azure-monitor/log-query/get-started-portal.md)。
