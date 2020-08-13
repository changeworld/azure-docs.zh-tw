---
title: 針對 Azure 度量警示進行疑難排解
description: Azure 監視器計量警示和可能解決方案的常見問題。
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 08/13/2020
ms.subservice: alerts
ms.openlocfilehash: 1c1fe613721ea2bc2d4b9665d6da73b094487efc
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190661"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>針對 Azure 監視器計量警示中的問題進行疑難排解 

本文討論 Azure 監視器計量 [警示](alerts-metric-overview.md) 的常見問題，以及如何進行疑難排解。

當您的監視資料中發現重要條件時，Azure 監視器警示會主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 如需警示的詳細資訊，請參閱 [Microsoft Azure 中的警示總覽](alerts-overview.md)。

## <a name="metric-alert-should-have-fired-but-didnt"></a>計量警示應已引發，但未 

如果您認為計量警示應該已引發，但未引發，而且在 Azure 入口網站中找不到，請嘗試下列步驟：

1. 設定 **-請**檢查計量警示規則設定，以確定已正確設定：
    - 檢查是否已如預期般設定**匯總類型**、**匯總資料細微性 (期間) **和**臨界值**或**敏感度**
    - 如需使用動態閾值的警示規則，請檢查是否已設定 advanced 設定，因為 **違規次數** 可能會篩選警示並 **忽略資料，然後才** 會影響閾值的計算方式

       > [!NOTE] 
       > 動態閾值必須至少有3天和30個度量樣本，才會變成作用中。

2. 已**引發但沒有通知**-請檢查[引發的警示清單](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)，以查看是否可以找到引發的警示。 如果您可以在清單中看到警示，但有一些動作或通知發生問題，請參閱 [這裡](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)的詳細資訊。

3. **已** 在作用中-檢查您預期會收到警示的計量時間序列是否已引發警示。 計量警示是可設定狀態的，這表示在特定計量時間序列上引發警示後，將不會引發該時間序列上的其他警示，直到不再觀察到問題為止。 這個設計選擇可減少雜訊。 當警示條件不符合三個連續的評估時，就會自動解決警示。

