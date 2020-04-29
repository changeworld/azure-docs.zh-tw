---
title: 保護您的 Synapse 工作區（預覽）
description: 本文將告訴您如何使用角色和存取控制來控制活動，以及存取 Synapse 工作區中的資料。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: ae8be848b5d12e01865fe6bd3b394b460252aa3e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606014"
---
# <a name="secure-your-synapse-workspace-preview"></a>保護您的 Synapse 工作區（預覽）

本文將告訴您如何使用角色和存取控制來控制活動和資料的存取權。 遵循這些指示，即可簡化 Azure Synapse 分析中的存取控制。 您只需要將使用者新增至三個安全性群組的其中一個，並將其移除。

## <a name="overview"></a>概觀

若要保護 Synapse 工作區（預覽），您將遵循設定下列專案的模式：

- Azure 角色（例如「擁有者」、「參與者」等內建的角色）
- Synapse 角色–這些角色對於 Synapse 而言是唯一的，而且不是以 Azure 角色為基礎。 其中有三個角色：
  - Synapse 工作區系統管理員
  - Synapse SQL 系統管理員
  - Synapse Spark 系統管理員
- Azure Data Lake Storage Gen 2 （ADLSGEN2）中的資料存取控制。
- Synapse SQL 和 Spark 資料庫的存取控制

## <a name="steps-to-secure-a-synapse-workspace"></a>保護 Synapse 工作區的步驟

本檔使用標準名稱來簡化指示。 將其取代為您選擇的任何名稱。

|設定 | 範例值 | 描述 |
| :------ | :-------------- | :---------- |
| **Synapse 工作區** | WS1 |  Synapse 工作區將會擁有的名稱。 |
| **ADLSGEN2 帳戶** | STG1 | 要與您的工作區搭配使用的 ADLS 帳戶。 |
| **容器** | CNT1 | 預設會使用工作區 STG1 中的容器。 |
| **Active directory 租使用者** | contoso | active directory 租使用者名稱。|
||||

## <a name="step-1-set-up-security-groups"></a>步驟1：設定安全性群組

建立並填入您工作區的三個安全性群組：

- **WS1\_WSAdmins** –適用于需要完整控制工作區的使用者
- **WS1\_SparkAdmins** –適用于需要完整控制工作區 Spark 層面的使用者
- **WS1\_SQLAdmins** –適用于需要完整控制工作區之 SQL 層面的使用者
- 將**WS1\_WSAdmins**新增**至\_WS1 SQLAdmins**
- 將**WS1\_WSAdmins**新增**至\_WS1 SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>步驟2：準備您的 Data Lake Storage Gen2 帳戶

識別儲存體的相關資訊：

- 要用於工作區的 ADLSGEN2 帳戶。 本檔會將它 STG1。  STG1 會被視為您工作區的「主要」儲存體帳戶。
- 您的 Synapse 工作區預設會使用 WS1 內的容器。 本檔會將它 CNT1。  此容器用於：
  - 儲存 Spark 資料表的支援資料檔案
  - Spark 作業的執行記錄

- 使用 Azure 入口網站，將 CNT1 上的下列角色指派給安全性群組

  - 將**WS1\_WSAdmins**指派給**儲存體 Blob 資料參與者**角色
  - 將**WS1\_SparkAdmins**指派給**儲存體 Blob 資料參與者**角色
  - 將**WS1\_SQLAdmins**指派給**儲存體 Blob 資料參與者**角色

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>步驟3：建立和設定您的 Synapse 工作區

在 Azure 入口網站中，建立 Synapse 工作區：

- 將工作區命名為 WS1
- 選擇儲存體帳戶的 [STG1]
- 針對做為「檔案系統」使用的容器，選擇 [CNT1]。
- 在 Synapse Studio 中開啟 WS1
- 選取 [**管理** > ]**存取控制**將安全性群組指派給下列 Synapse 角色。
  - 將**WS1\_WSAdmins**指派給 Synapse 工作區系統管理員
  - 將**WS1\_SparkAdmins**指派給 Synapse Spark 系統管理員
  - 將**WS1\_SQLAdmins**指派給 Synapse SQL 系統管理員

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>步驟4：設定 Synapse 工作區所使用的 Data Lake Storage Gen2

Synapse 工作區需要 STG1 和 CNT1 的存取權，才能執行管線和系統工作。

- 開啟 Azure 入口網站
- 找出 STG1
- 流覽至 CNT1
- 請確定 WS1 的 MSI （受控服務識別）已指派給 CNT1 上的**Azure Blob 資料參與者**角色
  - 如果您沒有看到它已指派，請將它指派給它。
  - MSI 的名稱與工作區相同。 在此情況下，它會&quot;是&quot;WS1。

## <a name="step-5-configure-admin-access-for-sql-pools"></a>步驟5：設定 SQL 集區的系統管理員存取權

