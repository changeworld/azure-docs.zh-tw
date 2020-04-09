---
title: 資源命名限制
description: 顯示命名 Azure 資源的規則和限制。
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: f6203f987654b33b32da72bfec030a9d0ab69df8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981523"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Azure 資源的命名規則和限制

本文總結了 Azure 資源的命名規則和限制。 有關如何命名資源的建議,請參閱[建議命名和標記約定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

除非在有效字元列中明確註明,否則資源名稱不區分大小寫。

在下表中,術語字母數位是指:

* **a**到**z(** 小寫字母 )
* **A****到 Z(** 大寫字母 )
* **0**到**9** (數位)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | servers | 資源群組 | 3-63 | 小寫字母和數位。<br><br>從小寫字母開始。 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 服務 | 全域 | 1-50 | 字母數位。<br><br>從字母開始。 |
> | 服務/ apis | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ apis / 問題 | api | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ apis / 問題 / 附件 | 問題 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ apis / 問題 / 評論 | 問題 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ apis / 操作 | api | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ apis / 操作 / 標籤 | operation (作業) | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ apis / 版本 | api | 1-80 | 字母數位、下劃線和連字元。<br><br>以字母數位或下劃線開始和結束。 |
> | 服務/ apis / 架構 | api | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ apis / 標籤描述 | api | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ apis / 標籤 | api | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/api 版本集 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/授權伺服器 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/後端介面 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/憑證 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/診斷 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/組 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ 群組 / 使用者 | 群組 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/身份提供者 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/記錄器 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/通知 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ 通知 / 收件者電子郵件 | 通知 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ 開放連線提供者 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/原則 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/產品 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ 產品 / 產品 | product | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ 產品 / 群組 | product | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/ 產品 / 標籤 | product | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/屬性 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/訂閱 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/標籤 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/樣本 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |
> | 服務/使用者 | 服務 | 1-256 | 無法使用:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>微軟.應用程式設定

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 設定儲存 | 資源群組 | 5-50 | 字母數位、下劃線和連字元。 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | locks | 配置範圍 | 1-90 | 字母數位、句點、下劃線、連字元和括弧。<br><br>不能在期間結束。 |
> | policyassignments | 配置範圍 | 1-128 顯示名稱<br><br>1-260 資源名稱 | 顯示名稱可以包含任何字元。<br><br>資源名稱不能包含`%`,也不能以期間或空格結尾。 |
> | 原則定義 | 定義範圍 | 1-128 顯示名稱<br><br>1-260 資源名稱 | 顯示名稱可以包含任何字元。<br><br>資源名稱不能包含`%`,也不能以期間或空格結尾。 |
> | policySetDefinitions | 定義範圍 | 1-128 顯示名稱<br><br>1-260 資源名稱 | 顯示名稱可以包含任何字元。<br><br>資源名稱不能包含`%`,也不能以期間或空格結尾。  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | automationAccounts | 資源群組 | 6-50 | 字母數位和連字元。<br><br>以字母開頭,以字母數位結尾。 |
> | 自動化帳戶/憑證 | 自動化帳戶 | 1-128 | 無法使用:<br> `<>*%&:\?.+/` <br><br>不能以空間結束。  |
> | 自動化帳號/連線 | 自動化帳戶 | 1-128 | 無法使用:<br> `<>*%&:\?.+/` <br><br>不能以空間結束。 |
> | 自動化帳戶/認證 | 自動化帳戶 | 1-128 | 無法使用:<br> `<>*%&:\?.+/` <br><br>不能以空間結束。 |
> | 自動化帳錄/執行簿 | 自動化帳戶 | 1-63 | 字母數位、下劃線和連字元。<br><br>從字母開始。  |
> | 自動化帳戶/時程表 | 自動化帳戶 | 1-128 | 無法使用:<br> `<>*%&:\?.+/` <br><br>不能以空間結束。 |
> | 自動化帳戶/變數 | 自動化帳戶 | 1-128 | 無法使用:<br> `<>*%&:\?.+/` <br><br>不能以空間結束。 |
> | 自動化帳戶/觀察程式 | 自動化帳戶 | 1-63 |  字母數位、下劃線和連字元。<br><br>從字母開始。 |
> | 自動化帳戶/網路鉤子 | 自動化帳戶 | 1-128 | 無法使用:<br> `<>*%&:\?.+/` <br><br>不能以空間結束。 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | batchAccounts | 區域 | 3-24 | 小寫字母和數位。 |
> | 批次處理帳/應用程式 | 批次帳戶 | 1-64 | 字母數位、下劃線和連字元。 |
> | 批次處理帳/憑證 | 批次帳戶 | 5-45 | 字母數位、下劃線和連字元。 |
> | 批次處理帳戶/池 | 批次帳戶 | 1-64 | 字母數位、下劃線和連字元。 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 區塊鏈成員 | 全域 | 2-20 | 小寫字母和數位。<br><br>從小寫字母開始。 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | botServices | 全域 | 2-64 |  字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 |
> | 機器人服務/頻道 | 機器人服務 | 2-64 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 |
> | 機器人服務 / 連接 | 機器人服務 | 2-64 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 |
> | 企業管道 | 資源群組 | 2-64 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | Redis | 全域 | 1-63 | 字母數位和連字元。<br><br>以字母數字開始和結束。 不允許連續連字元。 |
> | 雷瑞斯 / 防火牆規則 | Redis | 1-256 | 字母數字 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 設定檔 | 資源群組 | 1-260 | 字母數位和連字元。<br><br>以字母數字開始和結束。 |
> | 設定檔案/點端點 | 全域 | 1-50 | 字母數位和連字元。<br><br>以字母數字開始和結束。 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | certificateOrders | 資源群組 | 3-30 | 字母數位。 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | accounts | 資源群組 | 2-64 | 字母數位和連字元。<br><br>以字母數字開始和結束。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | availabilitySets | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 以字母數位或下劃線結束。 |
> | 磁碟加密集 | 資源群組 | 1-80 | 字母數位和下劃線。 |
> | disks | 資源群組 | 1-80 | 字母數位和下劃線。 |
> | galleries | 資源群組 | 1-80 | 字母數位和句點。<br><br>以字母數字開始和結束。 |
> | 畫廊/應用程式 | 資源庫 | 1-80 | 字母數位、連字元和句點。<br><br>以字母數字開始和結束。 |
> | 畫廊 /應用程式/版本 | 應用程式 | 32 位元整數 | 數字和期間。 |
> | 畫廊/圖片 | 資源庫 | 1-80 | 字母數位、連字元和句點。<br><br>以字母數字開始和結束。 |
> | 畫廊 / 影像 / 版本 | image | 32 位元整數 | 數字和期間。 |
> | images | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 以字母數位或下劃線結束。 |
> | snapshots | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 以字母數位或下劃線結束。 |
> | virtualMachines | 資源群組 | 1-15 (視窗)<br>1-64 (Linux)<br><br>請參閱下列注意事項。 | 無法使用:<br> `\/""[]:|<>+=;,?*@&`<br><br>不能從下劃線開始。 不能以句點或連字元結束。 |
> | virtualMachineScaleSets | 資源群組 | 1-15 (視窗)<br>1-64 (Linux)<br><br>請參閱下列注意事項。 | 無法使用:<br> `\/""[]:|<>+=;,?*@&`<br><br>不能從下劃線開始。 不能以句點或連字元結束。 |

> [!NOTE]
> Azure 虛擬機有兩個不同的名稱:資源名稱和主機名。 在門戶中創建虛擬機器時,兩個名稱使用相同的值。 上表中的限制適用於主機名。 實際資源名稱最多可以有 64 個字元。

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | containerGroups | 資源群組 | 1-63 | 小寫字母、數位和連字元。<br><br>不能以連字元開始或結尾。 不允許連續的連字號。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | registries | 全域 | 5-50 | 字母數位。 |
> | 註冊表/產生工作 | 登錄 | 5-50 | 字母數位。 |
> | 註冊表/產生任務/步驟 | 建置工作 | 5-50 | 字母數位。 |
> | 登錄表/複製 | 登錄 | 5-50 | 字母數位。 |
> | 註冊表/範圍圖 | 登錄 | 5-50 | 字母數位、連字元和下劃線。 |
> | 註冊表/任務 | 登錄 | 5-50 | 字母數位、連字元和下劃線。 |
> | 註冊表/權杖 | 登錄 | 5-50 | 字母數位、連字元和下劃線。 |
> | 註冊表/網鉤 | 登錄 | 5-50 | 字母數位。 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | managedClusters | 資源群組 | 1-63 | 字母數位、下劃線和連字元。<br><br>以字母數字開始和結束。 |
> | 開放式轉移託管集群 | 資源群組 | 1-30 | 字母數位。 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | hubs | 資源群組 | 1-64 | 字母數位。<br><br>從字母開始。  |
> | 集線器/授權政策 | 中樞 | 1-50 | 字母數位、下劃線和句點。<br><br>以字母數字開始和結束。 |
> | 集線器/連接器 | 中樞 | 1-128 | 字母數位和下劃線。<br><br>從字母開始。 |
> | 集線器/連接器/映射 | 連接器 | 1-128 | 字母數位和下劃線。<br><br>從字母開始。 |
> | 集線器/互動 | 中樞 | 1-128 | 字母數位和下劃線。<br><br>從字母開始。 |
> | 集線器 / kpi | 中樞 | 1-512 | 字母數位和下劃線。<br><br>從字母開始。 |
> | 集線器/連結 | 中樞 | 1-512 | 字母數位和下劃線。<br><br>從字母開始。 |
> | 集線器/預測 | 中樞 | 1-512 | 字母數位和下劃線。<br><br>從字母開始。 |
> | 集線器/設定檔 | 中樞 | 1-128 | 字母數位和下劃線。<br><br>從字母開始。 |
> | 集線器/關聯連結 | 中樞 | 1-512 | 字母數位和下劃線。<br><br>從字母開始。 |
> | 集線器/關係 | 中樞 | 1-512 | 字母數位和下劃線。<br><br>從字母開始。 |
> | 集線器/角色分配 | 中樞 | 1-128 | 字母數位和下劃線。<br><br>從字母開始。 |
> | 集線器/檢視 | 中樞 | 1-512 | 字母數位和下劃線。<br><br>從字母開始。 |

## <a name="microsoftcustomproviders"></a>微軟.自訂提供者

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 關聯 | 資源群組 | 1-180 | 無法使用:<br>`%&\\?/`<br><br>不能以句點或空格結束。 |
> | 資源提供者 | 資源群組 | 3-64 | 無法使用:<br>`%&\\?/`<br><br>不能以句點或空格結束。 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | jobs | 資源群組 | 3-24 | 字母數位、連字元、下劃線和句點。 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | workspaces | 資源群組 | 3-30 | 字母數字、下劃線與連字元 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | factories | 全域 | 3-63 | 字母數位和連字元。<br><br>以字母數字開始和結束。 |
> | 工廠/資料流程 | 工廠 | 1-260 | 無法使用:<br>`<>*#.%&:\\+?/`<br><br>以字母數字開頭。 |
> | 工廠/資料集 | 工廠 | 1-260 | 無法使用:<br>`<>*#.%&:\\+?/`<br><br>以字母數字開頭。 |
> | 工廠/整合執行時間 | 工廠 | 3-63 | 字母數位和連字元。<br><br>以字母數字開始和結束。 |
> | 工廠/連結服務 | 工廠 | 1-260 | 無法使用:<br>`<>*#.%&:\\+?/`<br><br>以字母數字開頭。 |
> | 工廠/導管 | 工廠 | 1-260 | 無法使用:<br>`<>*#.%&:\\+?/`<br><br>以字母數字開頭。 |
> | 工廠/觸發器 | 工廠 | 1-260 | 無法使用:<br>`<>*#.%&:\\+?/`<br><br>以字母數字開頭。 |
> | 工廠/ 觸發器 / 重新執行觸發器 | 觸發程序 (trigger) | 1-260 | 無法使用:<br>`<>*#.%&:\\+?/`<br><br>以字母數字開頭。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | accounts | 全域 | 3-24 | 小寫字母和數位。 |
> | 帳號/計算原則 | account | 3-60 | 字母數位、連字元和下劃線。 |
> | 帳號/資料湖儲存帳戶 | account | 3-24 | 小寫字母和數位。 |
> | 帳號/防火牆規則 | account | 3-50 | 字母數位、連字元和下劃線。 |
> | 帳戶/儲存帳戶 | account | 3-60 | 字母數位、連字元和下劃線。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | accounts | 全域 | 3-24 | 小寫字母和數位。 |
> | 帳號/防火牆規則 | account | 3-50 | 字母數位、連字元和下劃線。 |
> | 帳號/虛擬網路規則 | account | 3-50 | 字母數位、連字元和下劃線。 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 服務 | 資源群組 | 2-62 | 字母數位、連字元、句點和下劃線。<br><br>以字母數字開頭。 |
> | 服務/專案 | 服務 | 2-57 | 字母數位、連字元、句點和下劃線。<br><br>以字母數字開頭。 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | servers | 全域 | 3-63 | 小寫字母、連字元和數位。<br><br>不能以連字元開始或結尾。 |
> | 伺服器/資料庫 | servers | 1-63 | 字母數位和連字元。 |
> | 伺服器/防火牆規則 | servers | 1-128 | 字母數位、連字元和下劃線。 |
> | 伺服器/虛擬網路規則 | servers | 1-128 | 字母數位和連字元。 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | servers | 全域 | 3-63 | 小寫字母、連字元和數位。<br><br>不能以連字元開始或結尾。 |
> | 伺服器/資料庫 | servers | 1-63 | 字母數位和連字元。 |
> | 伺服器/防火牆規則 | servers | 1-128 | 字母數位、連字元和下劃線。 |
> | 伺服器/虛擬網路規則 | servers | 1-128 | 字母數位和連字元。 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | servers | 全域 | 3-63 | 小寫字母、連字元和數位。<br><br>不能以連字元開始或結尾。 |
> | 伺服器/資料庫 | servers | 1-63 | 字母數位和連字元。 |
> | 伺服器/防火牆規則 | servers | 1-128 | 字母數位、連字元和下劃線。 |
> | 伺服器/虛擬網路規則 | servers | 1-128 | 字母數位和連字元。 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | IotHubs | 全域 | 3-50 | 字母數位和連字元。<br><br>不能以連字元結束。 |
> | IotHubs / 憑證 | 物聯網中心 | 1-64 | 字母數位、連字元、句點和下劃線。 |
> | IotHubs / 事件 HubPoints / 消費者群體 | 事件 HubEndpoints | 1-50 | 字母數位、連字元、句點和下劃線。 |
> | 預配服務 | 資源群組 | 3-64 | 字母數位和連字元。<br><br>以字母數字結束。 |
> | 預先定義服務憑證 | 預配服務 | 1-64 | 字母數位、連字元、句點和下劃線。 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | labs | 資源群組 | 1-50 | 字母數位、下劃線和連字元。 |
> | 實驗室/自訂影像 | 實驗室 | 1-80 | 字母數位、下劃線、連字元和括弧。 |
> | 實驗室/配方 | 實驗室 | 1-80 | 字母數位、下劃線、連字元和括弧。 |
> | 實驗室/虛擬機器 | 實驗室 | 1-15 (視窗)<br>1-64 (Linux) | 字母數位和連字元。<br><br>以字母數字開始和結束。 不可能是所有數位。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | databaseAccounts | 全域 | 3-31 | 小寫字母、數位和連字元。<br><br>從小寫字母或數位開始。 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | domains | 資源群組 | 3-50 | 字母數位和連字元。 |
> | 網域/佈景主題 | 網域 | 3-50 | 字母數位和連字元。 |
> | eventSubscriptions | 資源群組 | 3-64 | 字母數位和連字元。 |
> | topics | 資源群組 | 3-50 | 字母數位和連字元。 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | clusters | 資源群組 | 6-50 | 字母數位和連字元。<br><br>從字母開始。 以字母或數位結尾。 |
> | 命名空間 | 全域 | 6-50 | 字母數位和連字元。<br><br>從字母開始。 以字母或數位結尾。 |
> | 命名空間/授權規則 | namespace | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以字母或數位開頭和結尾。 |
> | 命名空間 / 災難修復設定 | namespace | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以字母或數位開頭和結尾。 |
> | 命名空間/事件中心 | namespace | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以字母或數位開頭和結尾。 |
> | 命名空間 / 事件中心 / 授權規則 | 事件中心 | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以字母或數位開頭和結尾。 |
> | 命名空間 / 事件中心 / 消費組 | 事件中心 | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以字母或數位開頭和結尾。 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | clusters | 全域 | 3-59 | 字母數字與連字元<br><br>以字母或數位開頭和結尾。 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | jobs | 資源群組 | 2-64 | 字母數位和連字元。<br><br>從字母開始。 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | components | 資源群組 | 1-260 | 無法使用:<br>`%&\?/` <br><br>不能以空格或句點結束。  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | IoTApps | 全域 | 2-63 | 小寫字母、數位和連字元。<br><br>從小寫字母或數位開始。 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | vaults | 全域 | 3-24 | 字母數位和連字元。<br><br>從字母開始。 以字母或數位結尾。 不能包含連續連字元。 |
> | 保管庫/機密 | 保存庫 | 1-127 | 字母數位和連字元。 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | clusters | 全域 | 4-22 | 小寫字母和數位。<br><br>從字母開始。 |
> | /群集 / 資料庫 | 叢集 | 1-260 | 字母數位、連字元、空格和句點。 |
> | /群集 / 資料庫 / 資料連線 | [資料庫] | 1-40 | 字母數位、連字元、空格和句點。 |
> | /群集 / 資料庫 / 事件點連接 | [資料庫] | 1-40 | 字母數位、連字元、空格和句點。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | integrationAccounts | 資源群組 | 1-80 | 字母數位、連字元、下劃線、句點和括弧。 |
> | 整合帳號/程式集 | 整合帳戶 | 1-80 | 字母數位、連字元、下劃線、句點和括弧。 |
> | 整合帳號/批次處理設定 | 整合帳戶 | 1-20 | 字母數位。 |
> | 整合帳號/憑證 | 整合帳戶 | 1-80 | 字母數位、連字元、下劃線、句點和括弧。 |
> | 整合帳號/地圖 | 整合帳戶 | 1-80 | 字母數位、連字元、下劃線、句點和括弧。 |
> | 整合帳號/合作夥伴 | 整合帳戶 | 1-80 | 字母數位、連字元、下劃線、句點和括弧。 |
> | 整合帳號/羅塞塔內程式配置 | 整合帳戶 | 1-80 | 字母數位、連字元、下劃線、句點和括弧。 |
> | 整合帳號/架構 | 整合帳戶 | 1-80 | 字母數位、連字元、下劃線、句點和括弧。 |
> | 集成帳戶/工作階段 | 整合帳戶 | 1-80 | 字母數位、連字元、下劃線、句點和括弧。 |
> | 整合服務環境 | 資源群組 | 1-80 | 字母數位、連字元、句點和下劃線。 |
> | 整合服務環境/託管 Apis | 整合服務環境 | 1-80 | 字母數位、連字元、句點和下劃線。 |
> | workflows | 資源群組 | 1-80 | 字母數位、連字元、下劃線、句點和括弧。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | commitmentPlans | 資源群組 | 1-260 | 無法使用:<br>`<>*%&:?+/\\`<br><br>不能以空格結束。 |
> | webServices | 資源群組 | 1-260 | 無法使用:<br>`<>*%&:?+/\\`<br><br>不能以空格結束。 |
> | workspaces | 資源群組 | 1-260 | 無法使用:<br>`<>*%&:?+/\\`<br><br>不能以空格結束。 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | workspaces | 資源群組 | 3-33 | 字母數位和連字元。 |
> | 工作區/計算 | 工作區 | 2-16 | 字母數位和連字元。 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | userAssignedIdentities | 資源群組 | 3-128 | 字母數字、連字元與下劃線<br><br>以字母或數字開頭。 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | accounts | 資源群組 | 1-98(資源群組名稱和帳戶名稱) | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | mediaservices | 資源群組 | 3-24 | 小寫字母和數位。 |
> | 媒體服務 /現場活動 | 媒體服務 | 1-32 | 字母數位和連字元。<br><br>以字母數字開頭。 |
> | 媒體服務 / 現場活動 / 即時輸出 | 現場活動 | 1-256 | 字母數位和連字元。<br><br>以字母數字開頭。 |
> | 媒體服務/流式處理端點 | 媒體服務 | 1-24 | 字母數位和連字元。<br><br>以字母數字開頭。 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | applicationGateways | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | applicationSecurityGroups | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | azureFirewalls | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 以字母數位或下劃線結束。 |
> | 堡壘主機 | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | connections | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | dns 區域 | 資源群組 | 1-63 個字元<br><br>2 到 34 個分頁<br><br>每個標籤都是一組字元,由句點分隔。 例如 **,contoso.com**有 2 個標籤。 | 每個標籤可以包含字母數位、下劃線和連字元。<br><br>每個標籤由一個句點分隔。 |
> | expressRouteCircuits | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | 防火牆策略 | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | 防火牆原則/ 規則群組 | 防火牆策略 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | 前門 | 全域 | 5-64 | 字母數位和連字元。<br><br>以字母數字開始和結束。 |
> | loadBalancers | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | 負載平衡器/入站Nat規則 | 負載平衡器 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | localNetworkGateways | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | networkInterfaces | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | networkSecurityGroups | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | 網路安全群組/安全規則 | 網路安全組 | 1-80 |  字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | networkWatchers | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | 私有 Dns 區塊 | 資源群組 | 1-63 個字元<br><br>2 到 34 個分頁<br><br>每個標籤都是一組字元,由句點分隔。 例如 **,contoso.com**有 2 個標籤。 | 每個標籤可以包含字母數位、下劃線和連字元。<br><br>每個標籤由一個句點分隔。 |
> | 私人 Dns 區域 / 虛擬網路連結 | 私人 DNS 區域 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | publicIPAddresses | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | publicIPPrefixes | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | routeFilters | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | 路由篩選器/路由篩選器規則 | 路由篩選 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | routeTables | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | 路由表/ 路由 | 路由表 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | serviceEndpointPolicies | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | trafficmanagerprofiles | 全域 | 1-63 | 字母數位、連字元和句點。<br><br>以字母數字開始和結束。 |
> | virtualNetworkGateways | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | virtualNetworks | 資源群組 | 2-64 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | 虛擬網路/子網 | 虛擬網路 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | 虛擬網路/虛擬網路對等 | 虛擬網路 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | virtualWans | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | vpnGateways | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | vpn 閘道 / vpn 連線 | VPN 閘道 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |
> | vpnSites | 資源群組 | 1-80 | 字母數位、下劃線、句點和連字元。<br><br>以字母數字開頭。 結束字母數位或下劃線。 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 命名空間 | 全域 | 6-50 | 字母數字與連字元<br><br>以字母數字開始和結束。 |
> | 命名空間/授權規則 | namespace | 1-256 | 字母數位、句點、連字元和下劃線。<br><br>開始字母數位。 |
> | 命名空間/通知中心 | namespace | 1-260 | 字母數位、句點、連字元和下劃線。<br><br>開始字母數位。 |
> | 命名空間 / 通知中心 / 授權規則 | 通知中心 | 1-256 | 字母數位、句點、連字元和下劃線。<br><br>開始字母數位。 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | clusters | 資源群組 | 4-63 | 字母數位和連字元。<br><br>以字母數字開始和結束。 |
> | workspaces | 資源群組 | 4-63 | 字母數位和連字元。<br><br>以字母數字開始和結束。 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | dashboards | 資源群組 | 3-160 | 字母數位和連字元。<br><br>要使用受限字元,請使用要使用的儀表板名稱添加名為**隱藏標題**的標記。 顯示儀錶板時,門戶將顯示該名稱。 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | 字母數位和連字元。<br><br>不能以連字元開頭。 不能使用連續連字元。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | 小寫字母或數字<br><br>從小寫字母開始。 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | vaults | 資源群組 | 2-50 | 字母數位和連字元。<br><br>從字母開始。 |
> | 保存庫/備份原則 | 保存庫 | 3-150 | 字母數位和連字元。<br><br>從字母開始。 不能以連字元結束。 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 命名空間 | 全域 | 6-50 | 字母數位和連字元。<br><br>以字母開頭。 以字母或數位結尾。 |
> | 命名空間/授權規則 | namespace | 1-50 |  字母數位、句點、連字元和下劃線。<br><br>以字母數字開始和結束。 |
> | 命名空間/混合連線 | namespace | 1-260 | 字母數位、句點、連字元、下劃線和斜杠。<br><br>以字母數字開始和結束。 |
> | 命名空間 / 混合連線/ 授權規則 | 混合連線 | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以字母數字開始和結束。 |
> | 命名空間 / WcfRelay | namespace | 1-260 | 字母數位、句點、連字元、下劃線和斜杠。<br><br>以字母數字開始和結束。 |
> | 命名空間 / WcfRelay / 授權規則 | Wcf 繼電器 | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以字母數字開始和結束。 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | deployments | 資源群組 | 1-64 | 字母數位、下劃線、括弧、連字元和句點。 |
> | 資源群組 | 訂用帳戶 | 1-90 | 與[regex 文件](/rest/api/resources/resourcegroups/createorupdate)匹配的字母數位、下劃線、括弧、連字元、句點和單碼字元。<br><br>不能以句點結束。 |
> | 標籤名稱 | resource | 1-512 | 無法使用:<br>`<>%&\?/` |
> | 標記名稱/標記值 | 標記名稱 | 1-256 | 所有字元。 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 命名空間 | 全域 | 6-50 | 字母數位和連字元。<br><br>以字母開頭。 以字母或數位結尾。<br><br>有關詳細資訊,請參閱[建立命名空間](/rest/api/servicebus/create-namespace)。 |
> | 命名空間/授權規則 | namespace | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以數位開始和結束。 |
> | 命名空間 / 災難修復設定 | 全域 | 6-50 | 字母數位和連字元。<br><br>從字母開始。 以字母數字結束。 |
> | 命名空間/移轉設定 | namespace |  | 應始終 **$default。** |
> | 命名空間/佇列 | namespace | 1-260 | 字母數位、句點、連字元、下劃線和斜杠。<br><br>以字母數字開始和結束。 |
> | 命名空間/ 佇列 / 授權規則 | queue | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以數位開始和結束。 |
> | 命名空間/佈景主題 | namespace | 1-260 | 字母數位、句點、連字元、下劃線和斜杠。<br><br>以字母數字開始和結束。 |
> | 命名空間/ 佈景主題 / 授權規則 | 主題 | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以數位開始和結束。 |
> | 命名空間 / 佈景主題 / 訂閱 | 主題 | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以數位開始和結束。 |
> | 命名空間 / 佈景主題 / 訂閱 / 規則 | 訂用帳戶 | 1-50 | 字母數位、句點、連字元和下劃線。<br><br>以數位開始和結束。 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | 小寫字母、數位和連字元。<br><br>從小寫字母開始。 以小寫字母或數位結尾。 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | 信號R | 全域 | 3-63 | 字母數位和連字元。<br><br>從字母開始。 以字母或數位結尾。  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | managedInstances | 全域 | 1-63 | 小寫字母、數位和連字元。<br><br>不能以連字元開始或結尾。 |
> | servers | 全域 | 1-63 | 小寫字母、數位和連字元。<br><br>不能以連字元開始或結尾。 |
> | 伺服器/管理員 | 伺服器 |  | 必須是 `ActiveDirectory`。 |
> | 伺服器/資料庫 | 伺服器 | 1-128 | 無法使用:<br>`<>*%&:\/?`<br><br>不能以句點或空格結束。 |
> | 伺服器/ 資料庫/ 同步群組 | [資料庫] | 1-150 | 字母數位、連字元和下劃線。 |
> | 伺服器/彈性池 | 伺服器 | 1-128 | 無法使用:<br>`<>*%&:\/?`<br><br>不能以句點或空格結束。 |
> | 伺服器/容錯移轉群組 | 全域 | 1-63 | 小寫字母、數位和連字元。<br><br>不能以連字元開始或結尾。 |
> | 伺服器/防火牆規則 | 伺服器 | 1-128 | 無法使用:<br>`<>*%&:;\/?`<br><br>不能以句點結束。 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | storageAccounts | 全域 | 3-24 | 小寫字母和數位。 |
> | 儲存帳號/blob 服務 | storage account |  | 必須是 `default`。 |
> | 儲存帳號/ blob 服務 / 容器 | storage account | 3-63 | 小寫字母、數位和連字元。<br><br>從小寫字母或數位開始。 不能使用連續連字元。 |
> | 儲存帳號/檔案服務 | storage account |  | 必須是 `default`。 |
> | 儲存帳號/ 檔案服務 / 分享 | storage account | 3-63 | 小寫字母、數位和連字元。<br><br>不能以連字元開始或結尾。 不能使用連續連字元。 |
> | 儲存帳戶/管理原則 | storage account |  | 必須是 `default`。 |
> | blob | 容器 | 1-1024 | 任何 URL 字元,大小寫敏感 |
> | queue | storage account | 3-63 | 小寫字母、數位和連字元。<br><br>不能以連字元開始或結尾。 不能使用連續連字元。 |
> | 資料表 | storage account | 3-63 | 字母數位。<br><br>從字母開始。 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | storageSyncServices | 資源群組 | 1-260 | 字母數位、空格、句點、連字元和下劃線。<br><br>不能以句點或空格結束。 |
> | 儲存同步服務/同步群組 | 儲存同步服務 | 1-260 | 字母數位、空格、句點、連字元和下劃線。<br><br>不能以句點或空格結束。 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | managers | 資源群組 | 2-50 | 字母數位和連字元。<br><br>從字母開始。 以字母數字結束。 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | streamingjobs | 資源群組 | 3-63 | 字母數位、連字元和下劃線。 |
> | 流式處理/函數 | 流作業 | 3-63 | 字母數位、連字元和下劃線。 |
> | 流式處理/輸入 | 流作業 | 3-63 | 字母數位、連字元和下劃線。 |
> | 流式處理/輸出 | 流作業 | 3-63 | 字母數位、連字元和下劃線。 |
> | 流式處理/轉換 | 流作業 | 3-63 | 字母數位、連字元和下劃線。 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | environments | 資源群組 | 1-90 | 無法使用:<br>`'<>%&:\?/#` |
> | 環境/存取原則 | Environment | 1-90 | 無法使用:<br> `'<>%&:\?/#` |
> | 環境/事件來源 | Environment | 1-90 | 無法使用:<br>`'<>%&:\?/#` |
> | 環境/參考資料集集 | Environment | 3-63 | 字母數字 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 單位 | 影響範圍 | 長度 | 有效字元 |
> | --- | --- | --- | --- |
> | serverfarms | 資源群組 | 1-40 | 字母數位和連字元。 |
> | sites | 全域 | 2-60 | 包含字母數字和連字元。<br><br>不能以連字元開始或結尾。 |
> | 網站/插槽 | site | 2-59 | 字母數位和連字元。 |

## <a name="next-steps"></a>後續步驟

有關如何命名資源的建議,請參閱[「就緒:建議命名和標記約定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)」。
