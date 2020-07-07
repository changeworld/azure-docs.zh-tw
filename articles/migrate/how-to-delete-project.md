---
title: 刪除 Azure Migrate 專案
description: 說明如何建立 Azure Migrate 專案，以及新增評量/遷移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "73512726"
---
# <a name="delete-an-azure-migrate-project"></a>刪除 Azure Migrate 專案

本文說明如何刪除[Azure Migrate](migrate-overview.md)專案。


## <a name="before-you-start"></a>在您開始使用 Intune 之前

刪除專案之前：

- 當您刪除專案時，會刪除專案和探索到的電腦中繼資料。
- 如果您已將 Log Analytics 工作區附加至伺服器評估工具以進行相依性分析，請決定是否要刪除工作區。 
    - 不會自動刪除工作區。 請手動將其刪除。
    - 請先確認工作區的用途，再將其刪除。 相同的 Log Analytics 工作區可以用於多個案例。
    - 刪除專案之前，您可以在 [OMS 工作區] 下的 [ **Azure Migrate-** 伺服器  >  **Azure Migrate-伺服器評估**] 中**OMS Workspace**找到工作區的連結。
    - 若要在刪除專案之後刪除工作區，請尋找相關資源群組中的工作區，並遵循[這些指示](../azure-monitor/platform/delete-workspace.md)。


## <a name="delete-a-project"></a>刪除專案


1. 在 [Azure 入口網站中，開啟專案建立所在的資源群組。
2. 在 [資源群組] 頁面中，選取 [**顯示隱藏的類型**]。
3. 選取您想要刪除的專案和相關聯的資源。
    - Azure Migrate 專案的資源類型為**Microsoft. 遷移/migrateprojects**。
    - 在下一節中，請參閱在 Azure Migrate 專案中針對探索、評估和遷移所建立的資源。
    - 如果資源群組只包含 Azure Migrate 專案，您可以刪除整個資源群組。
    - 如果您想要刪除舊版 Azure Migrate 的專案，則步驟相同。 這些專案的資源類型是 [**遷移專案**]。


## <a name="created-resources"></a>建立的資源

這些資料表摘要說明在 Azure Migrate 專案中針對探索、評估和遷移所建立的資源。

> [!NOTE]
> 刪除金鑰保存庫時請小心，因為它可能包含安全性金鑰。

### <a name="vmwarephysical-server"></a>VMware/實體伺服器

**Resource** | **型別**
--- | ---
"Appliancename" kv | 金鑰保存庫
"Appliancename" 網站 | OffAzure/VMwareSites
名 | Microsoft. 遷移/migrateprojects
「專案名稱」專案 | Microsoft. 遷移/assessmentProjects
"專案名稱" rsvault | 復原服務保存庫
"專案名稱"-MigrateVault-* | 復原服務保存庫
migrateappligwsa* | 儲存體帳戶
migrateapplilsa* | 儲存體帳戶
migrateapplicsa* | 儲存體帳戶
migrateapplikv* | 金鑰保存庫
migrateapplisbns16041 | 服務匯流排 命名空間

### <a name="hyper-v-vm"></a>Hyper-V VM 

**Resource** | **型別**
--- | ---
名 | Microsoft. 遷移/migrateprojects
「專案名稱」專案 | Microsoft. 遷移/assessmentProjects
HyperV * kv | 金鑰保存庫
HyperV * 網站 | OffAzure/HyperVSites
"專案名稱"-MigrateVault-* | 復原服務保存庫


## <a name="next-steps"></a>後續步驟

瞭解如何新增其他[評估](how-to-assess.md)和[遷移](how-to-migrate.md)工具。 
