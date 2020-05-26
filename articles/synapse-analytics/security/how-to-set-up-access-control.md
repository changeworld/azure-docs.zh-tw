---
title: 保護您的 Synapse 工作區 (預覽)
description: 本文將告訴您如何使用角色和存取控制來控制活動，以及存取 Synapse 工作區中的資料。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: d02cd12552b3664dd7acaae0142fc939ee57f5f6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591976"
---
# <a name="secure-your-synapse-workspace-preview"></a>保護您的 Synapse 工作區 (預覽)

本文將告訴您如何使用角色和存取控制來控制活動及存取資料。 遵循這些指示，即可簡化 Azure Synapse Analytics 中的存取控制。 您只需要對三個安全性群組的其中一個新增及移除使用者。

## <a name="overview"></a>概觀

為保護 Synapse 工作區 (預覽)，您將遵循設定下列項目的模式：

- Azure 角色 (例如「擁有者」和「參與者」等內建角色)
- Synapse 角色 – 這些角色對於 Synapse 而言是唯一的，而且不是以 Azure 角色為基礎。 其中有三個角色：
  - Synapse 工作區管理員
  - Synapse SQL 管理員
  - 適用於 Azure Synapse Analytics 的 Apache Spark 管理員
- Azure Data Lake Storage Gen 2 (ADLSGEN2) 中的資料存取控制。
- Synapse SQL 和 Spark 資料庫的存取控制

## <a name="steps-to-secure-a-synapse-workspace"></a>保護 Synapse 工作區的步驟

本文件會使用標準名稱來簡化指示。 請將其取代為您選擇的任何名稱。

|設定 | 範例值 | 描述 |
| :------ | :-------------- | :---------- |
| **Synapse 工作區** | WS1 |  Synapse 工作區將會擁有的名稱。 |
| **ADLSGEN2 帳戶** | STG1 | 要與您工作區搭配使用的 ADLS 帳戶。 |
| **容器** | CNT1 | STG1 中工作區預設會使用的容器。 |
| **Active Directory 租用戶** | contoso | Active Directory 租用戶名稱。|
||||

## <a name="step-1-set-up-security-groups"></a>步驟 1：設定安全性群組

建立並填入適用於您工作區的三個安全性群組：

- **WS1\_WSAdmins** – 適用於需要完整控制工作區的使用者
- **WS1\_SparkAdmins** – 適用於需要完整控制工作區 Spark 層面的使用者
- **WS1\_SQLAdmins** – 適用於需要完整控制工作區 SQL 層面的使用者
- 將 **WS1\_WSAdmins** 新增至 **WS1\_SQLAdmins**
- 將 **WS1\_WSAdmins** 新增至 **WS1\_SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>步驟 2：準備 Azure Data Lake Storage Gen2 帳戶

識別儲存體的此資訊：

- 要用於工作區的 ADLSGEN2 帳戶。 本文件將其稱為 STG1。  STG1 會被視為您工作區的「主要」儲存體帳戶。
- WS1 中您 Synapse 工作區預設會使用的容器。 本文件將其稱為 CNT1。  此容器用於：
  - 儲存 Spark 資料表的支援資料檔案
  - Spark 作業的執行記錄

- 使用 Azure 入口網站，將 CNT1 上的下列角色指派給安全性群組

  - 將 **WS1\_WSAdmins** 指派給**儲存體 Blob 資料參與者**角色
  - 將 **WS1\_SparkAdmins** 指派給**儲存體 Blob 資料參與者**角色
  - 將 **WS1\_SQLAdmins** 指派給**儲存體 Blob 資料參與者**角色

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>步驟 3：建立和設定您的 Synapse 工作區

在 Azure 入口網站中，建立 Synapse 工作區：

- 將工作區命名為 WS1
- 選擇儲存體帳戶的 STG1
- 針對將作為「檔案系統」使用的容器，選擇 [CNT1]。
- 在 Synapse Studio 中開啟 WS1
- 選取 [管理] > [存取控制]，將安全性群組指派給下列 Synapse 角色。
  - 將 **WS1\_WSAdmins** 指派給 Synapse 工作區管理員
  - 將 **WS1\_SparkAdmins** 指派給 Synapse Spark 管理員
  - 將 **WS1\_SQLAdmins** 指派給 Synapse SQL 管理員

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>步驟4：設定 Synapse 工作區所使用的 Data Lake Storage Gen2

Synapse 工作區需要 STG1 和 CNT1 的存取權，才能執行管線和系統工作。

- 開啟 Azure 入口網站
- 找出 STG1
- 瀏覽至 CNT1
- 請確定 WS1 的 MSI (受控服務識別) 已指派給 CNT1 上的**儲存體 Blob 資料參與者**角色
  - 如果您沒有看到 MSI 已指派，請進行指派。
  - MSI 的名稱與工作區相同。 在案例中，此名稱會是 &quot;WS1&quot;。

## <a name="step-5-configure-admin-access-for-sql-pools"></a>步驟 5：設定 SQL 集區的理員存取權

- 開啟 Azure 入口網站
- 瀏覽至 WS1
- 在 [設定] 底下，按一下 [SQL Active Directory 管理員]
- 按一下 [設定系統管理員]，然後選擇 [WS1\_SQLAdmins]

## <a name="step-6-maintaining-access-control"></a>步驟 6：維護存取控制

