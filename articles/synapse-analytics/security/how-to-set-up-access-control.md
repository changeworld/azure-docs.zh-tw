---
title: 保護您的 Synapse 工作區(預覽版)
description: 本文將教您如何使用角色和存取控制來控制 Synapse 工作區中的活動和數據訪問。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: ae8be848b5d12e01865fe6bd3b394b460252aa3e
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606014"
---
# <a name="secure-your-synapse-workspace-preview"></a>保護您的 Synapse 工作區(預覽版)

本文將教您如何使用角色和訪問控制來控制活動和訪問數據。 通過按照這些說明,Azure 突觸分析中的訪問控制得到了簡化。 您只需將使用者添加到三個安全組之一。

## <a name="overview"></a>概觀

要保護 Synapse 工作區(預覽),您將遵循設定以下項目的模式:

- Azure 角色(如內置角色,如擁有者、參與者等)
- 突觸角色 – 這些角色是 Synapse 獨有的,不基於 Azure 角色。 有以下三個角色:
  - 突觸工作區管理員
  - 突觸 SQL 管理員
  - 突觸火花管理員
- Azure 資料儲存第 2 代 (ADLSGEN2) 中的數據的訪問控制。
- Synapse SQL 和 Spark 資料庫的存取控制

## <a name="steps-to-secure-a-synapse-workspace"></a>保護 Synapse 工作區的步驟

本文檔使用標準名稱來簡化說明。 用您選擇的任何名稱替換它們。

|設定 | 範例值 | 描述 |
| :------ | :-------------- | :---------- |
| **突觸工作區** | WS1 |  Synapse 工作區的名稱。 |
| **ADLSGEN2 帳號** | STG1 | 要與您的工作區一起使用的 ADLS 帳戶。 |
| **容器** | CNT1 | 預設情況下工作區將使用的 STG1 中的容器。 |
| **活動目錄租戶** | contoso | 活動目錄租戶名稱。|
||||

## <a name="step-1-set-up-security-groups"></a>第一步:設定安全性群組

為工作區建立與填充三個安全組:

- **WS1\_WSAdmins** – 適用於需要完全控制工作區的使用者
- **WS1\_SparkAdmins** – 適用於需要完全控制工作區 Spark 方面的使用者
- **WS1\_SQLAdmins** – 適用於需要完全控制工作區 SQL 方面的使用者
- 將**WS1\_WSAdmin 新增到** **WS1\_SQLAdmin**
- 將**WS1\_WSAdmin 新增到** **WS1\_SparkAdmin**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>第二步:準備您的資料儲存第 2 代帳戶

識別有關儲存的資訊:

- 用於工作區的 ADLSGEN2 帳戶。 本文檔稱其為STG1。  STG1 被視為工作區的"主"存儲帳戶。
- 預設情況下,Synapse 工作區將使用的 WS1 中的容器。 本文檔稱其為 CNT1。  此容器用於:
  - 儲存 Spark 表的備份資料檔
  - Spark 工作執行紀錄

- 使用 Azure 門戶,在 CNT1 上分配以下安全組

  - 將**\_WS1 WSAdmin 分配儲存**的 Blob**資料參與者**角色
  - 將**WS1\_SparkAdmin 分配給**儲存 Blob**資料參與者**角色
  - 將**WS1\_SQLAdmin 分配儲存**的檢視**器的總體碼者**角色

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>第三步:建立與設定 Synape 工作區

在 Azure 門戶中,建立 Synape 工作區:

- 命名工作區 WS1
- 為儲存帳戶選擇 STG1
- 為用作「檔案系統」的容器選擇 CNT1。
- 在 Synapse 工作室開啟 WS1
- 選擇管理**Manage** > **存取控制**「將安全組分配給以下 Synapse 角色。
  - 將**WS1\_WSAdmin 分配給**Synapse 工作區管理員
  - 將**WS1\_SparkAdmin 分配給**Synapse Spark 管理員
  - 將**WS1\_SQLAdmin 分配給**Synapse SQL 管理員

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>第四步:設定資料湖儲存 Gen2 供 Synapse 工作區使用

Synapse 工作區需要訪問 STG1 和 CNT1,以便它可以運行管道並執行系統任務。

- 開啟 Azure 入口網站
- 找到 STG1
- 導航到 CNT1
- 確保 WS1 的 MSI(託管服務識別)分配給 CNT1 上的**Azure Blob 資料參與者**角色
  - 如果未看到它分配,請分配它。
  - MSI 的名稱與工作區相同。 在這種情況下,它將是&quot;WS1&quot;。

## <a name="step-5-configure-admin-access-for-sql-pools"></a>第 5 步:為 SQL 池設定管理員存取權限

