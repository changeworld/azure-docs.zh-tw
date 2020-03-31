---
title: 刪除 Azure Migrate 專案
description: 介紹如何創建 Azure 遷移專案並添加評估/遷移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512726"
---
# <a name="delete-an-azure-migrate-project"></a>刪除 Azure Migrate 專案

本文介紹如何刪除 Azure[遷移](migrate-overview.md)專案。


## <a name="before-you-start"></a>開始之前

在刪除專案之前：

- 刪除專案時，將刪除專案和發現的電腦中繼資料。
- 如果將日誌分析工作區附加到伺服器評估工具以進行分析，請決定是否要刪除工作區。 
    - 不會自動刪除工作區。 手動刪除它。
    - 在刪除工作區之前，請驗證工作區的用途。 同一日誌分析工作區可用於多個方案。
    - 在刪除專案之前，可以在**OMS 工作區**下在**Azure 遷移 - 伺服器** > **Azure 遷移 - 伺服器評估**中找到指向工作區的連結。
    - 要刪除專案後刪除工作區，請查找相關資源組中的工作區，然後按照[這些說明操作](../azure-monitor/platform/delete-workspace.md)。


## <a name="delete-a-project"></a>刪除專案


1. 在 Azure 門戶中，打開在其中創建專案的資源組。
2. 在資源組頁中，選擇 **"顯示隱藏類型**"。
3. 選擇要刪除的專案和相關資源。
    - Azure 遷移專案的資源類型是**Microsoft.遷移/遷移專案**。
    - 在下一節中，查看為 Azure 遷移專案中的發現、評估和遷移而創建的資源。
    - 如果資源組僅包含 Azure 遷移專案，則可以刪除整個資源組。
    - 如果要從以前的 Azure 遷移版本中刪除專案，則步驟相同。 這些專案的資源類型是**遷移專案**。


## <a name="created-resources"></a>已創建資源

這些表匯總了為 Azure 遷移專案中的發現、評估和遷移而創建的資源。

> [!NOTE]
> 請謹慎刪除金鑰保存庫，因為它可能包含安全金鑰。

### <a name="vmwarephysical-server"></a>VMware/實體伺服器

**資源** | **類型**
--- | ---
"設備名稱"kv | 金鑰保存庫
"設備名稱"網站 | 微軟.關閉Azure/VMwareSites
"專案名稱" | 微軟.遷移/遷移專案
"專案名稱"專案 | 微軟.遷移/評估專案
"專案名稱"rsvault | 復原服務保存庫
"專案名稱"-遷移庫-* | 復原服務保存庫
遷移 appligwsa* | 儲存體帳戶
遷移 applilsa* | 儲存體帳戶
遷移應用* | 儲存體帳戶
遷移 applikv* | 金鑰保存庫
遷移applisbns16041 | 服務匯流排 命名空間

### <a name="hyper-v-vm"></a>Hyper-V VM 

**資源** | **類型**
--- | ---
"專案名稱" | 微軟.遷移/遷移專案
"專案名稱"專案 | 微軟.遷移/評估專案
HyperV_kv | 金鑰保存庫
HyperV_Site | 微軟.關閉Azure/超網站
"專案名稱"-遷移庫-* | 復原服務保存庫


## <a name="next-steps"></a>後續步驟

瞭解如何添加其他[評估和](how-to-assess.md)[遷移](how-to-migrate.md)工具。 
