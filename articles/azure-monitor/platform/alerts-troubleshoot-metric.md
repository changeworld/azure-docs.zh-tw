---
title: 針對 Azure 度量警示進行疑難排解
description: Azure 監視器計量警示和可能解決方案的常見問題。
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 01/03/2021
ms.subservice: alerts
ms.openlocfilehash: 9a05fe509e032681a0bf5ed989595a25f66d33c6
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857336"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>針對 Azure 監視器計量警示中的問題進行疑難排解 

本文討論 Azure 監視器計量 [警示](alerts-metric-overview.md) 的常見問題，以及如何進行疑難排解。

當您在監視資料中找到重要的條件時，Azure 監視器警示會主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 如需警示的詳細資訊，請參閱 [Microsoft Azure 中的警示總覽](alerts-overview.md)。

## <a name="metric-alert-should-have-fired-but-didnt"></a>計量警示應已引發但未 

如果您認為計量警示應已引發但未引發，而且在 Azure 入口網站中找不到，請嘗試下列步驟：

1. 設定 **-檢查** 計量警示規則設定，以確定其已正確設定：
    - 檢查 **匯總類型**、 **匯總細微性 (期間)**，以及 **臨界值** 或 **敏感度** 是否已如預期般設定
    - 若為使用動態閾值的警示規則，請檢查是否已設定 [advanced] 設定，因為 **違規次數** 可能會篩選警示，並 **忽略資料，才** 會影響閾值的計算方式。

       > [!NOTE] 
       > 動態閾值需要至少3天和30個度量樣本，才能變成作用中。

2. 已 **引發但沒有通知**-請檢查 [引發的警示清單](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)，以查看是否可以找到引發的警示。 如果您可以在清單中看到警示，但有一些動作或通知有問題，請參閱 [這裡](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)的詳細資訊。

3. **已** 在使用中-檢查您預期要取得警示的計量時間序列上是否已引發警示。 計量警示是可設定狀態的，這表示在特定計量時間序列上引發警示後，將不會引發該時間序列上的其他警示，直到不再觀察到問題為止。 此設計選擇可減少雜訊。 當三個連續評估的警示條件不符合時，就會自動解決警示。