- 開啟 Azure 入口網站
- 導覽到 WS1
- 在 **「設定」** 下,按**下 SQL 活動目錄管理員**
- 點選 **「設定管理員」**\_並選擇 WS1 SQLAdmin

## <a name="step-6-maintaining-access-control"></a>第六步:保持存取控制

配置已完成。

現在,為了管理使用者的訪問許可權,您可以將使用者添加到三個安全組。

儘管您可以手動將使用者分配給 Synapse 角色,但如果這樣做,它將無法一致地配置內容。 相反,僅向安全組添加或刪除使用者。

## <a name="step-7-verify-access-for-users-in-the-roles"></a>第 7 步:驗證角色中使用者的存取

每個角色中的使用者需要完成以下步驟:

|   | 步驟 | 工作區系統管理員 | 火花管理員 | SQL 管理員 |
| --- | --- | --- | --- | --- |
| 1 | 將鑲木地板檔上傳到 CNT1 | YES | YES | YES |
| 2 | 依使用 SQL 讀取鑲木地板檔 | YES | 否 | YES |
| 3 | 建立火花池 | 是 [1] | 是 [1] | 否  |
| 4 | 使用筆記本讀取鑲木地板檔 | YES | YES | 否 |
| 5 | 從筆記本建立管道,並觸發管道立即執行 | YES | 否 | 否 |
| 6 | 建立 SQL 池並執行 SQL 文稿&quot;,如 SELECT 1&quot; | 是 [1] | 否 | 是[1] |

> [!NOTE]
> [1] 要建立 SQL 或 Spark 池,用戶必須在 Synapse 工作區上至少具有"參與者"角色。
> [!TIP]
>
> - 根據角色的不同,某些步驟將故意不允許。
> - 請記住,如果安全性未完全配置,則某些任務可能會失敗。 這些任務在表中註明。

## <a name="step-8-network-security"></a>第 8 步:網路安全

設定工作區防火牆、虛擬網路和[專用鏈路](../../sql-database/sql-database-private-endpoint-overview.md)。

## <a name="step-9-completion"></a>第 9 步:完成

您的工作區現已完全配置和保護。

## <a name="how-roles-interact-with-synapse-studio"></a>角色如何與 Synapse 工作室互動

Synapse 工作室將根據使用者角色的不同表現。 如果使用者未分配到提供適當訪問許可權的角色,則某些專案可能被隱藏或禁用。 下表總結了對 Synapse 工作室的影響。

| Task | 工作區管理員 | 火花管理員 | SQL 管理員 |
| --- | --- | --- | --- |
| 開啟同步工作室 | YES | YES | YES |
| 檢視首頁中心 | YES | YES | YES |
| 檢視資料中心 | YES | YES | YES |
| 資料中心 /請參考連結的 ADLSGen2 帳號和容器 | 是 [1] | 是[1] | 是[1] |
| 資料中心/請參閱資料庫 | YES | YES | YES |
| 資料中心/檢視資料庫中的物件 | YES | YES | YES |
| 資料中心/存取 SQL 池資料庫中的資料 | YES   | 否   | YES   |
| 資料中心/存取 SQL 依需資料庫中的資料 | 是 [2]  | 否  | 是 [2]  |
| 資料中心/Spark 資料庫中的存取資料 | 是 [2] | 是 [2] | 是 [2] |
| 使用「開發」中心 | YES | YES | YES |
| 開發中心/作者 SQL 文稿 | YES | 否 | YES |
| 開發中心/作者 Spark 作業定義 | YES | YES | 否 |
| 開發中心/作者筆記本 | YES | YES | 否 |
| 開發中心/作者資料串流 | YES | 否 | 否 |
| 使用協調中心 | YES | YES | YES |
| 協調集線器/使用管道 | YES | 否 | 否 |
| 使用管理中心 | YES | YES | YES |
| 管理中心/SQL 池 | YES | 否 | YES |
| 管理中心/火花池 | YES | YES | 否 |
| 管理中心/觸發器 | YES | 否 | 否 |
| 管理中心/連結服務 | YES | YES | YES |
| 管理中心/存取控制(將使用者配置給 Synapse 工作區角色) | YES | 否 | 否 |
| 管理中心/整合式執行時 | YES | YES | YES |

> [!NOTE]
> [1] 容器中對資料的訪問取決於 ADLSGen2 [2] SQL OD 表中的存取控制,Spark 表將其資料存儲在 ADLSGen2 中,並且存取需要對 ADLSGen2 具有適當的許可權。

## <a name="next-steps"></a>後續步驟

建立[突觸工作區](../quickstart-create-workspace.md)
