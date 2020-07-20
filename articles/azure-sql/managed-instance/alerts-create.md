---
title: 受控執行個體的設定警示和通知（Azure 入口網站）
description: 使用 Azure 入口網站建立 SQL 受控執行個體警示，在符合您指定的條件時，可以觸發通知或自動化。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: ae139dd65242be9456f3498c494e1a7c5a29402f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695689"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>使用 Azure 入口網站建立 Azure SQL 受控執行個體的警示
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文說明如何使用 Azure 入口網站設定 Azure SQL 受控執行個體資料庫中的資料庫警示。 警示可以傳送電子郵件、呼叫 webhook、執行 Azure 函式、runbook、呼叫外部 ITSM 相容票證系統、在手機上打電話，或在某些計量（例如實例儲存體大小或 CPU 使用量）達到預先定義的臨界值時傳送文字訊息。 本文也提供設定警示期間的最佳做法。


## <a name="overview"></a>總覽

您可以收到以您 Azure 服務的監視計量或事件為基礎的警示。

* 計量**值**-當指定的度量值超出您在任一方向指派的閾值時，就會觸發警示。 也就是說，當先符合條件而之後該條件不再符合時，兩方面皆會觸發。

您可以在警示觸發時，設定警示執行下列動作︰

* 傳送電子郵件通知給服務管理員和共同管理員
* 傳送電子郵件給您指定的其他電子郵件。
* 使用語音提示撥打電話號碼
* 將文字訊息傳送至電話號碼
* 呼叫 Webhook
* 呼叫 Azure 函式
* 呼叫 Azure runbook
* 呼叫外部票證 ITSM 相容系統

您可以使用[Azure 入口網站、PowerShell 或 Azure CLI](../../azure-monitor/platform/alerts-classic-portal.md)或[Azure 監視器 REST API](/rest/api/monitor/alertrules)來設定和取得警示規則的相關資訊。 

## <a name="alerting-metrics-available-for-managed-instance"></a>適用于受控實例的警示計量

> [!IMPORTANT]
> 警示計量僅適用于受控實例。 無法使用受控實例中個別資料庫的警示計量。 另一種方式是使用[診斷記錄](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)形式的資料庫診斷遙測。 您可以使用受控實例的[記錄警示腳本](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance)，在[SQL 分析](../../azure-monitor/insights/azure-sql.md)產品內設定診斷記錄的警示。

下列受控實例計量適用于警示設定：

| 計量 | 描述 | 量值單位 \ 可能的值 |
| :--------- | --------------------- | ----------- |
| CPU 百分比平均 | 所選時段內 CPU 使用率的平均百分比。 | 0-100 （百分比） |
| 讀取的 IO 位元組 | 在選取的時段內讀取的 IO 位元組。 | 位元組 |
| 寫入的 IO 位元組 | 在選取的時段內寫入的 IO 位元組。 | 位元組 |
| IO 要求計數 | 所選時段內的 IO 要求計數。 | 數值 |
| 保留的儲存體空間 | 目前的最大值。保留給受控實例的儲存空間。 資源調整作業的變更。 | MB （mb） |
| 使用的儲存體空間 | 所選期間內使用的儲存空間。 資料庫和實例的儲存體耗用量變更。 | MB （mb） |
| 虛擬核心計數 | 為受控實例布建的虛擬核心。 資源調整作業的變更。 | 4-80 （虛擬核心） |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 入口網站建立計量的警示規則