設定已完成。

現在，若要管理使用者的存取權，您可以在三個安全性群組中新增和移除使用者。

雖然您可以手動將使用者指派給 Synapse 角色，但如果這麼做，設定項目並不會一致。 因此，請只在安全性群組中新增或移除使用者。

## <a name="step-7-verify-access-for-users-in-the-roles"></a>步驟 7：驗證角色中的使用者存取權

每個角色中的使用者都需要完成下列步驟：

|   | 步驟 | 工作區管理員 | Spark 管理員 | SQL 管理員 |
| --- | --- | --- | --- | --- |
| 1 | 將 Parquet 檔案上傳至 CNT1 | YES | YES | YES |
| 2 | 使用 SQL 隨選讀取 Parquet 檔案 | YES | 否 | YES |
| 3 | 建立 Spark 集區 | 是 [1] | 是 [1] | 否  |
| 4 | 使用 Notebook 讀取 Parquet 檔案 | YES | YES | 否 |
| 5 | 從 Notebook 建立管線並觸發管線立即執行 | YES | 否 | 否 |
| 6 | 建立 SQL 集區並執行 SQL 指令碼，例如 &quot;SELECT 1&quot; | 是 [1] | 否 | 是 [1] |

> [!NOTE]
> [1] 若要建立 SQL 或 Spark 集區，使用者在 Synapse 工作區上必須至少擁有「參與者」角色。
> [!TIP]
>
> - 有些步驟會刻意不讓某些角色執行。
> - 請注意，如果未完整設定安全性，某些工作可能會失敗。 這些工作會在資料表中說明。

## <a name="step-8-network-security"></a>步驟 8：網路安全性

設定工作區防火牆、虛擬網路和 [Private Link](../../sql-database/sql-database-private-endpoint-overview.md)。

## <a name="step-9-completion"></a>步驟 9：Completion

您的工作區現在已完整設定並受到保護。

## <a name="how-roles-interact-with-synapse-studio"></a>角色與 Synapse Studio 互動的方式

Synapse Studio 的行為會因為使用者角色不同而有所差異。 如果使用者未指派給具有適當存取權的角色，則某些項目可能會隱藏或停用。 下表摘要說明在 Synapse Studio 上的影響。

| Task | 工作區管理員 | Spark 管理員 | SQL 管理員 |
| --- | --- | --- | --- |
| 開啟 Synapse Studio | YES | YES | YES |
| 檢視首頁中樞 | YES | YES | YES |
| 檢視資料中樞 | YES | YES | YES |
| 資料中樞/查看閱連結的 ADLS Gen2 帳戶和容器 | 是 [1] | 是 [1] | 是 [1] |
| 資料中樞/查看資料庫 | YES | YES | YES |
| 資料中樞/查看資料庫中的物件 | YES | YES | YES |
| 資料中樞/存取 SQL 集區資料庫中的資料 | YES   | 否   | YES   |
| 資料中樞/存取 SQL 隨選資料庫中的資料 | 是 [2]  | 否  | 是 [2]  |
| 資料中樞/存取 Spark 資料庫中的資料 | 是 [2] | 是 [2] | 是 [2] |
| 使用開發中樞 | YES | YES | YES |
| 開發中樞/撰寫 SQL 指令碼 | YES | 否 | YES |
| 開發中樞/撰寫 Spark 作業定義 | YES | YES | 否 |
| 開發中樞/撰寫 Notebook | YES | YES | 否 |
| 開發中樞/撰寫資料流程 | YES | 否 | 否 |
| 使用協調中樞 | YES | YES | YES |
| 協調中樞/使用管線 | YES | 否 | 否 |
| 使用管理中樞 | YES | YES | YES |
| 管理中樞/SQL 集區 | YES | 否 | YES |
| 管理中樞/Spark 集區 | YES | YES | 否 |
| 管理中樞/觸發程序 | YES | 否 | 否 |
| 管理中樞/連結服務 | YES | YES | YES |
| 管理中樞/存取控制 (將使用者指派給 Synapse 工作區角色) | YES | 否 | 否 |
| 管理中樞/整合執行階段 | YES | YES | YES |
| 使用監視中樞 | YES | YES | YES |
| 監視中樞/協調流程/管線執行  | YES | 否 | 否 |
| 監視中樞/協調流程/觸發程序執行  | YES | 否 | 否 |
| 監視中樞/協調流程/整合執行階段  | YES | YES | YES |
| 監視中樞/活動/Spark 應用程式 | YES | YES | 否  |
| 監視中樞/活動/SQL 要求 | YES | 否 | YES |
| 監視中樞/活動/Spark 集區 | YES | YES | 否  |
| 監視中樞/觸發程序 | YES | 否 | 否 |
| 管理中樞/連結服務 | YES | YES | YES |
| 管理中樞/存取控制 (將使用者指派給 Synapse 工作區角色) | YES | 否 | 否 |
| 管理中樞/整合執行階段 | YES | YES | YES |


> [!NOTE]
> [1] 存取容器中的資料取決於 ADLS Gen2 中的存取控制。 </br>
> [2] SQL OD 資料表和 Spark 資料表會將其資料儲存在 ADLS Gen2 中，而且需要 ADLS Gen2 的適當權限才能存取。

## <a name="next-steps"></a>後續步驟

建立 [Synapse 工作區](../quickstart-create-workspace.md)
