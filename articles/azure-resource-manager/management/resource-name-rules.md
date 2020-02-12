---
title: 資源命名限制
description: 顯示為 Azure 資源命名的規則和限制。
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 7b6a9e16ba28a2a0e5e4e181dc5650d2110eab88
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153157"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Azure 資源的命名規則和限制

本文摘要說明 Azure 資源的命名規則和限制。 如需如何命名資源的建議，請參閱[Ready：建議的命名和標記慣例](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

除非在 [有效字元] 資料行中特別注明，否則資源名稱不區分大小寫。

在下表中，「英數位元」一詞是指：

* **a**到**z** （小寫字母）
* **A**到**Z** （大寫字母）
* **0**到**9** （數位）

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 伺服器 | 資源群組 | 3-63 | 小寫字母和數位。<br><br>開頭為小寫字母。 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 服務 | 全域 | 1-50 | 英數位元.<br><br>開頭為字母。 |
> | 服務/api | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/api/問題 | api | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/api/問題/附件 | 問題 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/api/問題/批註 | 問題 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/api/作業 | api | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/api/作業/標記 | operation (作業) | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/api/版本 | api | 1-80 | 英數位元、底線和連字號。<br><br>開頭和結尾都是英數位元或底線。 |
> | 服務/api/架構 | api | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/api/tagDescriptions | api | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/api/標記 | api | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/api 版本-設定 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/authorizationServers | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/後端 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/憑證 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/診斷 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/群組 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/群組/使用者 | 群組 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/identityProviders | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/記錄器 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/通知 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/通知/recipientEmails | 通知 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/openidConnectProviders | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/原則 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/產品 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/產品/api | 產品 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/產品/群組 | 產品 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/產品/標記 | 產品 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/屬性 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/訂用帳戶 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/標記 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/範本 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |
> | 服務/使用者 | 服務 | 1-256 | 無法使用：<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>AppConfiguration

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | configurationStores | 資源群組 | 5-50 | 英數位元、底線和連字號。 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 鎖定 | 指派範圍 | 1-90 | 英數位元、句號、底線、連字號和括弧。<br><br>不能以句號結尾。 |
> | policyassignments | 指派範圍 | 1-128 顯示名稱<br><br>1-260 資源名稱 | [顯示名稱] 可以包含任何字元。<br><br>資源名稱不能包含 `%`，且不能以句號或空格結尾。 |
> | policydefinitions | 定義的範圍 | 1-128 顯示名稱<br><br>1-260 資源名稱 | [顯示名稱] 可以包含任何字元。<br><br>資源名稱不能包含 `%`，且不能以句號或空格結尾。 |
> | policySetDefinitions | 定義的範圍 | 1-128 顯示名稱<br><br>1-260 資源名稱 | [顯示名稱] 可以包含任何字元。<br><br>資源名稱不能包含 `%`，且不能以句號或空格結尾。  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | automationAccounts | 資源群組 | 6-50 | 英數位元和連字號。<br><br>以字母開頭，並以英數位元結束。 |
> | automationAccounts/憑證 | 自動化帳戶 | 1-128 | 無法使用：<br> `<>*%&:\?.+/` <br><br>結尾不能是空格。  |
> | automationAccounts/connections | 自動化帳戶 | 1-128 | 無法使用：<br> `<>*%&:\?.+/` <br><br>結尾不能是空格。 |
> | automationAccounts/認證 | 自動化帳戶 | 1-128 | 無法使用：<br> `<>*%&:\?.+/` <br><br>結尾不能是空格。 |
> | automationAccounts/runbook | 自動化帳戶 | 1-63 | 英數位元、底線和連字號。<br><br>開頭為字母。  |
> | automationAccounts/排程 | 自動化帳戶 | 1-128 | 無法使用：<br> `<>*%&:\?.+/` <br><br>結尾不能是空格。 |
> | automationAccounts/變數 | 自動化帳戶 | 1-128 | 無法使用：<br> `<>*%&:\?.+/` <br><br>結尾不能是空格。 |
> | automationAccounts/監看員 | 自動化帳戶 | 1-63 |  英數位元、底線和連字號。<br><br>開頭為字母。 |
> | automationAccounts/webhook | 自動化帳戶 | 1-128 | 無法使用：<br> `<>*%&:\?.+/` <br><br>結尾不能是空格。 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | batchAccounts | 區域 | 3-24 | 小寫字母和數位。 |
> | batchAccounts/應用程式 | batch 帳戶 | 1-64 | 英數位元、底線和連字號。 |
> | batchAccounts/憑證 | batch 帳戶 | 5-45 | 英數位元、底線和連字號。 |
> | batchAccounts/集區 | batch 帳戶 | 1-64 | 英數位元、底線和連字號。 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | blockchainMembers | 全域 | 2-20 | 小寫字母和數位。<br><br>開頭為小寫字母。 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | botServices | 全域 | 2-64 |  英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 |
> | botServices/通道 | bot 服務 | 2-64 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 |
> | botServices/Connections | bot 服務 | 2-64 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 |
> | enterpriseChannels | 資源群組 | 2-64 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | Redis | 全域 | 1-63 | 英數位元和連字號。<br><br>以英數位元開頭和結尾。 不允許連續的連字號。 |
> | Redis/firewallRules | Redis | 1-256 | 英數位元 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 設定檔 | 資源群組 | 1-260 | 英數位元和連字號。<br><br>以英數位元開頭和結尾。 |
> | 設定檔/端點 | 全域 | 1-50 | 英數位元和連字號。<br><br>以英數位元開頭和結尾。 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | certificateOrders | 資源群組 | 3-30 | 英數位元. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 帳戶 | 資源群組 | 2-64 | 英數位元和連字號。<br><br>以英數位元開頭和結尾。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | availabilitySets | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 以英數位元或底線結尾。 |
> | diskEncryptionSets | 資源群組 | 1-80 | 英數位元和底線。 |
> | 磁碟 | 資源群組 | 1-80 | 英數位元和底線。 |
> | galleries | 資源群組 | 1-80 | 英數位元和句點。<br><br>以英數位元開頭和結尾。 |
> | 資源庫/應用程式 | 效果 | 1-80 | 英數位元、連字號和句點。<br><br>以英數位元開頭和結尾。 |
> | 資源庫/應用程式/版本 | 應用程式 | 32 位元整數 | 數位和句點。 |
> | 資源庫/影像 | 效果 | 1-80 | 英數位元、連字號和句點。<br><br>以英數位元開頭和結尾。 |
> | 資源庫/影像/版本 | image | 32 位元整數 | 數位和句點。 |
> | 影像 | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 以英數位元或底線結尾。 |
> | 快照 | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 以英數位元或底線結尾。 |
> | virtualMachines | 資源群組 | 1-15 （Windows）<br>1-64 （Linux）<br><br>請參閱下列注意事項。 | 無法使用：<br> `\/""[]:|<>+=;,?*@&`<br><br>不能以底線開頭。 不能以句號或連字號結尾。 |
> | virtualMachineScaleSets | 資源群組 | 1-15 （Windows）<br>1-64 （Linux）<br><br>請參閱下列注意事項。 | 無法使用：<br> `\/""[]:|<>+=;,?*@&`<br><br>不能以底線開頭。 不能以句號或連字號結尾。 |

> [!NOTE]
> Azure 虛擬機器有兩個不同的名稱： [資源名稱] 和 [主機名稱]。 當您在入口網站中建立虛擬機器時，這兩個名稱會使用相同的值。 上表中的限制適用于主機名稱。 實際資源名稱最多可以有 64 個字元。

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | containerGroups | 資源群組 | 1-63 | 小寫字母、數位和連字號。<br><br>不能以連字號開頭或結尾。 不允許連續的連字號。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | registries | 全域 | 5-50 | 英數位元. |
> | 登錄/buildTasks | 登錄 | 5-50 | 英數位元. |
> | 登錄/buildTasks/步驟 | 建置工作 | 5-50 | 英數位元. |
> | 登錄/複寫 | 登錄 | 5-50 | 英數位元. |
> | 登錄/scopeMaps | 登錄 | 5-50 | 英數位元、連字號和底線。 |
> | 登錄/工作 | 登錄 | 5-50 | 英數位元、連字號和底線。 |
> | 登錄/權杖 | 登錄 | 5-50 | 英數位元、連字號和底線。 |
> | 登錄/webhook | 登錄 | 5-50 | 英數位元. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | managedClusters | 資源群組 | 1-63 | 英數位元、底線和連字號。<br><br>以英數位元開頭和結尾。 |
> | openShiftManagedClusters | 資源群組 | 1-30 | 英數位元. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | hubs | 資源群組 | 1-64 | 英數位元.<br><br>開頭為字母。  |
> | 中樞/authorizationPolicies | 集線器 | 1-50 | 英數位元、底線和句點。<br><br>以英數位元開頭和結尾。 |
> | 中樞/連接器 | 集線器 | 1-128 | 英數位元和底線。<br><br>開頭為字母。 |
> | 中樞/連接器/對應 | 連接器 | 1-128 | 英數位元和底線。<br><br>開頭為字母。 |
> | 中樞/互動 | 集線器 | 1-128 | 英數位元和底線。<br><br>開頭為字母。 |
> | 中樞/kpi | 集線器 | 1-512 | 英數位元和底線。<br><br>開頭為字母。 |
> | 中樞/連結 | 集線器 | 1-512 | 英數位元和底線。<br><br>開頭為字母。 |
> | 中樞/預測 | 集線器 | 1-512 | 英數位元和底線。<br><br>開頭為字母。 |
> | 中樞/設定檔 | 集線器 | 1-128 | 英數位元和底線。<br><br>開頭為字母。 |
> | 中樞/relationshipLinks | 集線器 | 1-512 | 英數位元和底線。<br><br>開頭為字母。 |
> | 中樞/關聯性 | 集線器 | 1-512 | 英數位元和底線。<br><br>開頭為字母。 |
> | 中樞/roleAssignments | 集線器 | 1-128 | 英數位元和底線。<br><br>開頭為字母。 |
> | 中樞/視圖 | 集線器 | 1-512 | 英數位元和底線。<br><br>開頭為字母。 |

## <a name="microsoftcustomproviders"></a>CustomProviders

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 關聯 | 資源群組 | 1-180 | 無法使用：<br>`%&\\?/`<br><br>不能以句號或空格結尾。 |
> | resourceProviders | 資源群組 | 3-64 | 無法使用：<br>`%&\\?/`<br><br>不能以句號或空格結尾。 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 作業 | 資源群組 | 3-24 | 英數位元、連字號、底線和句點。 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 工作區 | 資源群組 | 3-30 | 英數位元、底線和連字號 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | Factory | 全域 | 3-63 | 英數位元和連字號。<br><br>以英數位元開頭和結尾。 |
> | factory/資料流程 | 工廠 | 1-260 | 無法使用：<br>`<>*#.%&:\\+?/`<br><br>開始使用英數位元。 |
> | factory/資料集 | 工廠 | 1-260 | 無法使用：<br>`<>*#.%&:\\+?/`<br><br>開始使用英數位元。 |
> | factory/integrationRuntimes | 工廠 | 3-63 | 英數位元和連字號。<br><br>以英數位元開頭和結尾。 |
> | factory/linkedservices.json 和 datasets.json | 工廠 | 1-260 | 無法使用：<br>`<>*#.%&:\\+?/`<br><br>開始使用英數位元。 |
> | 工廠/管線 | 工廠 | 1-260 | 無法使用：<br>`<>*#.%&:\\+?/`<br><br>開始使用英數位元。 |
> | factory/觸發程式 | 工廠 | 1-260 | 無法使用：<br>`<>*#.%&:\\+?/`<br><br>開始使用英數位元。 |
> | factory/觸發程式/rerunTriggers | 觸發程序 (trigger) | 1-260 | 無法使用：<br>`<>*#.%&:\\+?/`<br><br>開始使用英數位元。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 帳戶 | 全域 | 3-24 | 小寫字母和數位。 |
> | 帳戶/computePolicies | 帳戶 | 3-60 | 英數位元、連字號和底線。 |
> | 帳戶/dataLakeStoreAccounts | 帳戶 | 3-24 | 小寫字母和數位。 |
> | 帳戶/firewallRules | 帳戶 | 3-50 | 英數位元、連字號和底線。 |
> | 帳戶/storageAccounts | 帳戶 | 3-60 | 英數位元、連字號和底線。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 帳戶 | 全域 | 3-24 | 小寫字母和數位。 |
> | 帳戶/firewallRules | 帳戶 | 3-50 | 英數位元、連字號和底線。 |
> | 帳戶/virtualNetworkRules | 帳戶 | 3-50 | 英數位元、連字號和底線。 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 服務 | 資源群組 | 2-62 | 英數位元、連字號、句點和底線。<br><br>開始使用英數位元。 |
> | 服務/專案 | 服務 | 2-57 | 英數位元、連字號、句點和底線。<br><br>開始使用英數位元。 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 伺服器 | 全域 | 3-63 | 小寫字母、連字號和數位。<br><br>不能以連字號開頭或結尾。 |
> | 伺服器/資料庫 | 伺服器 | 1-63 | 英數位元和連字號。 |
> | 伺服器/firewallRules | 伺服器 | 1-128 | 英數位元、連字號和底線。 |
> | 伺服器/virtualNetworkRules | 伺服器 | 1-128 | 英數位元和連字號。 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 伺服器 | 全域 | 3-63 | 小寫字母、連字號和數位。<br><br>不能以連字號開頭或結尾。 |
> | 伺服器/資料庫 | 伺服器 | 1-63 | 英數位元和連字號。 |
> | 伺服器/firewallRules | 伺服器 | 1-128 | 英數位元、連字號和底線。 |
> | 伺服器/virtualNetworkRules | 伺服器 | 1-128 | 英數位元和連字號。 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 伺服器 | 全域 | 3-63 | 小寫字母、連字號和數位。<br><br>不能以連字號開頭或結尾。 |
> | 伺服器/資料庫 | 伺服器 | 1-63 | 英數位元和連字號。 |
> | 伺服器/firewallRules | 伺服器 | 1-128 | 英數位元、連字號和底線。 |
> | 伺服器/virtualNetworkRules | 伺服器 | 1-128 | 英數位元和連字號。 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | IotHubs | 全域 | 3-50 | 英數位元和連字號。<br><br>不能以連字號結尾。 |
> | IotHubs/憑證 | IoT 中樞 | 1-64 | 英數位元、連字號、句點和底線。 |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | 英數位元、連字號、句點和底線。 |
> | provisioningServices | 資源群組 | 3-64 | 英數位元和連字號。<br><br>以英數位元結束。 |
> | provisioningServices/憑證 | provisioningServices | 1-64 | 英數位元、連字號、句點和底線。 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | labs | 資源群組 | 1-50 | 英數位元、底線和連字號。 |
> | 實驗室/customimages | 沖洗 | 1-80 | 英數位元、底線、連字號和括弧。 |
> | 實驗室/公式 | 沖洗 | 1-80 | 英數位元、底線、連字號和括弧。 |
> | 實驗室/virtualmachines | 沖洗 | 1-15 （Windows）<br>1-64 （Linux） | 英數位元和連字號。<br><br>以英數位元開頭和結尾。 不可以是所有數位。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | databaseAccounts | 全域 | 3-31 | 小寫字母、數位和連字號。<br><br>開頭為小寫字母或數位。 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 網域 | 資源群組 | 3-50 | 英數位元和連字號。 |
> | 網域/主題 | 網域 | 3-50 | 英數位元和連字號。 |
> | eventSubscriptions | 資源群組 | 3-64 | 英數位元和連字號。 |
> | topics | 資源群組 | 3-50 | 英數位元和連字號。 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 群集 | 資源群組 | 6-50 | 英數位元和連字號。<br><br>開頭為字母。 以字母或數位結尾。 |
> | 命名空間 | 全域 | 6-50 | 英數位元和連字號。<br><br>開頭為字母。 以字母或數位結尾。 |
> | 命名空間/AuthorizationRules | namespace | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以字母或數位開頭和結尾。 |
> | 命名空間/disasterRecoveryConfigs | namespace | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以字母或數位開頭和結尾。 |
> | 命名空間/eventhubs | namespace | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以字母或數位開頭和結尾。 |
> | 命名空間/eventhubs/authorizationRules | 事件中樞 | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以字母或數位開頭和結尾。 |
> | 命名空間/eventhubs/consumergroups | 事件中樞 | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以字母或數位開頭和結尾。 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 群集 | 全域 | 3-59 | 英數位元和連字號<br><br>以字母或數位開頭和結尾。 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 作業 | 資源群組 | 2-64 | 英數位元和連字號。<br><br>開頭為字母。 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | IoTApps | 全域 | 2-63 | 小寫字母、數位和連字號。<br><br>開頭為小寫字母或數位。 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | vaults | 全域 | 3-24 | 英數位元和連字號。<br><br>開頭為字母。 以字母或數位結尾。 不能包含連續的連字號。 |
> | 保存庫/秘密 | 保存庫 | 1-127 | 英數位元和連字號。 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 群集 | 全域 | 4-22 | 小寫字母和數位。<br><br>開頭為字母。 |
> | /clusters/資料庫 | 群集 | 1-260 | 英數位元、連字號、空格和句點。 |
> | /clusters/資料庫/dataConnections | database | 1-40 | 英數位元、連字號、空格和句點。 |
> | /clusters/資料庫/eventhubconnections | database | 1-40 | 英數位元、連字號、空格和句點。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | integrationAccounts | 資源群組 | 1-80 | 英數位元、連字號、底線、句號和括弧。 |
> | integrationAccounts/元件 | 整合帳戶 | 1-80 | 英數位元、連字號、底線、句號和括弧。 |
> | integrationAccounts / batchConfigurations | 整合帳戶 | 1-20 | 英數位元. |
> | integrationAccounts/憑證 | 整合帳戶 | 1-80 | 英數位元、連字號、底線、句號和括弧。 |
> | integrationAccounts/maps | 整合帳戶 | 1-80 | 英數位元、連字號、底線、句號和括弧。 |
> | integrationAccounts/合作夥伴 | 整合帳戶 | 1-80 | 英數位元、連字號、底線、句號和括弧。 |
> | integrationAccounts / rosettanetprocessconfigurations | 整合帳戶 | 1-80 | 英數位元、連字號、底線、句號和括弧。 |
> | integrationAccounts/架構 | 整合帳戶 | 1-80 | 英數位元、連字號、底線、句號和括弧。 |
> | integrationAccounts/會話 | 整合帳戶 | 1-80 | 英數位元、連字號、底線、句號和括弧。 |
> | integrationServiceEnvironments | 資源群組 | 1-80 | 英數位元、連字號、句點和底線。 |
> | integrationServiceEnvironments/managedApis | 整合服務環境 | 1-80 | 英數位元、連字號、句點和底線。 |
> | 工作流程 | 資源群組 | 1-80 | 英數位元、連字號、底線、句號和括弧。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | commitmentPlans | 資源群組 | 1-260 | 無法使用：<br>`<>*%&:?+/\\`<br><br>結尾不能是空格。 |
> | webServices | 資源群組 | 1-260 | 無法使用：<br>`<>*%&:?+/\\`<br><br>結尾不能是空格。 |
> | 工作區 | 資源群組 | 1-260 | 無法使用：<br>`<>*%&:?+/\\`<br><br>結尾不能是空格。 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 工作區 | 資源群組 | 3-33 | 英數位元和連字號。 |
> | 工作區/計算 | workspace | 2-16 | 英數位元和連字號。 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | userAssignedIdentities | 資源群組 | 3-128 | 英數位元、連字號和底線<br><br>開頭為字母或數位。 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 帳戶 | 資源群組 | 1-98 （適用于資源組名和帳戶名稱） | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | mediaservices | 資源群組 | 3-24 | 小寫字母和數位。 |
> | windowsazure.mediaservices.extensions/Liveevent | 媒體服務 | 1-32 | 英數位元和連字號。<br><br>開始使用英數位元。 |
> | windowsazure.mediaservices.extensions/Liveevent/Liveoutput | 實況活動 | 1-256 | 英數位元和連字號。<br><br>開始使用英數位元。 |
> | windowsazure.mediaservices.extensions/Streamingendpoint | 媒體服務 | 1-24 | 英數位元和連字號。<br><br>開始使用英數位元。 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | applicationGateways | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | applicationSecurityGroups | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | azureFirewalls | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 以英數位元或底線結尾。 |
> | bastionHosts | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | connections | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | dnsZones | 資源群組 | 1-63 個字元<br><br>2到34標籤<br><br>每個標籤都是以句點分隔的一組字元。 例如， **contoso.com**有2個標籤。 | 每個標籤都可以包含英數位元、底線和連字號。<br><br>每個標籤都是以句點分隔。 |
> | expressRouteCircuits | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | firewallPolicies | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | firewallPolicies / ruleGroups | 防火牆原則 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | frontDoors | 全域 | 5-64 | 英數位元和連字號。<br><br>以英數位元開頭和結尾。 |
> | loadBalancers | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | loadBalancers/Loadbalancer.inboundnatrules | 負載平衡器 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | localNetworkGateways | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | networkInterfaces | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | networkSecurityGroups | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | networkSecurityGroups/securityRules | 網路安全性群組 | 1-80 |  英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | networkWatchers | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | privateDnsZones | 資源群組 | 1-63 個字元<br><br>2到34標籤<br><br>每個標籤都是以句點分隔的一組字元。 例如， **contoso.com**有2個標籤。 | 每個標籤都可以包含英數位元、底線和連字號。<br><br>每個標籤都是以句點分隔。 |
> | privateDnsZones / virtualNetworkLinks | 私人 DNS 區域 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | publicIPAddresses | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | publicIPPrefixes | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | routeFilters | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | routeFilters / routeFilterRules | 路由篩選 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | routeTables | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | routeTables/路由 | 路由表 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | serviceEndpointPolicies | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | trafficmanagerprofiles | 全域 | 1-63 | 英數位元、連字號和句點。<br><br>以英數位元開頭和結尾。 |
> | virtualNetworkGateways | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | virtualNetworks | 資源群組 | 2-64 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | virtualnetworks/子網 | 虛擬網路 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | virtualNetworks/virtualNetworkPeerings | 虛擬網路 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | virtualWans | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | vpnGateways | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | vpnGateways / vpnConnections | VPN 閘道 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |
> | vpnSites | 資源群組 | 1-80 | 英數位元、底線、句號和連字號。<br><br>開始使用英數位元。 結束英數位元或底線。 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 命名空間 | 全域 | 6-50 | 英數位元和連字號<br><br>以英數位元開頭和結尾。 |
> | 命名空間/AuthorizationRules | namespace | 1-256 | 英數位元、句點（.）、連字號和底線。<br><br>開始英數位元。 |
> | 命名空間/notificationHubs | namespace | 1-260 | 英數位元、句點（.）、連字號和底線。<br><br>開始英數位元。 |
> | 命名空間/notificationHubs/AuthorizationRules | 通知中樞 | 1-256 | 英數位元、句點（.）、連字號和底線。<br><br>開始英數位元。 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 群集 | 資源群組 | 4-63 | 英數位元和連字號。<br><br>以英數位元開頭和結尾。 |
> | 工作區 | 資源群組 | 4-63 | 英數位元和連字號。<br><br>以英數位元開頭和結尾。 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 儀表板 | 資源群組 | 3-160 | 英數位元和連字號。<br><br>若要使用受限制的字元，請以您想要使用的儀表板名稱新增名為**hidden title**的標記。 顯示儀表板時，入口網站會顯示該名稱。 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | 英數位元和連字號。<br><br>不能以連字號開頭。 不能使用連續的連字號。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 容量 | region | 3-63 | 小寫字母或數位<br><br>開頭為小寫字母。 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | vaults | 資源群組 | 2-50 | 英數位元和連字號。<br><br>開頭為字母。 |
> | 保存庫/backupPolicies | 保存庫 | 3-150 | 英數位元和連字號。<br><br>開頭為字母。 不能以連字號結尾。 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 命名空間 | 全域 | 6-50 | 英數位元和連字號。<br><br>以字母開頭。 以字母或數位結尾。 |
> | 命名空間/AuthorizationRules | namespace | 1-50 |  英數位元、句點（.）、連字號和底線。<br><br>以英數位元開頭和結尾。 |
> | 命名空間/HybridConnections | namespace | 1-260 | 英數位元、句點（.）、連字號、底線和斜線。<br><br>以英數位元開頭和結尾。 |
> | 命名空間/HybridConnections/authorizationRules | 混合連線 | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以英數位元開頭和結尾。 |
> | 命名空間/WcfRelays | namespace | 1-260 | 英數位元、句點（.）、連字號、底線和斜線。<br><br>以英數位元開頭和結尾。 |
> | 命名空間/WcfRelays/authorizationRules | Wcf 轉送 | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以英數位元開頭和結尾。 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 部署 | 資源群組 | 1-64 | 英數位元、底線、括弧、連字號和句點。 |
> | resourcegroups | 訂閱中 | 1-90 | 符合[RegEx 檔](/rest/api/resources/resourcegroups/createorupdate)的英數位元、底線、括弧、連字號、句號和 unicode 字元。<br><br>不能以句號結尾。 |
> | tagNames | resource | 1-512 | 無法使用：<br>`<>%&\?/` |
> | tagNames / tagValues | 標記名稱 | 1-256 | 所有字元。 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 命名空間 | 全域 | 6-50 | 英數位元和連字號。<br><br>以字母開頭。 以字母或數位結尾。<br><br>如需詳細資訊，請參閱[建立命名空間](/rest/api/servicebus/create-namespace)。 |
> | 命名空間/AuthorizationRules | namespace | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以 alphnumeric 開頭和結尾。 |
> | 命名空間/disasterRecoveryConfigs | 全域 | 6-50 | 英數位元和連字號。<br><br>開頭為字母。 以英數位元結束。 |
> | 命名空間/migrationConfigurations | namespace |  | 應該一律 **$default**。 |
> | 命名空間/佇列 | namespace | 1-260 | 英數位元、句點（.）、連字號、底線和斜線。<br><br>以英數位元開頭和結尾。 |
> | 命名空間/佇列/authorizationRules | queue | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以 alphnumeric 開頭和結尾。 |
> | 命名空間/主題 | namespace | 1-260 | 英數位元、句點（.）、連字號、底線和斜線。<br><br>以英數位元開頭和結尾。 |
> | 命名空間/主題/authorizationRules | 主題 | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以 alphnumeric 開頭和結尾。 |
> | 命名空間/主題/訂用帳戶 | 主題 | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以 alphnumeric 開頭和結尾。 |
> | 命名空間/主題/訂用帳戶/規則 | 訂閱中 | 1-50 | 英數位元、句點（.）、連字號和底線。<br><br>以 alphnumeric 開頭和結尾。 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 群集 | region | 4-23 | 小寫字母、數位和連字號。<br><br>開頭為小寫字母。 以小寫字母或數位結尾。 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | signalR | 全域 | 3-63 | 英數位元和連字號。<br><br>開頭為字母。 以字母或數位結尾。  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | managedInstances | 全域 | 1-63 | 小寫字母、數位和連字號。<br><br>不能以連字號開頭或結尾。 |
> | 伺服器 | 全域 | 1-63 | 小寫字母、數位和連字號。<br><br>不能以連字號開頭或結尾。 |
> | 伺服器/資料庫 | 伺服器 | 1-128 | 無法使用：<br>`<>*%&:\/?`<br><br>不能以句號或空格結尾。 |
> | 伺服器/資料庫/syncGroups | database | 1-150 | 英數位元、連字號和底線。 |
> | 伺服器/elasticPools | 伺服器 | 1-128 | 無法使用：<br>`<>*%&:\/?`<br><br>不能以句號或空格結尾。 |
> | 伺服器/failoverGroups | 全域 | 1-63 | 小寫字母、數位和連字號。<br><br>不能以連字號開頭或結尾。 |
> | 伺服器/firewallRules | 伺服器 | 1-128 | 無法使用：<br>`<>*%&:;\/?`<br><br>不能以句號結尾。 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | storageAccounts | 全域 | 3-24 | 小寫字母和數位。 |
> | storageAccounts/blobServices | 儲存體帳戶 |  | 必須是 `default`。 |
> | storageAccounts/blobServices/容器 | 儲存體帳戶 | 3-63 | 小寫字母、數位和連字號。<br><br>開頭為小寫字母或數位。 不能使用連續的連字號。 |
> | storageAccounts/fileServices | 儲存體帳戶 |  | 必須是 `default`。 |
> | storageAccounts/fileServices/共用 | 儲存體帳戶 | 3-63 | 小寫字母、數位和連字號。<br><br>不能以連字號開頭或結尾。 不能使用連續的連字號。 |
> | storageAccounts/managementPolicies | 儲存體帳戶 |  | 必須是 `default`。 |
> | blob | 容器 | 1-1024 | 任何 URL 字元，區分大小寫 |
> | queue | 儲存體帳戶 | 3-63 | 小寫字母、數位和連字號。<br><br>不能以連字號開頭或結尾。 不能使用連續的連字號。 |
> | table | 儲存體帳戶 | 3-63 | 英數位元.<br><br>開頭為字母。 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | storageSyncServices | 資源群組 | 1-260 | 英數位元、空格、句點、連字號和底線。<br><br>不能以句號或空格結尾。 |
> | storageSyncServices / syncGroups | 儲存體同步服務 | 1-260 | 英數位元、空格、句點、連字號和底線。<br><br>不能以句號或空格結尾。 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | managers | 資源群組 | 2-50 | 英數位元和連字號。<br><br>開頭為字母。 以英數位元結束。 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | streamingjobs | 資源群組 | 3-63 | 英數位元、連字號和底線。 |
> | streamingjobs/函數 | 串流作業 | 3-63 | 英數位元、連字號和底線。 |
> | streamingjobs/輸入 | 串流作業 | 3-63 | 英數位元、連字號和底線。 |
> | streamingjobs/輸出 | 串流作業 | 3-63 | 英數位元、連字號和底線。 |
> | streamingjobs/轉換 | 串流作業 | 3-63 | 英數位元、連字號和底線。 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | 環境 | 資源群組 | 1-90 | 無法使用：<br>`'<>%&:\?/#` |
> | 環境/accessPolicies | 環境 | 1-90 | 無法使用：<br> `'<>%&:\?/#` |
> | 環境/eventSources | 環境 | 1-90 | 無法使用：<br>`'<>%&:\?/#` |
> | 環境/referenceDataSets | 環境 | 3-63 | 英數位元 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 實體 | 範圍 | Length | 有效字元 |
> | --- | --- | --- | --- |
> | serverfarms | 資源群組 | 1-40 | 英數位元和連字號。 |
> | sites | 全域 | 2-60 | 包含英數位元和連字號。<br><br>不能以連字號開頭或結尾。 |
> | 網站/位置 | site | 2-59 | 英數位元和連字號。 |

## <a name="next-steps"></a>後續步驟

如需如何命名資源的建議，請參閱[Ready：建議的命名和標記慣例](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。