4. **使用的維度** -如果您已選取計量 [的某些維度值](./alerts-metric-overview.md#using-dimensions)，警示規則會監視每個個別的計量時間序列 (如維度值的組合所定義，) 是否違反閾值。 若要同時監視匯總計量時間序列 (未選取任何維度) ，請在 [度量] 上設定額外的警示規則，而不選取維度。

5. **匯總和時間細微性** -如果您要使用 [計量圖表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)將度量視覺化，請確定：
    * 度量圖表中選取的 **匯總** 與您的警示規則中的 **匯總類型** 相同
    * 選取的 **時間細微性** 與警示規則中的 **匯總資料細微性 (期間) ** 相同 (而且不會設定為 [自動] ) 

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>計量警示在不應出現時引發

如果您認為您的計量警示不應該引發，但卻發生了，下列步驟可能有助於解決此問題。

1. 檢查 [引發的警示清單](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) 以找出引發的警示，然後按一下以查看其詳細資料。 請參閱 [為什麼會 **引發此警示？** ] 底下所提供的資訊，以查看觸發警示時的計量圖表、計量 **值**和 **臨界值** 。

    > [!NOTE] 
    > 如果您使用動態閾值條件類型，並認為所使用的臨界值不正確，請使用苦臉圖示提供意見反應。 此意見反應將會影響機器學習演算法研究，並協助改善未來的偵測。

2. 如果您已針對計量選取多個維度值，當維度值組合所定義的 **任何** 計量時間序列 (，) 違反閾值時，就會觸發警示。 如需在計量警示中使用維度的詳細資訊，請參閱[此處](./alerts-metric-overview.md#using-dimensions)。

3. 請檢查警示規則設定，確定已正確設定：
    - 檢查是否已如預期般設定**匯總類型**、**匯總資料細微性 (期間) **和**臨界值**或**敏感度**
    - 如需使用動態閾值的警示規則，請檢查是否已設定 advanced 設定，因為 **違規次數** 可能會篩選警示並 **忽略資料，然後才** 會影響閾值的計算方式

   > [!NOTE]
   > 動態閾值必須至少有3天和30個度量樣本，才會變成作用中。

4. 如果您要使用 [計量圖表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)將度量視覺化，請確定：
    - 度量圖表中選取的 **匯總** 與您的警示規則中的 **匯總類型** 相同
    - 選取的 **時間細微性** 與警示規則中的 **匯總資料細微性 (期間) ** 相同 (而且不會設定為 [自動] ) 

5. 如果在已經引發警示的情況下引發警示，而這些警示會監視未解決) 的相同準則 (，請檢查警示規則是否已設定 *autoMitigate* 屬性設為 **false** (此屬性只能透過 REST/PowerShell/CLI 來設定，因此請檢查用來部署警示規則) 的腳本。 在這種情況下，警示規則不會將引發的警示自動解析，而且不需要在引發的警示重新引發後再引發。


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>找不到警示虛擬機器來賓計量的度量

若要對虛擬機器的客體作業系統計量發出警示 (例如：記憶體、磁碟空間) ，請確定您已安裝必要的代理程式來收集此資料，以 Azure 監視器計量：
- [針對 Windows VM](./collect-custom-metrics-guestos-resource-manager-vm.md)
- [針對 Linux VM](./collect-custom-metrics-linux-telegraf.md)

如需從虛擬機器的客體作業系統收集資料的詳細資訊，請參閱 [這裡](../insights/monitor-vm-azure.md#guest-operating-system)。
    
> [!NOTE] 
> 如果您已設定要傳送至 Log Analytics 工作區的來賓計量，計量會顯示在 Log Analytics 工作區資源底下，而且只有在建立監視警示規則的警示規則之後， **才** 會開始顯示資料。 若要這麼做，請遵循步驟來[設定記錄的計量警示](./alerts-metric-logs.md#configuring-metric-alert-for-logs)。

## <a name="cant-find-the-metric-to-alert-on"></a>找不到要發出警示的度量

如果您想要針對特定計量發出警示，但看不到該資源的任何度量，請 [檢查是否支援度量警示的資源類型](./alerts-metric-near-real-time.md)。
如果您可以看到資源的某些計量，但找不到特定的度量，請 [檢查該度量是否可用](./metrics-supported.md)，如果是，請參閱計量描述，以查看它是否僅適用于資源的特定版本。

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>找不到要警示的計量維度

如果您想要針對 [度量的特定維度值](./alerts-metric-overview.md#using-dimensions)發出警示，但找不到這些值，請注意下列事項：

1. 可能需要幾分鐘的時間，維度值才會出現在 [維度值] 清單底下
1. 顯示的維度值會以過去三天內收集的計量資料為基礎
1. 如果尚未發出維度值，請按一下 [+] 符號來新增自訂值
1. 如果您想要針對維度的所有可能值發出警示 (包括) 的未來值，請核取 [選取 *] 核取方塊

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>已刪除資源上仍定義的度量警示規則 

刪除 Azure 資源時，不會自動刪除相關聯的計量警示規則。 若要刪除與已刪除資源相關聯的警示規則：

1. 開啟已刪除資源定義所在的資源群組
1. 在顯示資源的清單中，勾選 [顯示隱藏的類型] 核取方塊
1. 依類型 == **microsoft.insights/metricalerts** 篩選清單
1. 選取相關的警示規則，然後選取 [**刪除**]

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>每次符合我的條件時，就會發生計量警示

計量警示預設為可設定狀態，因此，如果在指定的時間序列上已經引發警示，則不會引發其他警示。 如果您想要將特定的計量警示規則設定為無狀態，並在符合警示條件的每個評估上收到警示，請以程式設計方式建立警示規則 (例如，透過 [Resource Manager](./alerts-metric-create-templates.md)、 [PowerShell](/powershell/module/az.monitor/?view=azps-3.6.1)、 [REST](/rest/api/monitor/metricalerts/createorupdate)、 [CLI](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)) ，然後將 *autoMitigate* 屬性設定為 ' False '。

> [!NOTE] 
> 將計量警示規則設為無狀態，可防止引發的警示變成已解決，因此即使在不符合條件的情況下，引發的警示仍會維持在已引發狀態，直到30天的保留期限為止。

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>針對尚未發出的自訂計量定義警示規則

建立計量警示規則時，系統會根據計量 [定義 API](/rest/api/monitor/metricdefinitions/list) 來驗證計量名稱，以確保它存在。 在某些情況下，您會想要建立自訂計量的警示規則，即使在發出之前也一樣。 例如，使用 Resource Manager 範本建立 (時) 將發出自訂計量的 Application Insights 資源，以及監視該度量的警示規則。

若要避免在嘗試驗證自訂計量的定義時部署失敗，您可以在警示規則的 [準則] 區段中使用 *skipMetricValidation* 參數，這會導致略過計量驗證。 請參閱下列範例，以瞭解如何在 Resource Manager 範本中使用此參數。 如需詳細資訊，請參閱 [建立計量警示規則的完整 Resource Manager 範本範例](./alerts-metric-create-templates.md)。

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                    "name" : "condition1",
                        "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                        "dimensions":[],
                        "operator": "GreaterThan",
                        "threshold" : 10,
                        "timeAggregation": "Average",
                    "skipMetricValidation": true
        }
              ]
        }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>透過 Azure 入口網站匯出計量警示規則的 Azure Resource Manager 範本

匯出計量警示規則的 Resource Manager 範本，可協助您瞭解其 JSON 語法和屬性，並可用來將未來的部署作業自動化。
1. 流覽至入口網站中的 [ **資源群組** ] 區段，然後選取包含規則的資源群組。
2. 在 [總覽] 區段中，選取 [ **顯示隱藏的類型** ] 核取方塊。
3. 在 [ **類型** ] 篩選中，選取 [ *microsoft insights/metricalerts*]。
4. 選取相關的警示規則，以查看其詳細資料。
5. 在 [ **設定**] 底下，選取 [ **匯出範本**]。

## <a name="metric-alert-rules-quota-too-small"></a>度量警示規則配額太小

每個訂用帳戶的計量警示規則允許數目受限於 [配額限制](../service-limits.md)。

如果您已達到配額限制，下列步驟可能有助於解決此問題：
1. 請嘗試刪除或停用不再使用的計量警示規則。

2. 切換至使用監視多個資源的計量警示規則。 有了這項功能，單一警示規則就可以只使用一個針對配額計算的警示規則來監視多個資源。 如需此功能和受支援資源類型的詳細資訊，請參閱[這裡](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。

3. 如果您需要增加配額限制，請開啟支援要求，並提供下列資訊：

    - 需要增加配額限制的訂用帳戶識別碼 (s) 
    - 配額增加的資源類型：計量 **警示** 或計量 **警示 (傳統) **
    - 要求的配額限制

## <a name="check-total-number-of-metric-alert-rules"></a>檢查計量警示規則的總數

若要檢查目前的計量警示規則使用量，請遵循下列步驟。

### <a name="from-the-azure-portal"></a>從 Azure 入口網站

1. 開啟 [警示] 畫面，然後按一下 [管理警示規則]
2. 使用 [ **訂** 用帳戶] 下拉式清單控制項來篩選相關的訂用帳戶
3. 請確定不會篩選至特定的資源群組、資源類型或資源
4. 在 [**信號類型**] 下拉式控制項中，選取 [**計量**]
5. 確認 [**狀態**] 下拉式清單控制項已設定為 [**已啟用**]
6. 計量警示規則總數會顯示在 [警示規則] 清單上方

### <a name="from-api"></a>從 API

- PowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- REST API - [依訂用帳戶列出](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [az monitor metrics alert list](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>使用 Resource Manager 範本、REST API、PowerShell 或 Azure CLI 來管理警示規則

如果您在使用 Resource Manager 範本、REST API、PowerShell 或 Azure 命令列介面 (CLI) 來建立、更新、抓取或刪除計量警示時遇到問題，下列步驟可能有助於解決此問題。

### <a name="resource-manager-templates"></a>Resource Manager 範本

- 檢閱[常見的 Azure 部署錯誤](../../azure-resource-manager/templates/common-deployment-errors.md)清單，並據以進行疑難排解
- 請參閱計量 [警示 Azure Resource Manager 範本範例](./alerts-metric-create-templates.md) ，以確保您正確地傳遞所有參數

### <a name="rest-api"></a>REST API

請參閱 [REST API 指南](/rest/api/monitor/metricalerts/) ，確認您是否正確地傳遞所有參數

### <a name="powershell"></a>PowerShell

請確定您使用的是正確的 PowerShell Cmdlet 來進行計量警示：

- 計量警示的 PowerShell Cmdlet 可在 [Az.Monitor 模組](/powershell/module/az.monitor/?view=azps-3.6.1)中取得
- 針對新的 (非傳統) 計量警示，請務必使用結尾為 ' V2 ' 的 Cmdlet (例如 [AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2?view=azps-3.6.1)) 

### <a name="azure-cli"></a>Azure CLI

請確定您使用的是適用于計量警示的正確 CLI 命令：

- 計量警示的 CLI 命令會以 `az monitor metrics alert` 開頭。 請檢閱 [Azure CLI 參考](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)以了解語法。
- 您會看到[說明如何使用計量警示 CLI 的範例](./alerts-metric.md#with-azure-cli)
- 若要針對自訂計量發出警示，請務必在計量名稱前面加上相關的計量命名空間：NAMESPACE.METRIC

### <a name="general"></a>一般

- 如果您收到 `Metric not found` 錯誤：

   - 針對平臺計量：請確定您使用[的是 [Azure 監視器支援的計量] 頁面](./metrics-supported.md)中的 [**度量**] 名稱，而不是 [**度量] 顯示名稱**

   - 針對自訂計量：請確定已發出計量， (您無法針對尚未存在) 的自訂計量建立警示規則，而且您要提供自訂計量的命名空間 (請參閱 [這裡](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric) 的 Resource Manager 範本範例) 

- 如果您要建立 [記錄的計量警示](./alerts-metric-logs.md)，請確保包含適當的相依性。 請參閱[範例範本](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs)。

- 如果您要建立包含多個準則的警示規則，請注意下列條件約束：

   - 在每個準則中，您只能為每個維度選取一個值
   - 您不能使用 "\*" 做為維度值
   - 當不同使用準則來中設定的計量支援相同的維度時，必須以相同的方式明確設定所有這些計量的維度值， (在 [這裡](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria) 看到 Resource Manager 範本範例) 


## <a name="no-permissions-to-create-metric-alert-rules"></a>沒有建立計量警示規則的許可權

若要建立度量警示規則，您必須具有下列許可權：

- 警示規則之目標資源的讀取權限
- 建立警示規則之資源群組的寫入權限 (如果您要從 Azure 入口網站建立警示規則，警示規則會建立在目標資源所在的相同資源群組中) 
- 與警示規則相關聯之任何動作群組的 [讀取] 許可權 (如果適用) 


## <a name="naming-restrictions-for-metric-alert-rules"></a>計量警示規則的命名限制

請考慮計量警示規則名稱的下列限制：

- 無法變更計量警示規則名稱， (建立後) 重新命名
- 度量警示規則名稱在資源群組內必須是唯一的
- 度量警示規則名稱不能包含下列字元： * # & +：  < > ？ @ % { } \ / 
- 計量警示規則名稱的結尾不能是下列字元：。


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>在具有多個條件的計量警示規則中使用維度時的限制

計量警示支援多維度度量的警示，以及支援定義多個條件， (每個警示規則) 最多5個條件。

在包含多個條件的警示規則中使用維度時，請考慮下列條件約束：
- 在每個條件中，您只能為每個維度選取一個值。
- 您不能使用 [選取所有目前和未來的值] 選項， (選取 [ \*) ]。
- 當不同條件中設定的計量支援相同的維度時，必須以相同的方式明確設定維度值，以便在相關條件) 中 (所有這些計量。
例如：
    - 請考慮在儲存體帳戶上定義的度量警示規則，並監視兩個條件：
        * 總 **交易** 數 > 5
        * 平均 **SuccessE2ELatency** > 250 毫秒
    - 我想要更新第一個條件，而且只監視**ApiName**維度等於 *"GetBlob"* 的交易
    - 因為**交易**和**SuccessE2ELatency**度量都支援**ApiName**維度，所以我需要同時更新這兩個條件，而且兩者都使用 *"GetBlob"* 值來指定**ApiName**維度。


## <a name="next-steps"></a>後續步驟

- 如需有關警示和通知的一般疑難排解資訊，請參閱 [疑難排解 Azure 監視器警示中的問題](alerts-troubleshoot.md)。