1. 在 Azure[入口網站](https://portal.azure.com/)中，找出您想要監視的受控實例，然後選取它。

2. 選取 [監視] 區段中的 [**計量**] 功能表項目。

   ![監視](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. 在下拉式功能表中，選取您想要設定警示的其中一個計量（所使用的儲存空間如範例所示）。

4. 選取 [匯總週期]-在指定的時段中達到的平均值、最小值或最大值（Avg、Min 或 Max）。 

5. 選取**新的警示規則**

6. 在 [建立警示規則] 窗格中，按一下 [**條件名稱**] （範例中會顯示所使用的儲存空間）

   ![定義條件](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. 在 [設定信號邏輯] 窗格上，定義 [運算子]、[匯總類型] 和 [臨界值]

   * 運算子類型選項大於、等於和小於（臨界值）
   * 匯總類型選項為最小值、最大值或平均值（在匯總細微性期間）
   * 臨界值是將根據運算子和匯總準則評估的警示值
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   在螢幕擷取畫面顯示的範例中，會使用 1840876 MB 的值來代表閾值為 1.8 TB 的臨界值。 當範例中的運算子設定為 [大於] 時，如果受控實例上的儲存空間耗用量超過 1.8 TB，就會建立警示。 請注意，儲存空間計量的臨界值必須以 MB 表示。

8. 設定評估期間-匯總資料細微性（分鐘）和評估頻率。 評估的頻率會指出警示系統會定期檢查是否符合閾值條件的時間。

9. 選取 [動作群組]。 [動作群組] 窗格將會顯示，讓您可以選取現有的或建立新的動作。 此動作會定義觸發警示時所發生的情況（例如，傳送電子郵件、在手機上呼叫您、執行 webhook、Azure function 或 runbook）。

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * 若要建立新的動作群組，請選取 [ **+ 建立動作群組**]

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * 定義您想要如何收到警示：輸入 [動作組名]、[簡短名稱]、[動作名稱] 和 [選取動作類型]。 動作類型會定義您是否要透過電子郵件、文字訊息、語音通話通知您，或可能會執行 webhook、Azure 函式、runbook，或將在相容系統中建立 ITSM 票證。

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. 填入記錄的警示規則詳細資料，選取 [嚴重性] 類型。

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * 按一下 [**建立警示規則**] 按鈕，完成建立警示規則的程式。

新的警示規則會在幾分鐘內生效，並根據您的設定來觸發。

## <a name="verifying-alerts"></a>驗證警示

> [!NOTE]
> 若要抑制雜訊警示，請參閱[使用動作規則的警示隱藏專案](../../azure-monitor/platform/alerts-action-rules.md#suppression-of-alerts)。

設定警示規則時，請確認您已滿意警示觸發程式及其頻率。 針對此頁面上顯示的範例，若要在所使用的儲存空間上設定警示，如果您的警示選項是電子郵件，您可能會收到如下所示的電子郵件。

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

此電子郵件會顯示警示名稱、臨界值的詳細資料，以及觸發警示的原因，協助您驗證和疑難排解警示。 您可以使用 [**在 Azure 入口網站中查看**] 按鈕來查看 Azure 入口網站中透過電子郵件收到的警示。 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>View、暫止、啟動、修改和刪除現有的警示規則

> [!NOTE]
> 您必須從 Azure 入口網站儀表板] 的 [警示] 功能表中管理現有的警示。 無法從受控執行個體資源] 分頁修改現有的警示。

若要查看、暫止、啟動、修改和刪除現有的警示：

1. 使用 Azure 入口網站搜尋來搜尋警示。 按一下 [警示]。

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   或者，您也可以按一下 Azure 導覽列上的 [警示] （如果已設定）。

2. 在 [警示] 窗格中，選取 [管理警示規則]。

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   現有警示的清單隨即顯示。 選取要管理的個別現有警示規則。 現有的作用中規則可以修改並調整為您的喜好設定。 使用中的規則也可以在不刪除的情況下暫停。 

## <a name="next-steps"></a>後續步驟

* 瞭解 Azure 監視器警示系統的詳細資訊，請參閱[Microsoft Azure 中的警示總覽](../../azure-monitor/platform/alerts-overview.md)
* 深入瞭解計量警示的詳細資訊，請參閱[瞭解計量警示在 Azure 監視器中的使用方式](../../azure-monitor/platform/alerts-metric-overview.md)
* 深入瞭解在警示中設定 webhook 的詳細資訊，請參閱[使用傳統計量警示呼叫 webhook](../../azure-monitor/platform/alerts-webhooks.md)
* 瞭解使用 PowerShell 設定和管理警示的詳細資訊，請參閱[動作規則](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)
* 若要瞭解如何使用 API 來設定和管理警示，請參閱[Azure 監視器 REST API 參考](https://docs.microsoft.com/rest/api/monitor/) 
