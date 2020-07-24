---
title: 在 Linux 擴展集範本中搭配使用 Azure 自動調整與來賓計量
description: 了解如何在 Linux 虛擬機器擴展集範本中使用客體計量自動調整規模
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/26/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 549f8fbc1e3acf435011f223faeb5b8240f0c55d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080415"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>在 Linux 擴展集範本中使用客體計量自動調整規模

Azure 中有兩種廣泛的計量類型，從 Vm 和擴展集收集：主機計量和來賓計量。 概括而言，如果您想要使用標準 CPU、磁片和網路計量，那麼主機計量就很適合。 不過，如果您需要更大的計量選取範圍，則應該查閱來賓計量。

主機計量不需要額外的安裝，因為它們是由主機 VM 所收集，而來賓計量則會要求您在來賓 VM 中安裝[Windows Azure 診斷擴充](../virtual-machines/extensions/diagnostics-template.md)功能或[Linux Azure 診斷延伸](../virtual-machines/extensions/diagnostics-linux.md)模組。 使用客體計量而非主機計量的一個常見原因是，客體計量會提供比主機計量更大的計量選取範圍。 記憶體耗用量計量即為一例，這類計量只能透過客體計量使用。 [這裡](../azure-monitor/platform/metrics-supported.md)會列出支援的主機度量，而常用的客體計量則列於[這裡](../azure-monitor/platform/autoscale-common-metrics.md)。 本文說明如何修改[基本可行擴展集範本](virtual-machine-scale-sets-mvss-start.md)，以使用以 Linux 擴展集的來賓計量為基礎的自動調整規則。

## <a name="change-the-template-definition"></a>變更範本定義

在[上一篇文章中](virtual-machine-scale-sets-mvss-start.md)，我們建立了基本的擴展集範本。 我們現在會使用先前的範本並加以修改，以建立範本，以使用以來賓計量為基礎的自動調整來部署 Linux 擴展集。

首先，新增 `storageAccountName` 和 `storageAccountSasToken` 的參數。 診斷代理程式會將計量資料儲存於此儲存體帳戶的[表格](../cosmos-db/tutorial-develop-table-dotnet.md)中。 從 Linux 診斷代理程式 3.0 版開始，不再支援使用儲存體存取金鑰。 請改用 [SAS 權杖](../storage/common/storage-sas-overview.md)。

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

接下來，修改擴展集 `extensionProfile` 以包含診斷擴充功能。 在此設定中，指定要從中收集計量之擴展集的資源識別碼，以及要用來儲存計量的儲存體帳戶和 SAS 權杖。 指定彙總計量資訊的頻率 (在此案例中為每隔一分鐘)，以及要追蹤的計量 (在此案例中為已使用記憶體的百分比)。 如需此設定及已使用記憶體的百分比以外之計量的詳細資訊，請參閱[這份文件](../virtual-machines/extensions/diagnostics-linux.md)。

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