4. **使用的維度** -如果您已選取某個計量的某個 [維度值](./alerts-metric-overview.md#using-dimensions)，警示規則會監視每個個別計量時間序列 (如臨界值缺口) 的組合所定義。 若要同時監視匯總計量時間序列 (沒有選取任何維度) ，請在度量上設定額外的警示規則，而不選取維度。

5. **匯總和時間細微性** -如果您要使用 [計量圖表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)來視覺化度量，請確定：
    * 度量圖表中選取的 **匯總** 與警示規則中的 **匯總類型** 相同
    * 選取的 **時間資料細微性** 與您的警示規則中 **)  (期間的匯總細微性** 相同 (且未設定為「自動」 ) 

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>計量警示不應出現時引發

如果您認為您的計量警示不應該引發，但已執行，則下列步驟可能有助於解決此問題。

1. 檢查 [引發的警示清單](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) 以找出引發的警示，然後按一下以查看其詳細資料。 請檢查 **此警示引發的原因** 所提供的資訊，以查看觸發警示時的度量圖表、計量 **值** 和 **臨界值** 。

    > [!NOTE] 
    > 如果您使用動態閾值條件類型，而且認為使用的閾值不正確，請使用苦臉圖示提供意見反應。 這項意見反應會影響機器學習演算法研究，並協助改善未來的偵測結果。

2. 如果您已針對計量選取多個維度值，當 **任何** 計量時間序列 (如維度值組合所定義) 違反閾值時，就會觸發警示。 如需在計量警示中使用維度的詳細資訊，請參閱[此處](./alerts-metric-overview.md#using-dimensions)。

3. 請檢查警示規則設定，以確定其已正確設定：
    - 檢查 **匯總類型**、 **匯總細微性 (期間)**，以及 **臨界值** 或 **敏感度** 是否已如預期般設定
    - 若為使用動態閾值的警示規則，請檢查是否已設定 [advanced] 設定，因為 **違規次數** 可能會篩選警示，並 **忽略資料，才** 會影響閾值的計算方式。

   > [!NOTE]
   > 動態閾值需要至少3天和30個度量樣本，才能變成作用中。

4. 如果您要使用 [計量圖表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)來視覺化度量，請確定：
    - 度量圖表中選取的 **匯總** 與警示規則中的 **匯總類型** 相同
    - 選取的 **時間資料細微性** 與您的警示規則中 **)  (期間的匯總細微性** 相同 (且未設定為「自動」 ) 

5. 如果在已引發警示的情況下引發警示，但監視的相同準則 (未解決) ，請檢查是否已將 *autoMitigate* 屬性設定為 **false** (此屬性只能透過 REST/PowerShell/CLI 進行設定，因此請檢查用來部署警示規則) 的腳本。 在這種情況下，警示規則不會自動解析引發的警示，而且不需要先解決引發的警示，就能再次引發。


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>找不到用來警示虛擬機器來賓計量的度量

若要對虛擬機器的客體作業系統計量發出警示 (例如：記憶體、磁碟空間) ，請確定您已安裝必要的代理程式，以將此資料收集到 Azure 監視器計量：
- [針對 Windows VM](./collect-custom-metrics-guestos-resource-manager-vm.md)
- [針對 Linux VM](./collect-custom-metrics-linux-telegraf.md)

如需有關從虛擬機器的客體作業系統收集資料的詳細資訊，請參閱 [這裡](../insights/monitor-vm-azure.md#guest-operating-system)。

> [!NOTE] 
> 如果您已將來賓計量設定為傳送至 Log Analytics 工作區，計量會出現在 Log Analytics 工作區資源下，而且只會在建立監視這些計量的警示規則之後， **才** 會開始顯示資料。 若要這麼做，請遵循步驟來[設定記錄的計量警示](./alerts-metric-logs.md#configuring-metric-alert-for-logs)。

> [!NOTE] 
> 目前的計量警示不支援監視多部虛擬機器的來賓計量（具有單一警示規則）。 您可以使用 [記錄警示規則](./alerts-unified-log.md)來達成此目的。 若要這樣做，請確定系統會將來賓計量收集到 Log Analytics 工作區，並在工作區上建立記錄警示規則。

## <a name="cant-find-the-metric-to-alert-on"></a>找不到要警示的度量

如果您想要針對特定計量發出警示，但在建立警示規則時看不到該計量，請檢查下列事項：
- 如果您看不到該資源的任何計量，請[檢查計量警示是否支援該資源類型](./alerts-metric-near-real-time.md)。
- 如果您可以看到該資源的某些計量，但找不到特定計量，請[檢查該計量是否可供使用](./metrics-supported.md)，如果是，請參閱計量描述，以檢查其是否僅適用於資源的特定版本。
- 如果計量不適用於該資源，可能可用於資源記錄中，並且可以使用記錄警示來加以監視。 請在此參閱[如何從 Azure 資源收集和分析資源記錄](../learn/tutorial-resource-logs.md)的詳細資訊。

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>找不到要警示的度量維度

如果您想要對 [度量的特定維度值](./alerts-metric-overview.md#using-dimensions)發出警示，但找不到這些值，請注意下列事項：

1. 可能需要幾分鐘的時間，維度值才會出現在 [維度值] 清單底下
1. 顯示的維度值會以過去三天內收集的計量資料為基礎
1. 如果尚未發出維度值，請按一下 [+] 符號來新增自訂值
1. 如果您想要針對維度的所有可能值發出警示 (包括未來值) ，請選取 [選取 *] 核取方塊

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>已刪除的資源仍定義計量警示規則 

刪除 Azure 資源時，不會自動刪除相關聯的計量警示規則。 若要刪除與已刪除之資源相關聯的警示規則：

1. 開啟已刪除資源定義所在的資源群組
1. 在顯示資源的清單中，勾選 [顯示隱藏的類型] 核取方塊
1. 依類型 == **microsoft.insights/metricalerts** 篩選清單
1. 選取相關的警示規則，然後選取 [**刪除**]

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>每次符合我的條件時，都會進行計量警示

計量警示預設是具狀態的，因此，如果在指定的時間序列上已經引發警示，則不會引發其他警示。 如果您想要讓特定的計量警示規則成為無狀態，並在符合警示條件的每個評估上取得警示，請以程式設計方式建立警示規則 (例如，透過 [Resource Manager](./alerts-metric-create-templates.md)、 [PowerShell](/powershell/module/az.monitor/?view=azps-3.6.1)、 [REST](/rest/api/monitor/metricalerts/createorupdate)、 [CLI](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)) ，然後將 *autoMitigate* 屬性設定為 ' False '。

> [!NOTE] 
> 讓計量警示規則保持無狀態可防止引發的警示變成已解決，因此即使在不符合條件之後，引發的警示仍會維持在引發的狀態，直到30天的保留期限為止。

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>針對尚未發出的自訂度量定義警示規則

建立計量警示規則時，計量名稱會針對計量 [定義 API](/rest/api/monitor/metricdefinitions/list) 進行驗證，以確定它存在。 在某些情況下，您會想要在自訂計量上建立警示規則，即使在發出之前也是如此。 例如，使用 Resource Manager 範本建立 (時) 將發出自訂計量的 Application Insights 資源，以及監視該度量的警示規則。

若要避免在嘗試驗證自訂度量的定義時部署失敗，您可以在警示規則的 [準則] 區段中使用 *skipMetricValidation* 參數，這會導致略過計量驗證。 請參閱下列範例，以瞭解如何在 Resource Manager 範本中使用此參數。 如需詳細資訊，請參閱 [建立計量警示規則的完整 Resource Manager 範本範例](./alerts-metric-create-templates.md)。

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

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>透過 Azure 入口網站匯出度量警示規則的 Azure Resource Manager 範本

匯出計量警示規則的 Resource Manager 範本，可協助您瞭解其 JSON 語法和屬性，並且可以用來將未來的部署自動化。
1. 流覽至入口網站中的 [ **資源群組** ] 區段，然後選取包含規則的資源群組。
2. 在 [總覽] 區段中，選取 [ **顯示隱藏的類型** ] 核取方塊。
3. 在 **類型** 篩選器中，選取 [ *metricalerts*]。
4. 選取相關的警示規則以查看其詳細資料。
5. 在 [ **設定**] 底下，選取 [ **匯出範本**]。

## <a name="metric-alert-rules-quota-too-small"></a>計量警示規則配額太小

每個訂用帳戶允許的計量警示規則數目受限於 [配額限制](../service-limits.md)。

如果您已達到配額限制，下列步驟可能有助於解決此問題：
1. 請嘗試刪除或停用不再使用的計量警示規則。

2. 切換至使用監視多個資源的計量警示規則。 有了這項功能，單一警示規則就只能使用一個針對配額計數的警示規則來監視多個資源。 如需此功能和受支援資源類型的詳細資訊，請參閱[這裡](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。

3. 如果您需要增加配額限制，請開啟支援要求，並提供下列資訊：

    - 需要增加配額限制的訂用帳戶識別碼 (s) 
    - 配額增加的資源類型：計量 **警示** 或 **(傳統)** 的計量警示
    - 要求的配額限制

## <a name="check-total-number-of-metric-alert-rules"></a>檢查計量警示規則的總數

若要檢查計量警示規則目前的使用方式，請遵循下列步驟。

### <a name="from-the-azure-portal"></a>從 Azure 入口網站

1. 開啟 [警示] 畫面，然後按一下 [管理警示規則]
2. 使用 **訂** 用帳戶下拉式控制項篩選至相關的訂用帳戶
3. 請確定不要篩選至特定資源群組、資源類型或資源
4. 在 [**信號類型**] 下拉式清單控制項中，選取 [**計量**]
5. 確認 [**狀態**] 下拉式控制項已設定為 [**已啟用**]
6. 計量警示規則總數會顯示在警示規則清單上方

### <a name="from-api"></a>從 API

- PowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- REST API - [依訂用帳戶列出](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [az monitor metrics alert list](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>使用 Resource Manager 範本、REST API、PowerShell 或 Azure CLI 來管理警示規則

如果您在使用 Resource Manager 範本、REST API、PowerShell 或 Azure 命令列介面 (CLI) 建立、更新、抓取或刪除計量警示時遇到問題，下列步驟可能有助於解決此問題。

### <a name="resource-manager-templates"></a>Resource Manager 範本

- 檢閱[常見的 Azure 部署錯誤](../../azure-resource-manager/templates/common-deployment-errors.md)清單，並據以進行疑難排解
- 請參閱計量 [警示 Azure Resource Manager 範本範例](./alerts-metric-create-templates.md) ，以確保您正確地傳遞所有參數

### <a name="rest-api"></a>REST API

請參閱 [REST API 指南](/rest/api/monitor/metricalerts/) ，確認您已正確地傳遞所有參數

### <a name="powershell"></a>PowerShell

確定您使用的是適用于計量警示的正確 PowerShell Cmdlet：

- 計量警示的 PowerShell Cmdlet 可在 [Az.Monitor 模組](/powershell/module/az.monitor/?view=azps-3.6.1)中取得
- 針對新的 (非傳統) 計量警示，請務必使用結尾為 ' V2 ' 的 Cmdlet (例如，新增 [-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2?view=azps-3.6.1)) 

### <a name="azure-cli"></a>Azure CLI

請確定您使用的是適用于計量警示的正確 CLI 命令：

- 計量警示的 CLI 命令會以 `az monitor metrics alert` 開頭。 請檢閱 [Azure CLI 參考](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)以了解語法。
- 您會看到[說明如何使用計量警示 CLI 的範例](./alerts-metric.md#with-azure-cli)
- 若要針對自訂計量發出警示，請務必在計量名稱前面加上相關的計量命名空間：NAMESPACE.METRIC

### <a name="general"></a>一般

- 如果您收到 `Metric not found` 錯誤：

   - 針對平臺計量：請確定您 **使用的計量名稱來自** [Azure 監視器支援的計量頁面](./metrics-supported.md)，而不是計量 **顯示名稱**

   - 針對自訂計量：確定已發出計量 (您無法針對尚未存在) 的自訂計量建立警示規則，而且您要提供自訂計量的命名空間 (在 [此](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric) 看到 Resource Manager 範本範例) 

- 如果您要建立 [記錄的計量警示](./alerts-metric-logs.md)，請確定包含適當的相依性。 請參閱[範例範本](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs)。

- 如果您要建立包含多個準則的警示規則，請注意下列條件約束：

   - 在每個準則中，您只能為每個維度選取一個值
   - 您不能使用 "\*" 做為維度值
   - 當不同使用準則中設定的度量支援相同維度時，您必須以相同方式為所有這些計量明確設定所設定的維度值 (請參閱 [此處](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria) 的 Resource Manager 範本範例) 


## <a name="no-permissions-to-create-metric-alert-rules"></a>沒有建立計量警示規則的許可權

若要建立計量警示規則，您必須具備下列許可權：

- 警示規則的目標資源的讀取權限
- 建立警示規則之資源群組的寫入權限 (如果您要從 Azure 入口網站建立警示規則，則預設會在目標資源所在的相同資源群組中建立警示規則) 
- 與警示規則相關聯之任何動作群組的讀取權限 (如果適用) 


## <a name="naming-restrictions-for-metric-alert-rules"></a>計量警示規則的命名限制

針對計量警示規則名稱，請考慮下列限制：

- 無法變更計量警示規則名稱 (在建立後重新命名) 
- 計量警示規則名稱在資源群組內必須是唯一的
- 計量警示規則名稱不能包含下列字元： * # & +：  < > ？ @ % { } \ / 
- 計量警示規則名稱不能以空格或句號結尾

> [!NOTE] 
> 如果警示規則名稱包含的字元不是字母或數位 (例如：空格、標點符號或符號) ，則這些字元可能會在特定用戶端抓取時進行 URL 編碼。

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>在具有多個條件的度量警示規則中使用維度時的限制

計量警示支援多維度計量的警示，並支援定義多項條件 (每個警示規則) 最多5個條件。

在包含多個條件的警示規則中使用維度時，請考慮下列條件約束：
- 在每個條件中，您只能為每個維度選取一個值。
- 您無法使用 [選取所有目前及未來的值] 選項 (選取 \*) 。
- 當在不同條件下設定的度量支援相同的維度時，您必須以相同的方式，針對) 相關條件 (的所有計量明確設定所設定的維度值。
例如：
    - 請考慮在儲存體帳戶上定義的度量警示規則，並監視兩個條件：
        * **交易** 總數 > 5
        * 平均 **successe2elatency 時間** > 250 毫秒
    - 我想要更新第一個條件，只監視 **ApiName** 維度等於 *"GetBlob"* 的交易
    - 由於 **交易** 和 **successe2elatency 時間** 計量都支援 **ApiName** 維度，因此我必須同時更新這兩個條件，而且兩者都必須使用 *"GetBlob"* 值來指定 **ApiName** 維度。

## <a name="setting-the-alert-rules-period-and-frequency"></a>設定警示規則的期間和頻率

我們建議您選擇大於 *評估頻率* 的 *匯總資料細微性 (期間)* ，以降低在下列情況下遺漏新增時間序列第一次評估的可能性：
-   監視多個維度的度量警示規則-新增維度值組合時
-   監視多個資源的計量警示規則-將新資源新增至範圍時
-   計量警示規則，可監視未持續發出 (稀疏計量) 的計量–當計量在一段時間超過24小時未發出時發出時

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>動態臨界值框線似乎不符合資料

如果最近變更計量的行為，則變更不一定會反映在動態閾值邊界內 (的上限和下限) ，因為這些變更是根據過去10天的度量資料來計算。 當您針對指定的計量查看動態閾值框線時，請務必查看上周的計量趨勢，而不只是最近的時數或天數。

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>為什麼動態閾值未偵測到每週季節性？

若要識別每週季節性，動態閾值模型至少需要三周的歷程記錄資料。 一旦有足夠的歷程記錄資料可供使用之後，就會識別出任何存在於度量資料中的每週季節性，並據此調整模型。 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>即使計量永遠有正值，動態閾值仍會顯示計量的負下限

當計量出現大量波動時，動態閾值將會建立更廣泛的計量值周圍模型，這可能會導致下方的框線小於零。 具體而言，在下列情況下可能會發生這種情況：
1. 敏感度設定為 low 
2. 中間值接近零
3. 度量表現出具有高變異數的異常行為 (資料中有尖峰或下降) 

當下限的值為負值時，這表示它的合理是根據度量的異常行為，達到零值。 您可以考慮選擇較高的敏感度或較大的 *匯總細微性 (期間)* 來使模型較不敏感，或使用 [ *略過資料* ] 選項，從用來建立模型的歷程記錄資料中排除最近的 irregulaity。

## <a name="next-steps"></a>後續步驟

- 如需有關警示和通知的一般疑難排解資訊，請參閱 [Azure 監視器警示中的疑難排解問題](alerts-troubleshoot.md)。
