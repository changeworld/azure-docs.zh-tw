---
title: 資源命名限制
description: 顯示命名 Azure 資源的規則和限制。
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: a4c3bf58bf5e84051b1e21863d2d581af5fa9f23
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447518"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Azure 資源的命名規則和限制

本文摘要說明 Azure 資源的命名規則和限制。 如需如何命名資源的建議，請參閱[建議的命名和標記慣例](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

本文依資源提供者命名空間列出資源。 如需資源提供者如何與 Azure 服務相符的清單，請參閱 [azure 服務的資源提供者](azure-services-resource-providers.md)。

除非在 [有效字元] 資料行中特別註明，否則資源名稱不區分大小寫。

在下表中，英數字元一詞是指：

* **a** 到 **z** (小寫字母)
* **A** 到 **Z** (大寫字母)
* **0** 到 **9** (數字)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | servers | 資源群組 | 3-63 | 小寫字母和數字。<br><br>以小寫字母開頭。 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 服務 | 全域 | 1-50 | 英數位元和連字號。<br><br>以字母開頭，並以英數字元結尾。 |
> | service / apis | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / issues | api | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / issues / attachments | 問題 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / issues / comments | 問題 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / operations | api | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / operations / tags | operation (作業) | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / releases | api | 1-80 | 英數字元、底線和連字號。<br><br>以字母或底線開頭及結尾。 |
> | service / apis / schemas | api | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / tagDescriptions | api | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / tags | api | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / api-version-sets | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / authorizationServers | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / backends | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / certificates | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / diagnostics | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / groups | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / groups / users | 群組 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / identityProviders | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / loggers | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / notifications | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / notifications / recipientEmails | 通知 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / openidConnectProviders | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / policies | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / products | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / products / apis | product | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / products / groups | product | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / products / tags | product | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / properties | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / subscriptions | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / tags | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / templates | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / users | 服務 | 1-256 | 不能使用：<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | configurationStores | 資源群組 | 5-50 | 英數字元、底線和連字號。 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | locks | 指派範圍 | 1-90 | 英數字元、句號、底線、連字號和括弧。<br><br>不能以句號結尾。 |
> | policyAssignments | 指派範圍 | 1-128 (顯示名稱)<br><br>1-64 資源名稱 | 顯示名稱可包含任何字元。<br><br>資源名稱不能包含 `%`，且不能以句號或空格結尾。 |
> | policyDefinitions | 定義範圍 | 1-128 (顯示名稱)<br><br>1-64 資源名稱 | 顯示名稱可包含任何字元。<br><br>資源名稱不能包含 `%`，且不能以句號或空格結尾。 |
> | policySetDefinitions | 定義範圍 | 1-128 (顯示名稱)<br><br>1-64 資源名稱<br><br>1-24 管理群組範圍的資源名稱 | 顯示名稱可包含任何字元。<br><br>資源名稱不能包含 `%`，且不能以句號或空格結尾。  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | automationAccounts | 資源群組 | 6-50 | 英數字元和連字號。<br><br>以字母開頭，並以英數字元結尾。 |
> | automationAccounts / certificates | 自動化帳戶 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格結尾。  |
> | automationAccounts / connections | 自動化帳戶 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格結尾。 |
> | automationAccounts / credentials | 自動化帳戶 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格結尾。 |
> | automationAccounts / runbooks | 自動化帳戶 | 1-63 | 英數字元、底線和連字號。<br><br>以字母開頭。  |
> | automationAccounts / schedules | 自動化帳戶 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格結尾。 |
> | automationAccounts / variables | 自動化帳戶 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格結尾。 |
> | automationAccounts / watchers | 自動化帳戶 | 1-63 |  英數字元、底線和連字號。<br><br>以字母開頭。 |
> | automationAccounts / webhooks | 自動化帳戶 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格結尾。 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | batchAccounts | 區域 | 3-24 | 小寫字母和數字。 |
> | batchAccounts / applications | Batch 帳戶 | 1-64 | 英數字元、底線和連字號。 |
> | batchAccounts / certificates | Batch 帳戶 | 5-45 | 英數字元、底線和連字號。 |
> | batchAccounts / pools | Batch 帳戶 | 1-64 | 英數字元、底線和連字號。 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | blockchainMembers | 全域 | 2-20 | 小寫字母和數字。<br><br>以小寫字母開頭。 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | botServices | 全域 | 2-64 |  英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 |
> | botServices / channels | 機器人服務 | 2-64 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 |
> | botServices / Connections | 機器人服務 | 2-64 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 |
> | enterpriseChannels | 資源群組 | 2-64 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | Redis | 全域 | 1-63 | 英數字元和連字號。<br><br>以英數字元開頭及結尾。 不允許連續的連字號。 |
> | Redis / firewallRules | Redis | 1-256 | 英數字元 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 設定檔 | 資源群組 | 1-260 | 英數字元和連字號。<br><br>以英數字元開頭及結尾。 |
> | profiles / endpoints | 全域 | 1-50 | 英數字元和連字號。<br><br>以英數字元開頭及結尾。 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | certificateOrders | 資源群組 | 3-30 | 英數字元。 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | accounts | 資源群組 | 2-64 | 英數字元和連字號。<br><br>以英數字元開頭及結尾。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | availabilitySets | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | diskEncryptionSets | 資源群組 | 1-80 | 英數字元和底線。 |
> | disks | 資源群組 | 1-80 | 英數字元和底線。 |
> | galleries | 資源群組 | 1-80 | 英數字元和句號。<br><br>以英數字元開頭及結尾。 |
> | galleries / applications | gallery | 1-80 | 英數字元、連字號和句號。<br><br>以英數字元開頭及結尾。 |
> | galleries / applications / versions | 應用程式 | 32 位元整數 | 數字和句號。 |
> | galleries / images | gallery | 1-80 | 英數字元、連字號和句號。<br><br>以英數字元開頭及結尾。 |
> | galleries / images / versions | image | 32 位元整數 | 數字和句號。 |
> | images | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | snapshots | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | virtualMachines | 資源群組 | 1-15 (Windows)<br>1-64 (Linux)<br><br>請參閱下方注意事項。 | 無法使用空格或下列字元：<br> `\/"'[]:|<>+=;,?*@&`<br><br>不能以底線開頭。 不能以句號或連字號結尾。 |
> | virtualMachineScaleSets | 資源群組 | 1-15 (Windows)<br>1-64 (Linux)<br><br>請參閱下方注意事項。 | 無法使用空格或下列字元：<br> `\/"'[]:|<>+=;,?*@&`<br><br>不能以底線開頭。 不能以句號或連字號結尾。 |

> [!NOTE]
> Azure 虛擬機器有兩個不同的名稱：資源名稱和主機名稱。 當在入口網站中建立虛擬機器時，會針對這兩個名稱使用相同的值。 上表中的限制適用於主機名稱。 實際資源名稱最多可以有 64 個字元。

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | containerGroups | 資源群組 | 1-63 | 小寫字母、數字和連字號。<br><br>不能以連字號開頭或結尾。 不允許連續的連字號。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | registries | 全域 | 5-50 | 英數字元。 |
> | registries / buildTasks | 登錄 | 5-50 | 英數字元。 |
> | registries / buildTasks / steps | 建置工作 | 5-50 | 英數字元。 |
> | registries / replications | 登錄 | 5-50 | 英數字元。 |
> | registries / scopeMaps | 登錄 | 5-50 | 英數字元、連字號和底線。 |
> | registries / tasks | 登錄 | 5-50 | 英數字元、連字號和底線。 |
> | registries / tokens | 登錄 | 5-50 | 英數字元、連字號和底線。 |
> | registries / webhooks | 登錄 | 5-50 | 英數字元。 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | managedClusters | 資源群組 | 1-63 | 英數字元、底線和連字號。<br><br>以英數字元開頭及結尾。 |
> | openShiftManagedClusters | 資源群組 | 1-30 | 英數字元。 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | hubs | 資源群組 | 1-64 | 英數字元。<br><br>以字母開頭。  |
> | hubs / authorizationPolicies | 中樞 | 1-50 | 英數字元、底線和句號。<br><br>以英數字元開頭及結尾。 |
> | hubs / connectors | 中樞 | 1-128 | 英數字元和底線。<br><br>以字母開頭。 |
> | hubs / connectors / mappings | 連接器 | 1-128 | 英數字元和底線。<br><br>以字母開頭。 |
> | hubs / interactions | 中樞 | 1-128 | 英數字元和底線。<br><br>以字母開頭。 |
> | hubs / kpi | 中樞 | 1-512 | 英數字元和底線。<br><br>以字母開頭。 |
> | hubs / links | 中樞 | 1-512 | 英數字元和底線。<br><br>以字母開頭。 |
> | hubs / predictions | 中樞 | 1-512 | 英數字元和底線。<br><br>以字母開頭。 |
> | hubs / profiles | 中樞 | 1-128 | 英數字元和底線。<br><br>以字母開頭。 |
> | hubs / relationshipLinks | 中樞 | 1-512 | 英數字元和底線。<br><br>以字母開頭。 |
> | hubs / relationships | 中樞 | 1-512 | 英數字元和底線。<br><br>以字母開頭。 |
> | hubs / roleAssignments | 中樞 | 1-128 | 英數字元和底線。<br><br>以字母開頭。 |
> | hubs / views | 中樞 | 1-512 | 英數字元和底線。<br><br>以字母開頭。 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | associations | 資源群組 | 1-180 | 不能使用：<br>`%&\\?/`<br><br>不能以句號或空格結尾。 |
> | resourceProviders | 資源群組 | 3-64 | 不能使用：<br>`%&\\?/`<br><br>不能以句號或空格結尾。 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | jobs | 資源群組 | 3-24 | 英數字元、連字號、底線和句號。 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | workspaces | 資源群組 | 3-30 | 英數字元、底線和連字號 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | factories | 全域 | 3-63 | 英數字元和連字號。<br><br>以英數字元開頭及結尾。 |
> | factories / dataflows | 工廠 | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以英數字元開頭。 |
> | factories / datasets | 工廠 | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以英數字元開頭。 |
> | factories / integrationRuntimes | 工廠 | 3-63 | 英數字元和連字號。<br><br>以英數字元開頭及結尾。 |
> | factories / linkedservices | 工廠 | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以英數字元開頭。 |
> | factories / pipelines | 工廠 | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以英數字元開頭。 |
> | factories / triggers | 工廠 | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以英數字元開頭。 |
> | factories / triggers / rerunTriggers | 觸發程序 (trigger) | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以英數字元開頭。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | accounts | 全域 | 3-24 | 小寫字母和數字。 |
> | accounts / computePolicies | account | 3-60 | 英數字元、連字號和底線。 |
> | accounts / dataLakeStoreAccounts | account | 3-24 | 小寫字母和數字。 |
> | accounts / firewallRules | account | 3-50 | 英數字元、連字號和底線。 |
> | accounts / storageAccounts | account | 3-60 | 英數字元、連字號和底線。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | accounts | 全域 | 3-24 | 小寫字母和數字。 |
> | accounts / firewallRules | account | 3-50 | 英數字元、連字號和底線。 |
> | accounts / virtualNetworkRules | account | 3-50 | 英數字元、連字號和底線。 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 服務 | 資源群組 | 2-62 | 英數字元、連字號、句號和底線。<br><br>以英數字元開頭。 |
> | services / projects | 服務 | 2-57 | 英數字元、連字號、句號和底線。<br><br>以英數字元開頭。 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | servers | 全域 | 3-63 | 小寫字母、連字號和數字。<br><br>不能以連字號開頭或結尾。 |
> | servers / databases | servers | 1-63 | 英數字元和連字號。 |
> | servers / firewallRules | servers | 1-128 | 英數字元、連字號和底線。 |
> | servers / virtualNetworkRules | servers | 1-128 | 英數字元和連字號。 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | servers | 全域 | 3-63 | 小寫字母、連字號和數字。<br><br>不能以連字號開頭或結尾。 |
> | servers / databases | servers | 1-63 | 英數字元和連字號。 |
> | servers / firewallRules | servers | 1-128 | 英數字元、連字號和底線。 |
> | servers / virtualNetworkRules | servers | 1-128 | 英數字元和連字號。 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | servers | 全域 | 3-63 | 小寫字母、連字號和數字。<br><br>不能以連字號開頭或結尾。 |
> | servers / databases | servers | 1-63 | 英數字元和連字號。 |
> | servers / firewallRules | servers | 1-128 | 英數字元、連字號和底線。 |
> | servers / virtualNetworkRules | servers | 1-128 | 英數字元和連字號。 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | IotHubs | 全域 | 3-50 | 英數字元和連字號。<br><br>不能以連字號結尾。 |
> | IotHubs / certificates | IoT 中樞 | 1-64 | 英數字元、連字號、句號和底線。 |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | 英數字元、連字號、句號和底線。 |
> | provisioningServices | 資源群組 | 3-64 | 英數字元和連字號。<br><br>以英數字元結尾。 |
> | provisioningServices / certificates | provisioningServices | 1-64 | 英數字元、連字號、句號和底線。 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | labs | 資源群組 | 1-50 | 英數字元、底線和連字號。 |
> | labs / customimages | 實驗室 | 1-80 | 英數字元、底線、連字號和括弧。 |
> | labs / formulas | 實驗室 | 1-80 | 英數字元、底線、連字號和括弧。 |
> | labs / virtualmachines | 實驗室 | 1-15 (Windows)<br>1-64 (Linux) | 英數字元和連字號。<br><br>以英數字元開頭及結尾。 不能全部為數字。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | databaseAccounts | 全域 | 3-44 | 小寫字母、數字和連字號。<br><br>以小寫字母或數字開頭。 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | domains | 資源群組 | 3-50 | 英數字元和連字號。 |
> | domains / topics | 網域 | 3-50 | 英數字元和連字號。 |
> | eventSubscriptions | 資源群組 | 3-64 | 英數字元和連字號。 |
> | topics | 資源群組 | 3-50 | 英數字元和連字號。 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | clusters | 資源群組 | 6-50 | 英數字元和連字號。<br><br>以字母開頭。 以字母或數字結尾。 |
> | 命名空間 | 全域 | 6-50 | 英數字元和連字號。<br><br>以字母開頭。 以字母或數字結尾。 |
> | namespaces / AuthorizationRules | 命名空間 | 1-50 | 英數字元、句號、連字號和底線。<br><br>以字母或數字開頭及結尾。 |
> | namespaces / disasterRecoveryConfigs | 命名空間 | 1-50 | 英數字元、句號、連字號和底線。<br><br>以字母或數字開頭及結尾。 |
> | namespaces / eventhubs | 命名空間 | 1-50 | 英數字元、句號、連字號和底線。<br><br>以字母或數字開頭及結尾。 |
> | namespaces / eventhubs / authorizationRules | 事件中樞 | 1-50 | 英數字元、句號、連字號和底線。<br><br>以字母或數字開頭及結尾。 |
> | namespaces / eventhubs / consumergroups | 事件中樞 | 1-50 | 英數字元、句號、連字號和底線。<br><br>以字母或數字開頭及結尾。 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | clusters | 全域 | 3-59 | 英數字元和連字號<br><br>以字母或數字開頭及結尾。 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | jobs | 資源群組 | 2-64 | 英數字元和連字號。<br><br>以字母開頭。 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | actionGroups | 資源群組 | 1-260 | 不能使用：<br>`/&%\?` <br><br>不能以空格或句號結尾。  |
> | components | 資源群組 | 1-260 | 不能使用：<br>`%&\?/` <br><br>不能以空格或句號結尾。  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | IoTApps | 全域 | 2-63 | 小寫字母、數字和連字號。<br><br>以小寫字母或數字開頭。 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | vaults | 全域 | 3-24 | 英數字元和連字號。<br><br>以字母開頭。 以字母或數字結尾。 不能包含連續的連字號。 |
> | vaults / secrets | 保存庫 | 1-127 | 英數字元和連字號。 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | clusters | 全域 | 4-22 | 小寫字母和數字。<br><br>以字母開頭。 |
> | /clusters / databases | 叢集 | 1-260 | 英數字元、連字號、空格和句號。 |
> | /clusters / databases / dataConnections | [資料庫] | 1-40 | 英數字元、連字號、空格和句號。 |
> | /clusters / databases / eventhubconnections | [資料庫] | 1-40 | 英數字元、連字號、空格和句號。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | integrationAccounts | 資源群組 | 1-80 | 英數字元、連字號、底線、句號和括弧。 |
> | integrationAccounts / assemblies | 整合帳戶 | 1-80 | 英數字元、連字號、底線、句號和括弧。 |
> | integrationAccounts / batchConfigurations | 整合帳戶 | 1-20 | 英數字元。 |
> | integrationAccounts / certificates | 整合帳戶 | 1-80 | 英數字元、連字號、底線、句號和括弧。 |
> | integrationAccounts / maps | 整合帳戶 | 1-80 | 英數字元、連字號、底線、句號和括弧。 |
> | integrationAccounts / partners | 整合帳戶 | 1-80 | 英數字元、連字號、底線、句號和括弧。 |
> | integrationAccounts / rosettanetprocessconfigurations | 整合帳戶 | 1-80 | 英數字元、連字號、底線、句號和括弧。 |
> | integrationAccounts / schemas | 整合帳戶 | 1-80 | 英數字元、連字號、底線、句號和括弧。 |
> | integrationAccounts / sessions | 整合帳戶 | 1-80 | 英數字元、連字號、底線、句號和括弧。 |
> | integrationServiceEnvironments | 資源群組 | 1-80 | 英數字元、連字號、句號和底線。 |
> | integrationServiceEnvironments / managedApis | 整合服務環境 | 1-80 | 英數字元、連字號、句號和底線。 |
> | workflows | 資源群組 | 1-80 | 英數字元、連字號、底線、句號和括弧。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | commitmentPlans | 資源群組 | 1-260 | 不能使用：<br>`<>*%&:?+/\\`<br><br>不能以空格結尾。 |
> | webServices | 資源群組 | 1-260 | 不能使用：<br>`<>*%&:?+/\\`<br><br>不能以空格結尾。 |
> | workspaces | 資源群組 | 1-260 | 不能使用：<br>`<>*%&:?+/\\`<br><br>不能以空格結尾。 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | workspaces | 資源群組 | 3-33 | 英數字元和連字號。 |
> | workspaces / computes | 工作區 | 2-16 | 英數字元和連字號。 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | userAssignedIdentities | 資源群組 | 3-128 | 英數字元、連字號和底線<br><br>以字母或數字開頭。 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | accounts | 資源群組 | 1-98 (資源群組名稱和帳戶名稱) | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | mediaservices | 資源群組 | 3-24 | 小寫字母和數字。 |
> | mediaservices / liveEvents | 媒體服務 | 1-32 | 英數字元和連字號。<br><br>以英數字元開頭。 |
> | mediaservices / liveEvents / liveOutputs | 即時活動 | 1-256 | 英數字元和連字號。<br><br>以英數字元開頭。 |
> | mediaservices / streamingEndpoints | 媒體服務 | 1-24 | 英數字元和連字號。<br><br>以英數字元開頭。 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | applicationGateways | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | applicationSecurityGroups | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | azureFirewalls | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | bastionHosts | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | connections | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | dnsZones | 資源群組 | 1-63 個字元<br><br>2-34 個標籤<br><br>每個標籤都是以句號分隔的一組字元。 例如，**contoso.com** 有 2 個標籤。 | 每個標籤都可包含英數字元、底線和連字號。<br><br>每個標籤都以句號分隔。 |
> | expressRouteCircuits | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | firewallPolicies | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | firewallPolicies / ruleGroups | 防火牆原則 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | frontDoors | 全域 | 5-64 | 英數字元和連字號。<br><br>以英數字元開頭及結尾。 |
> | loadBalancers | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | loadBalancers / inboundNatRules | 負載平衡器 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | localNetworkGateways | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | networkInterfaces | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | networkSecurityGroups | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | networkSecurityGroups / securityRules | 網路安全性群組 | 1-80 |  英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | networkWatchers | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | privateDnsZones | 資源群組 | 1-63 個字元<br><br>2-34 個標籤<br><br>每個標籤都是以句號分隔的一組字元。 例如，**contoso.com** 有 2 個標籤。 | 每個標籤都可包含英數字元、底線和連字號。<br><br>每個標籤都以句號分隔。 |
> | privateDnsZones / virtualNetworkLinks | 私人 DNS 區域 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | publicIPAddresses | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | publicIPPrefixes | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | routeFilters | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | routeFilters / routeFilterRules | 路由篩選 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | routeTables | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | routeTables / routes | 路由表 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | serviceEndpointPolicies | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | trafficmanagerprofiles | 全域 | 1-63 | 英數字元、連字號和句號。<br><br>以英數字元開頭及結尾。 |
> | virtualNetworkGateways | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | virtualNetworks | 資源群組 | 2-64 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | virtualnetworks / subnets | 虛擬網路 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | virtualNetworks / virtualNetworkPeerings | 虛擬網路 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | virtualWans | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | vpnGateways | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | vpnGateways / vpnConnections | VPN 閘道 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |
> | vpnSites | 資源群組 | 1-80 | 英數字元、底線、句號和連字號。<br><br>以英數字元開頭。 以英數字元或底線結尾。 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 命名空間 | 全域 | 6-50 | 英數字元和連字號<br><br>以字母開頭。 以英數字元結尾。 |
> | namespaces / AuthorizationRules | 命名空間 | 1-256 | 英數字元、句號、連字號和底線。<br><br>以英數字元開頭。 |
> | namespaces / notificationHubs | 命名空間 | 1-260 | 英數字元、句號、連字號和底線。<br><br>以英數字元開頭。 |
> | namespaces / notificationHubs / AuthorizationRules | 通知中樞 | 1-256 | 英數字元、句號、連字號和底線。<br><br>以英數字元開頭。 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | clusters | 資源群組 | 4-63 | 英數字元和連字號。<br><br>以英數字元開頭及結尾。 |
> | workspaces | 資源群組 | 4-63 | 英數字元和連字號。<br><br>以英數字元開頭及結尾。 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | solutions | 工作區 | N/A | 若為 Microsoft 所撰寫的解決方案，則名稱必須採用下列模式：<br>`SolutionType(WorkspaceName)`<br><br>若為第三方所撰寫的解決方案，則名稱必須採用下列模式：<br>`SolutionType[WorkspaceName]`<br><br>例如，有效的名稱為：<br>`AntiMalware(contoso-IT)`<br><br>解決方案類型會區分大小寫。 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | dashboards | 資源群組 | 3-160 | 英數字元和連字號。<br><br>若要使用限制的字元，請新增名為 **hidden-title** 的標記，其中包含想要使用的儀表板名稱。 入口網站會在顯示儀表板時顯示該名稱。 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | 英數字元和連字號。<br><br>不能以連字號開頭。 不能使用連續的連字號。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | 小寫字母或數字<br><br>以小寫字母開頭。 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | vaults | 資源群組 | 2-50 | 英數字元和連字號。<br><br>以字母開頭。 |
> | vaults / backupPolicies | 保存庫 | 3-150 | 英數字元和連字號。<br><br>以字母開頭。 不能以連字號結尾。 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 命名空間 | 全域 | 6-50 | 英數字元和連字號。<br><br>以字母開頭。 以字母或數字結尾。 |
> | namespaces / AuthorizationRules | 命名空間 | 1-50 |  英數字元、句號、連字號和底線。<br><br>以英數字元開頭及結尾。 |
> | namespaces / HybridConnections | 命名空間 | 1-260 | 英數字元、句號、連字號、底線和斜線。<br><br>以英數字元開頭及結尾。 |
> | namespaces / HybridConnections / authorizationRules | 混合式連線 | 1-50 | 英數字元、句號、連字號和底線。<br><br>以英數字元開頭及結尾。 |
> | namespaces / WcfRelays | 命名空間 | 1-260 | 英數字元、句號、連字號、底線和斜線。<br><br>以英數字元開頭及結尾。 |
> | namespaces / WcfRelays / authorizationRules | WCF 轉送 | 1-50 | 英數字元、句號、連字號和底線。<br><br>以英數字元開頭及結尾。 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | deployments | 資源群組 | 1-64 | 英數字元、底線、括弧、連字號和句號。 |
> | resourcegroups | 訂用帳戶 | 1-90 | 英數字元、底線、括弧、連字號、句號，以及符合 [Regex 文件](/rest/api/resources/resourcegroups/createorupdate)的 Unicode 字元。<br><br>不能以句號結尾。 |
> | tagNames | resource | 1-512 | 不能使用：<br>`<>%&\?/` |
> | tagNames / tagValues | 標記名稱 | 1-256 | 所有字元。 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 命名空間 | 全域 | 6-50 | 英數字元和連字號。<br><br>以字母開頭。 以字母或數字結尾。<br><br>如需詳細資訊，請參閱[建立命名空間](/rest/api/servicebus/create-namespace)。 |
> | namespaces / AuthorizationRules | 命名空間 | 1-50 | 英數字元、句號、連字號和底線。<br><br>以英數字元開頭及結尾。 |
> | namespaces / disasterRecoveryConfigs | 全域 | 6-50 | 英數字元和連字號。<br><br>以字母開頭。 以英數字元結尾。 |
> | namespaces / migrationConfigurations | 命名空間 |  | 應該一律為 **$default**。 |
> | namespaces / queues | 命名空間 | 1-260 | 英數字元、句號、連字號、底線和斜線。<br><br>以英數字元開頭及結尾。 |
> | namespaces / queues / authorizationRules | queue | 1-50 | 英數字元、句號、連字號和底線。<br><br>以英數字元開頭及結尾。 |
> | namespaces / topics | 命名空間 | 1-260 | 英數字元、句號、連字號、底線和斜線。<br><br>以英數字元開頭及結尾。 |
> | namespaces / topics / authorizationRules | 主題 | 1-50 | 英數字元、句號、連字號和底線。<br><br>以英數字元開頭及結尾。 |
> | namespaces / topics / subscriptions | 主題 | 1-50 | 英數字元、句號、連字號和底線。<br><br>以英數字元開頭及結尾。 |
> | namespaces / topics / subscriptions / rules | 訂用帳戶 | 1-50 | 英數字元、句號、連字號和底線。<br><br>以英數字元開頭及結尾。 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | 小寫字母、數字和連字號。<br><br>以小寫字母開頭。 以小寫字母或數字結尾。 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | signalR | 全域 | 3-63 | 英數字元和連字號。<br><br>以字母開頭。 以字母或數字結尾。  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | managedInstances | 全域 | 1-63 | 小寫字母、數字和連字號。<br><br>不能以連字號開頭或結尾。 |
> | servers | 全域 | 1-63 | 小寫字母、數字和連字號。<br><br>不能以連字號開頭或結尾。 |
> | servers / administrators | 伺服器 |  | 必須是 `ActiveDirectory`。 |
> | servers / databases | 伺服器 | 1-128 | 不能使用：<br>`<>*%&:\/?`<br><br>不能以句號或空格結尾。 |
> | servers / databases / syncGroups | [資料庫] | 1-150 | 英數字元、連字號和底線。 |
> | servers / elasticPools | 伺服器 | 1-128 | 不能使用：<br>`<>*%&:\/?`<br><br>不能以句號或空格結尾。 |
> | servers / failoverGroups | 全域 | 1-63 | 小寫字母、數字和連字號。<br><br>不能以連字號開頭或結尾。 |
> | servers / firewallRules | 伺服器 | 1-128 | 不能使用：<br>`<>*%&:;\/?`<br><br>不能以句號結尾。 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | storageAccounts | 全域 | 3-24 | 小寫字母和數字。 |
> | storageAccounts / blobServices | storage account |  | 必須是 `default`。 |
> | storageAccounts / blobServices / containers | storage account | 3-63 | 小寫字母、數字和連字號。<br><br>以小寫字母或數字開頭。 不能使用連續的連字號。 |
> | storageAccounts / fileServices | storage account |  | 必須是 `default`。 |
> | storageAccounts / fileServices / shares | storage account | 3-63 | 小寫字母、數字和連字號。<br><br>不能以連字號開頭或結尾。 不能使用連續的連字號。 |
> | storageAccounts / managementPolicies | storage account |  | 必須是 `default`。 |
> | blob | 容器 | 1-1024 | 任何 URL 字元，區分大小寫 |
> | queue | storage account | 3-63 | 小寫字母、數字和連字號。<br><br>不能以連字號開頭或結尾。 不能使用連續的連字號。 |
> | 資料表 | storage account | 3-63 | 英數字元。<br><br>以字母開頭。 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | storageSyncServices | 資源群組 | 1-260 | 英數字元、空格、句號、連字號和底線。<br><br>不能以句號或空格結尾。 |
> | storageSyncServices / syncGroups | 儲存體同步服務 | 1-260 | 英數字元、空格、句號、連字號和底線。<br><br>不能以句號或空格結尾。 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | managers | 資源群組 | 2-50 | 英數字元和連字號。<br><br>以字母開頭。 以英數字元結尾。 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | streamingjobs | 資源群組 | 3-63 | 英數字元、連字號和底線。 |
> | streamingjobs / functions | 資料流處理工作 | 3-63 | 英數字元、連字號和底線。 |
> | streamingjobs / inputs | 資料流處理工作 | 3-63 | 英數字元、連字號和底線。 |
> | streamingjobs / outputs | 資料流處理工作 | 3-63 | 英數字元、連字號和底線。 |
> | streamingjobs / transformations | 資料流處理工作 | 3-63 | 英數字元、連字號和底線。 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | environments | 資源群組 | 1-90 | 不能使用：<br>`'<>%&:\?/#` |
> | environments / accessPolicies | Environment | 1-90 | 不能使用：<br> `'<>%&:\?/#` |
> | environments / eventSources | Environment | 1-90 | 不能使用：<br>`'<>%&:\?/#` |
> | environments / referenceDataSets | Environment | 3-63 | 英數字元 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | certificates | 資源群組 | 1-260 | 不能使用：<br>`/` <br><br>不能以空格或句號結尾。  | 
> | serverfarms | 資源群組 | 1-40 | 英數字元和連字號。 |
> | sites | 全域 | 2-60 | 包含英數字元和連字號。<br><br>不能以連字號開頭或結尾。 |
> | sites / slots | site | 2-59 | 英數字元和連字號。 |

> [!NOTE]
> Azure Functions 與 Microsoft.Web/sites 有相同的命名規則和限制。

## <a name="next-steps"></a>後續步驟

如需如何命名資源的建議，請參閱[就緒：建議的命名和標記慣例](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。
