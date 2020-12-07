---
title: 刪除 Azure Migrate 專案
description: 在本文中，您將瞭解如何使用 Azure 入口網站來刪除 Azure Migrate 專案。
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: face3d02ee72d1e05c6c08330dae4fffc2fd0e0b
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754244"
---
# <a name="delete-an-azure-migrate-project"></a>刪除 Azure Migrate 專案

本文說明如何刪除 [Azure Migrate](./migrate-services-overview.md) 專案。


## <a name="before-you-start"></a>開始之前

刪除專案之前：

- 當您刪除專案時，會刪除專案，並刪除探索到的電腦中繼資料。
- 如果您已將 Log Analytics 工作區附加至伺服器評定工具以進行相依性分析，請決定是否要刪除工作區。 
    - 工作區不會自動刪除。 請手動將其刪除。
    - 在刪除工作區之前，請先確認該工作區的用途。 相同的 Log Analytics 工作區可用於多個案例。
    - 刪除專案之前，您可以在 [OMS 工作區] 下的 [ **Azure Migrate-伺服器**  >  **Azure Migrate-伺服器評定**] 中 **OMS Workspace** 找到工作區的連結。
    - 若要在刪除專案之後刪除工作區，請在相關的資源群組中尋找工作區，然後依照 [這些指示](../azure-monitor/platform/delete-workspace.md)進行。


## <a name="delete-a-project"></a>刪除專案


1. 在 Azure 入口網站中，開啟專案建立所在的資源群組。
2. 在 [資源群組] 頁面中，選取 [ **顯示隱藏的類型**]。
3. 選取您要刪除的專案和相關聯的資源。
    - Azure Migrate 專案的資源類型為 **Microsoft. 遷移/migrateprojects**。
    - 在下一節中，請參閱在 Azure Migrate 專案中建立以進行探索、評估和遷移的資源。
    - 如果資源群組只包含 Azure Migrate 的專案，您可以刪除整個資源群組。
    - 如果您想要從舊版 Azure Migrate 中刪除專案，步驟也相同。 這些專案的資源類型為「 **遷移專案**」。


## <a name="created-resources"></a>建立的資源

這些表格摘要說明在 Azure Migrate 專案中，針對探索、評量和遷移所建立的資源。

> [!NOTE]
> 請小心刪除金鑰保存庫，因為它可能包含安全性金鑰。

### <a name="vmwarephysical-server"></a>VMware/實體伺服器

**Resource** | **類型**
--- | ---
"Appliancename" kv | 金鑰保存庫
"Appliancename" 網站 | Microsoft.offazure/VMwareSites
ProjectName | Microsoft. 遷移/migrateprojects
"專案名稱" 專案 | Microsoft. 遷移/assessmentProjects
"專案名稱" rsvault | 復原服務保存庫
"專案名稱"-MigrateVault-* | 復原服務保存庫
migrateappligwsa* | 儲存體帳戶
migrateapplilsa* | 儲存體帳戶
migrateapplicsa* | 儲存體帳戶
migrateapplikv* | 金鑰保存庫
migrateapplisbns16041 | 服務匯流排 命名空間

### <a name="hyper-v-vm"></a>Hyper-V VM 

**Resource** | **類型**
--- | ---
ProjectName | Microsoft. 遷移/migrateprojects
"專案名稱" 專案 | Microsoft. 遷移/assessmentProjects
HyperV * kv | 金鑰保存庫
HyperV * 網站 | Microsoft.offazure/HyperVSites
"專案名稱"-MigrateVault-* | 復原服務保存庫


## <a name="next-steps"></a>後續步驟

瞭解如何新增額外的 [評定](how-to-assess.md) 及 [遷移](how-to-migrate.md) 工具。 
