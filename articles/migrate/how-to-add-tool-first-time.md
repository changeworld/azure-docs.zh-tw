---
title: 在 Azure 遷移中添加評估/遷移工具
description: 介紹如何創建 Azure 遷移專案並添加評估/遷移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185943"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>第一次使用請新增評量/移轉工具

本文介紹如何首次向[Azure 遷移](migrate-overview.md)專案添加評估或遷移工具。  
Azure 遷移提供了一個中心中心，用於跟蹤本地應用和工作負荷以及私有/公共雲 VM 到 Azure 的發現、評估和遷移。 中心提供用於評估和遷移的 Azure 遷移工具，以及其他工具和獨立軟體廠商 （ISV）[產品](migrate-services-overview.md#isv-integration)。 

## <a name="create-a-project-and-add-a-tool"></a>創建專案並添加工具

在 Azure 訂閱中設置新的 Azure 遷移專案，並添加工具。

- Azure 遷移專案用於存儲從要評估或遷移的環境收集的發現、評估和遷移中繼資料。 
- 在專案中，您可以跟蹤發現的資產，並協調評估和遷移。

1. 在 Azure 入口網站 > [所有服務]**** 中，搜尋 **Azure Migrate**。
2. 在 [服務]**** 下，選取 [Azure Migrate]****。

    ![設定 Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. 在 [概觀]**** 中，按一下 [評估和遷移伺服器]****。
4. 在 [探索、評估和遷移伺服器]**** 下方，按一下 [評估和遷移伺服器]****。

    ![探索和評估伺服器](./media/how-to-add-tool-first-time/assess-migrate.png)

1. 在 [探索、評估和遷移伺服器]**** 中，按一下 [新增工具]****。
2. 在 [Migrate 專案]**** 中選取您的 Azure 訂用帳戶，並建立資源群組 (如果您還沒有的話)。
3. 在 **"專案詳細資訊**"中，指定要在其中創建專案的專案名稱和地理位置。 

    ![建立 Azure Migrate 專案](./media/how-to-add-tool-first-time/migrate-project.png)

    您可以在下列任何地理位置建立 Azure Migrate 專案。

   **地理** | **存儲位置區域**
    --- | ---
    Asia   | 東南亞或東亞
    歐洲 | 北歐或西歐
    日本  | 日本東部或日本西部
    United Kingdom | 英國南部或英國西部
    美國 | 美國中部或美國西部 2
    Canada | 加拿大中部
    印度  | 印度中部或印度南部
    澳大利亞 | 澳大利亞東南部

    針對專案所指定的地理位置，僅會用於儲存從內部部署虛擬機器收集的中繼資料。 您可以選取任何目標區域以進行實際移轉。

    如果要在地理位置中指定用於部署遷移專案及其關聯資源的特定區域（訂閱中的策略限制可能只允許將 Azure 資源部署到特定的 Azure 區域），則可以使用以下 API 進行創建遷移專案。 指定訂閱 ID、資源組名稱、遷移專案名稱以及位置（部署 Azure 遷移的表中提及的任何 Azure 區域）。

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. 按一下 **"下一步**"，然後添加評估或遷移工具。

    > [!NOTE]
    > 創建專案時，需要添加至少一個評估或遷移工具。

5. 在 **"選擇評估工具"** 中，添加評估工具。 如果您不需要評估工具，請選擇 **"立即跳過添加評估工具** > **下一步**"。 
2. 在**選擇遷移工具**中，根據需要添加遷移工具。 如果您現在不需要遷移工具，請選擇 **"立即跳過添加遷移工具** > **"。**
3. 在 **"審閱 + 添加工具**"中，查看設置並按一下"**添加工具**"。

創建專案後，您可以選擇用於評估和遷移伺服器和工作負荷、資料庫和 Web 應用的其他工具。

## <a name="create-additional-projects"></a>創建其他專案

在某些情況下，您可能需要創建其他 Azure 遷移專案。 例如，如果資料中心位於不同的地理位置，或者需要將中繼資料存儲在不同的地理位置。 創建其他專案，如下所示：

1. 在當前 Azure 遷移專案中，按一下 **"伺服器**"或"**資料庫**"。
2. 在右上角，按一下當前專案名稱旁邊的 **"更改**"。
3. 在 **"設置"** 中，選擇 **"按一下此處"創建新專案**。
4. 創建新專案並添加新工具，如上一過程所述。

## <a name="next-steps"></a>後續步驟

瞭解如何添加其他[評估和](how-to-assess.md)[遷移](how-to-migrate.md)工具。 