- 開啟 Azure 入口網站
- 流覽至 WS1
- 在 [**設定**] 底下，按一下 **[SQL Active Directory 管理員**]
- 按一下 [**設定系統管理員**]\_，然後選擇 [WS1 SQLAdmins]

## <a name="step-6-maintaining-access-control"></a>步驟6：維護存取控制

設定已完成。

現在，若要管理使用者的存取權，您可以在三個安全性群組中新增和移除使用者。

雖然您可以手動將使用者指派給 Synapse 角色，但如果這麼做，則不會一致地設定專案。 相反地，只在安全性群組中新增或移除使用者。

## <a name="step-7-verify-access-for-users-in-the-roles"></a>步驟7：驗證角色中使用者的存取權

每個角色中的使用者都需要完成下列步驟：

|   | 步驟 | 工作區系統管理員 | Spark 管理員 | SQL 系統管理員 |
| --- | --- | --- | --- | --- |
| 1 | 將 parquet 檔案上傳至 CNT1 | YES | YES | YES |
| 2 | 使用 SQL 隨選閱讀 parquet 檔案 | YES | 否 | YES |
| 3 | 建立 Spark 集區 | 是 [1] | 是 [1] | 否  |
| 4 | 使用筆記本讀取 parquet 檔案 | YES | YES | 否 |
| 5 | 從筆記本建立管線並觸發管線以立即執行 | YES | 否 | 否 |
| 6 | 建立 SQL 集區並執行 SQL 腳本，例如&quot;SELECT 1&quot; | 是 [1] | 否 | 是 [1] |

> [!NOTE]
> [1] 若要建立 SQL 或 Spark 集區，使用者在 [Synapse] 工作區上至少必須擁有 [參與者] 角色。
> [!TIP]
>
> - 根據角色而定，會刻意不允許某些步驟。
> - 請記住，如果未完整設定安全性，某些工作可能會失敗。 這些工作會在表格中注明。

## <a name="step-8-network-security"></a>步驟8：網路安全性

設定工作區防火牆、虛擬網路和[私人連結](../../sql-database/sql-database-private-endpoint-overview.md)。

## <a name="step-9-completion"></a>步驟9：完成

您的工作區現在已完全設定並受到保護。

## <a name="how-roles-interact-with-synapse-studio"></a>角色與 Synapse Studio 的互動方式

Synapse Studio 的行為會根據使用者角色而有所不同。 如果使用者未指派給授與適當存取權的角色，某些專案可能會隱藏或停用。 下表摘要說明 Synapse Studio 的效果。

| 工作 | 工作區管理員 | Spark 管理員 | SQL 系統管理員 |
| --- | --- | --- | --- |
| 開啟 Synapse Studio | YES | YES | YES |
| 觀看首頁中樞 | YES | YES | YES |
| 查看資料中樞 | YES | YES | YES |
| 資料中樞/請參閱連結的 ADLSGen2 帳戶和容器 | 是 [1] | 是 [1] | 是 [1] |
| 資料中樞/請參閱資料庫 | YES | YES | YES |
| 資料中樞/請參閱資料庫中的物件 | YES | YES | YES |
| SQL 集區資料庫中的資料中樞/存取資料 | YES   | 否   | YES   |
| SQL 隨選資料庫中的資料中樞/存取資料 | 是 [2]  | 否  | 是 [2]  |
| Spark 資料庫中的資料中樞/存取資料 | 是 [2] | 是 [2] | 是 [2] |
| 使用開發中樞 | YES | YES | YES |
| 開發中樞/作者 SQL 腳本 | YES | 否 | YES |
| 開發中樞/作者 Spark 作業定義 | YES | YES | 否 |
| 開發中樞/作者筆記本 | YES | YES | 否 |
| 開發中樞/作者資料流程 | YES | 否 | 否 |
| 使用協調中樞 | YES | YES | YES |
| 協調中樞/使用管線 | YES | 否 | 否 |
| 使用管理中樞 | YES | YES | YES |
| 管理中樞/SQL 集區 | YES | 否 | YES |
| 管理中樞/Spark 集區 | YES | YES | 否 |
| 管理中樞/觸發程式 | YES | 否 | 否 |
| 管理中樞/連結服務 | YES | YES | YES |
| 管理中樞/存取控制（將使用者指派給 Synapse 工作區角色） | YES | 否 | 否 |
| 管理中樞/整合執行時間 | YES | YES | YES |

> [!NOTE]
> [1] 容器中資料的存取權取決於 ADLSGen2 [2] SQL OD 資料表和 Spark 資料表中的存取控制將其資料儲存在 ADLSGen2 中，而 access 需要 ADLSGen2 的適當許可權。

## <a name="next-steps"></a>後續步驟

建立[Synapse 工作區](../quickstart-create-workspace.md)
