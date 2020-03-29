---
title: 在 Linux 比例集範本中使用 Azure 自動縮放和來賓指標
description: 了解如何在 Linux 虛擬機器擴展集範本中使用客體計量自動調整規模
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 88f839b281e4d345b012a7e6acff3770dc536045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271960"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>在 Linux 擴展集範本中使用客體計量自動調整規模

Azure 中有兩種廣泛的指標類型是從 VM 和縮放集收集的：主機指標和來賓指標。 在高級別上，如果要使用標準 CPU、磁片和網路指標，則主機指標非常適合。 但是，如果您需要更多指標選擇，則應研究來賓指標。

主機指標不需要其他設置，因為它們由主機 VM 收集，而來賓指標要求您在來賓 VM 中安裝[Windows Azure 診斷擴展](../virtual-machines/windows/extensions-diagnostics-template.md)或[Linux Azure 診斷擴展](../virtual-machines/linux/diagnostic-extension.md)。 使用客體計量而非主機計量的一個常見原因是，客體計量會提供比主機計量更大的計量選取範圍。 記憶體耗用量計量即為一例，這類計量只能透過客體計量使用。 [這裡](../azure-monitor/platform/metrics-supported.md)會列出支援的主機度量，而常用的客體計量則列於[這裡](../azure-monitor/platform/autoscale-common-metrics.md)。 本文演示如何修改[基本可行的比例集範本](virtual-machine-scale-sets-mvss-start.md)，以便根據 Linux 比例集的來賓指標使用自動縮放規則。

## <a name="change-the-template-definition"></a>變更範本定義

在前面的一[篇文章中](virtual-machine-scale-sets-mvss-start.md)，我們創建了一個基本的比例集範本。 現在，我們將使用該早期範本對其進行修改，以創建一個範本，該範本使用基於來賓指標的自動縮放部署 Linux 比例集。

首先，新增 `storageAccountName` 和 `storageAccountSasToken` 的參數。 診斷代理程式會將計量資料儲存於此儲存體帳戶的[表格](../cosmos-db/table-storage-how-to-use-dotnet.md)中。 從 Linux 診斷代理程式 3.0 版開始，不再支援使用儲存體存取金鑰。 請改用 [SAS 權杖](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

接下來，修改擴展集 `extensionProfile` 以包含診斷擴充功能。 在此設定中，指定要從中收集計量之擴展集的資源識別碼，以及要用來儲存計量的儲存體帳戶和 SAS 權杖。 指定彙總計量資訊的頻率 (在此案例中為每隔一分鐘)，以及要追蹤的計量 (在此案例中為已使用記憶體的百分比)。 如需此設定及已使用記憶體的百分比以外之計量的詳細資訊，請參閱[這份文件](../virtual-machines/linux/diagnostic-extension.md)。

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

最後，新增 `autoscaleSettings` 資源，以根據這些計量來設定自動調整規模。 此資源含有 `dependsOn` 子句，其會參考擴展集以確定擴展集存在，然後再嘗試自動調整其規模。 如果您選擇不同的計量來自動調整規模，可以使用來自診斷擴充功能設定的 `counterSpecifier`，作為自動調整規模設定中的 `metricName`。 如需自動調整規模設定的詳細資訊，請參閱[自動調整規模的最佳做法](../azure-monitor/platform/autoscale-best-practices.md)和 [Azure 監視器 REST API 參考文件](/rest/api/monitor/autoscalesettings) \(英文\)。

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>後續步驟

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
