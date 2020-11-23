---
title: '使用資料收集規則在適用於 VM 的 Azure 監視器來賓健康情況中設定監視 (預覽) '
description: 說明如何使用 Resource Manager 範本，以大規模的適用於 VM 的 Azure 監視器來賓健康情況來修改預設監視。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 2bbc57d8ddc004c1926da7e0037efdc1fcf2d76e
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95318094"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-using-data-collection-rules-preview"></a>使用資料收集規則在適用於 VM 的 Azure 監視器來賓健康情況中設定監視 (預覽) 
[適用於 VM 的 Azure 監視器來賓健康狀態](vminsights-health-overview.md) 可讓您依定期取樣的一組效能測量所定義，來查看虛擬機器的健康情況。 本文說明如何使用資料收集規則，在多部虛擬機器之間修改預設監視。


## <a name="monitors"></a>監視器
虛擬機器的健全狀況狀態取決於每個監視的 [健全狀況匯總](vminsights-health-overview.md#health-rollup-policy) 套件。 適用於 VM 的 Azure 監視器來賓健康情況下有兩種監視類型，如下表所示。

| 監視 | 說明 |
|:---|:---|
| 單位監視 | 測量資源或應用程式的某些層面。 這可能是檢查效能計數器以判斷資源的效能或可用性。 |
| 彙總監視器 | 將多個監視器分組，以提供單一匯總的健全狀況狀態。 匯總監視可包含一或多個單位監視和其他匯總監視。 |

適用於 VM 的 Azure 監視器來賓健康情況和其設定所使用的一組監視，無法直接變更。 您可以建立 [覆寫](#overrides) ，但會修改預設設定的行為。 覆寫是在資料收集規則中定義。 您可以建立多個資料集合規則，其中每個都包含多重覆寫，以達成所需的監視設定。

## <a name="monitor-properties"></a>監視屬性
下表說明可在每個監視器上設定的屬性。

| 屬性 | 監視器 | 說明 |
|:---|:---|:---|
| 啟用 | Aggregate<br>單位 | 若為 true，則會計算狀態監視器，並貢獻虛擬機器的健全狀況。 它可能會觸發警示警示已啟用。 |
| 警示 | Aggregate<br>單位 | 若為 true，當監視移至狀況不良狀態時，就會觸發警示。 若為 false，監視器的狀態仍會導致可能觸發警示的虛擬機器健全狀況。 |
| 警告 | 單位 | 警告狀態的準則。 如果沒有，則監視器將永遠不會進入警告狀態。 |
| 重大 | 單位 | 重大狀態的準則。 如果沒有，則監視器將永遠不會進入重大狀態。 |
| 評估頻率 | 單位 | 評估健全狀況狀態的頻率。 |
| 回顧 | 單位 | 回顧視窗的大小（以秒為單位）。 如需詳細說明，請參閱 [monitorConfiguration 元素](#monitorconfiguration-element) 。 |
| 評估類型 | 單位 | 定義要從範例集合中使用的值。 如需詳細說明，請參閱 [monitorConfiguration 元素](#monitorconfiguration-element) 。 |
| 最小取樣 | 單位 | 要用來計算值的最小值數目。 |
| 最大範例 | 單位 | 要用來計算值的最大值數目。 |


## <a name="default-configuration"></a>預設組態
下表列出每個監視的預設設定。 此預設設定無法直接變更，但您可以定義 [覆寫](#overrides) ，以修改某些虛擬機器的監視設定。


| 監視 | 啟用 | 警示 | 警告 | 重大 | 評估頻率 | 回顧 | 評估類型 | 最小取樣 | 樣本數上限 |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| CPU 使用率  | True | False | None | \> 90%    | 60 秒 | 240秒 | 最小值 | 2 | 3 |
| 可用的記憶體 | True | False | None | \< 100 MB | 60 秒 | 240秒 | 最大值 | 2 | 3 |
| 檔案系統      | True | False | None | \< 100 MB | 60 秒 | 120秒 | 最大值 | 1 | 1 |


## <a name="overrides"></a>覆寫
覆 *寫* 會變更監視的一或多個屬性。 例如，覆寫可以停用預設啟用的監視、定義監視的警告準則，或修改監視的重大閾值。 

覆寫是在 [資料集合規則中定義 (DCR) ](../platform/data-collection-rule-overview.md)。 您可以使用不同的覆寫集合來建立多個 Dcr，並將它們套用至多個虛擬機器。 您可以依照 [設定 Azure 監視器代理程式 (preview) 的資料收集 ](../platform/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations)中所述，建立關聯，以將 DCR 套用至虛擬機器。


## <a name="multiple-overrides"></a>多重覆寫

單一監視器可能會有多個覆寫。 如果覆寫定義不同的屬性，則產生的設定是所有覆寫的組合。

例如， `memory|available` 監視器預設不會指定警告閾值或啟用警示。 請考慮套用到此監視的下列覆寫：

- 覆寫1定義 `alertConfiguration.isEnabled` 屬性值為 `true`
- 覆寫2定義 `monitorConfiguration.warningCondition` ，其臨界值條件為 `< 250` 。

產生的設定會是一種監視器，它會在記憶體低於250Mb 時進入警告健全狀況狀態，並建立嚴重性2警示，而且當可用記憶體低於100Mb 時，也會進入重大健全狀況狀態，並建立警示嚴重性 1 (或將現有的警示從嚴重性2變更為1（如果已存在) ）。

如果兩個覆寫在相同的監視器上定義了相同的屬性，則會優先使用一個值。 覆寫會根據其 [範圍](#scopes-element)套用（從最普遍到最特定）。 這表示，最特定的覆寫會有最大的套用機會。 特定順序如下所示：

1. 全球 
2. 訂用帳戶
3. 資源群組
4. 虛擬機器。 

如果相同範圍層級上的多個覆寫在相同的監視器上定義相同的屬性，則會以它們出現在 DCR 中的順序套用。 如果覆寫在不同的 Dcr 中，則會依 DCR 資源識別碼的字母順序套用。


## <a name="data-collection-rule-configuration"></a>資料收集規則設定
下列各節將說明資料集合規則中定義覆寫的 JSON 元素。 [範例資料收集規則](#sample-data-collection-rule)中提供完整的範例。

## <a name="extensions-structure"></a>擴充功能結構
來賓健全狀況會實作為 Azure 監視器代理程式的延伸模組，因此覆寫是在 `extensions` 資料收集規則的元素中定義。 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| 元素 | 必要 | 描述 |
|:---|:---|:---|
| `name` | 是 | 擴充功能的使用者定義字串。 |
| `streams` | 是 | 來賓健全狀況資料將傳送至其中的資料流程清單。 這必須包含 **Microsoft HealthStateChange**。  |
| `extensionName` | 是 | 延伸模組的名稱。 這必須是 **HealthExtension**。 |
| `extensionSettings` | 是 | 要套用 `healthRuleOverride` 至預設設定的元素陣列。 |


## <a name="extensionsettings-element"></a>extensionSettings 元素
包含擴充功能的設定。

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| 元素 | 必要 | 描述 |
|:---|:---|:---|
| `schemaVersion` | 是 | 由 Microsoft 定義的字串，表示預期的元素架構。 目前必須設定為1。0 |
| `contentVersion` | 否 | 由使用者定義的字串，用以追蹤不同版本的健康情況設定（如有必要）。 |
| `healthRuleOverrides` | 是 | 要套用 `healthRuleOverride` 至預設設定的元素陣列。 |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides 元素
包含一或多個專案 `healthRuleOverride` ，而每個元素都會定義覆寫。

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| 元素 | 必要 | 描述 |
|:---|:---|:---|
| `scopes` | 是 | 一或多個範圍的清單，指定此覆寫適用的虛擬機器。 雖然 DCR 與虛擬機器相關聯，但虛擬機器必須落在要套用覆寫的範圍內。 |
| `monitors` | 是 | 定義哪些監視器將接收此覆寫的一或多個字串清單。  |
| `monitorConfiguration` | 否 | 監視的設定，包括健全狀況狀態及其計算方式。 |
| `alertConfiguration` | 否 | 監視監視的警示。 |
| `isEnabled` | 否 | 控制是否啟用監視。 停用的監視會切換為特殊 *停用* 的健康狀態，並停用狀態，除非重新啟用。 如果省略，監視將會從階層中的父監視繼承其狀態。 |


## <a name="scopes-element"></a>scope 元素
每個覆寫都有一或多個範圍，可定義要套用覆寫的虛擬機器。 範圍可以是訂用帳戶、資源群組或單一虛擬機器。 即使覆寫位於與特定虛擬機器相關聯的 DCR 中，如果虛擬機器位於覆寫的其中一個範圍內，它只會套用至該虛擬機器。 這可讓您廣泛地將較少的 Dcr 與一組 Vm 產生關聯，但可讓您更精確地控制 DCR 本身內每個覆寫的指派。 您可能會想要使用原則來建立一組小型的 Dcr 並將它們與一組虛擬機器進行關聯，同時使用 scope 元素針對這些虛擬機器的不同子集指定健全狀況監視器覆寫。

下表顯示不同範圍的範例。

| 影響範圍 | 範例 |
|:---|:---|
| 單一虛擬機器 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| 資源群組中的所有虛擬機器 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| 一個訂用帳戶中的所有虛擬機器 | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| 與資料收集規則相關聯的所有虛擬機器 | `*` |


## <a name="monitors-element"></a>monitor 元素
一或多個字串的清單，定義健全狀況階層中的哪些監視器會接收此覆寫。 每個專案都可以是監視器名稱或類型名稱，以符合將接收此覆寫的一或多個監視器。 

```json
"monitors": [
    "<monitor name>"
 ],
```



下表列出目前可用的監視名稱。

| 類型名稱 | Name | 描述 |
|:---|:---|:---|
| root | root | 代表虛擬機器健康情況的最上層監視。 | |
| cpu 使用率 | cpu 使用率 | CPU 使用率監視器。 | |
| 邏輯磁片 | 邏輯磁片 | Windows 虛擬機器上所有受監視磁片的健全狀況狀態匯總監視。 | |
| 邏輯磁片\|* | 邏輯磁片 \| C：<br>邏輯磁片 \| D： | 在 Windows 虛擬機器上匯總監視追蹤指定磁片的健全狀況。 | 
| 邏輯磁片 \| * \| 可用空間 | 邏輯磁片 \| C： \| 可用空間<br>邏輯磁片 \| D： \| 可用空間 | Windows 虛擬機器上的磁片可用空間監視。 |
| filesystems | filesystems | 監視 Linux 虛擬機器上所有檔案系統健全狀況的匯總監視。 |
| 檔案系統\|* | 檔案系統\|/<br>檔案系統 \| /mnt | Linux 虛擬機器檔案系統的匯總監視追蹤健全狀況。 | filesystems|/var/log |
| 檔案系統 \| * \| 可用空間 | 檔案系統 \| / \| 可用空間<br>檔案系統 \| /mnt \| 可用空間 | Linux 虛擬機器檔案系統上的磁片可用空間監視。 | 
| 記憶體 | 記憶體 | 虛擬機器記憶體健全狀況的匯總監視。 | |
| 可用的記憶體 \|| 可用的記憶體 \| | 監視虛擬機器上可用記憶體的追蹤。 | |


## <a name="alertconfiguration-element"></a>alertConfiguration 元素
指定是否應該從監視建立警示。

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| 項目 | 強制性 | 說明 | 
|:---|:---|:---|
| `isEnabled` | 否 | 如果設定為 true，監視將會在切換至重大或警告狀態時產生警示，並在返回狀況良好狀態時解決警示。 如果為 false 或省略，則不會產生警示。  |


## <a name="monitorconfiguration-element"></a>monitorConfiguration 元素
僅適用于單位監視器。 定義監視的設定，包括健全狀況狀態以及其計算方式。

參數會定義演算法，以計算要與臨界值比較的度量值。 此監視器不會根據基礎計量中的一個資料取樣，而是會評估在時間範圍內收到的數個計量範例，而不是從評估時間到 `lookbackSec` 前。 系統會考慮該時間範圍內收到的所有範例，如果樣本計數大於 `maxSamples` ，則會忽略上述較舊 `maxSamples` 的範例。 

如果回顧間隔中的樣本數較少 `minSamples` ，監視將會切換至 *未知* 的健康狀態，指出沒有足夠的資料可做出有關基礎度量健康情況的明智決策。 如果有更多可用的範例 `minSamples` ，則由 evaluationType 參數指定的彙總函式會在集合上執行，以計算單一值。


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| 項目 | 強制性 | 說明 | 
|:---|:---|:---|
| `evaluationFrequencySecs` | 否 | 定義健全狀況狀態評估的頻率。 每個監視器都會在代理程式啟動時進行評估，並在之後依此參數定義的定期間隔進行評估。 |
| `lookbackSecs`   | 否 | 回顧視窗的大小（以秒為單位）。 |
| `evaluationType` | 否 | `min` –從整個樣本集取得最小值<br>`max` -從整個樣本集取得最大值<br>`avg` –平均取樣設定值<br>`all` –將集合中的每個單一值與臨界值進行比較。 只有在集合中的所有樣本都符合臨界值條件時，監視切換狀態。 |
| `minSamples`     | 否 | 要用來計算值的最小值數目。 |
| `maxSamples`     | 否 | 要用來計算值的最大值數目。 |
| `warningCondition`  | 否 | 警告條件的臨界值和比較邏輯。 |
| `criticalCondition` | 否 | 重大條件的臨界值和比較邏輯。 |


## <a name="warningcondition-element"></a>warningCondition 元素
定義警告條件的臨界值和比較邏輯。 如果未包含此元素，則監視器永遠不會切換至警告健全狀況狀態。

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| 屬性 | 強制性 | 說明 | 
|:---|:---|:---|
| `isEnabled` | 否 | 指定是否啟用條件。 如果設定為 **false**，即使可以設定臨界值和運算子屬性，還是會停用條件。 |
| `threshold` | 否 | 定義要比較評估值的臨界值。 |
| `operator`  | 否 | 定義要在臨界值運算式中使用的比較運算子。 可能的值： >、<、>=、<=、= =。 |


## <a name="criticalcondition-element"></a>criticalCondition 元素
定義重大條件的臨界值和比較邏輯。 如果未包含此元素，則監視器將永遠不會切換至重大健全狀況狀態。

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| 屬性 | 強制性 | 說明 | 
|:---|:---|:---|
| `isEnabled` | 否 | 指定是否啟用條件。 如果設定為 **false**，即使可以設定臨界值和運算子屬性，還是會停用條件。 |
| `threshold` | 否 | 定義要比較評估值的臨界值。 |
| `operator`  | 否 | 定義要在臨界值運算式中使用的比較運算子。 可能的值： >、<、>=、<=、= =。 |

## <a name="sample-data-collection-rule"></a>範例資料收集規則
下列範例資料收集規則顯示用來設定監視的覆寫範例。


```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [資料收集規則](../platform/data-collection-rule-overview.md)。