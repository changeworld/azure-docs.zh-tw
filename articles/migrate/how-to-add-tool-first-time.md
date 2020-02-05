---
title: 在 Azure Migrate 中新增評量/遷移工具
description: 說明如何建立 Azure Migrate 專案，以及新增評量/遷移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185943"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>第一次使用請新增評量/移轉工具

本文說明如何在第一次將評估或遷移工具加入至[Azure Migrate](migrate-overview.md)專案。  
Azure Migrate 提供中央中樞，以追蹤內部部署應用程式和工作負載的探索、評估和遷移，以及私人/公用雲端 Vm 至 Azure。 中樞提供評估和遷移的 Azure Migrate 工具，以及其他工具和獨立軟體廠商（ISV）[產品](migrate-services-overview.md#isv-integration)。 

## <a name="create-a-project-and-add-a-tool"></a>建立專案並新增工具

在 Azure 訂用帳戶中設定新的 Azure Migrate 專案，並新增工具。

- Azure Migrate 專案用來儲存從您要評估或遷移的環境中收集到的探索、評估和遷移中繼資料。 
- 在專案中，您可以追蹤探索到的資產，並協調評估和遷移。

1. 在 Azure 入口網站 > [所有服務] 中，搜尋 **Azure Migrate**。
2. 在 [服務] 下，選取 [Azure Migrate]。

    ![設定 Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. 在 [概觀] 中，按一下 [評估和遷移伺服器]。
4. 在 [探索、評估和遷移伺服器] 下方，按一下 [評估和遷移伺服器]。

    ![探索和評估伺服器](./media/how-to-add-tool-first-time/assess-migrate.png)

1. 在 [探索、評估和遷移伺服器] 中，按一下 [新增工具]。
2. 在 [Migrate 專案] 中選取您的 Azure 訂用帳戶，並建立資源群組 (如果您還沒有的話)。
3. 在 [**專案詳細資料**] 中，指定您要在其中建立專案的 [專案名稱] 和 [地理位置]。 

    ![建立 Azure Migrate 專案](./media/how-to-add-tool-first-time/migrate-project.png)

    您可以在下列任何地理位置建立 Azure Migrate 專案。

   **地理位置** | **儲存位置區域**
    --- | ---
    亞洲   | 東南亞或東亞
    歐洲 | 歐洲北部或歐洲西部
    日本  | 日本東部或日本西部
    英國 | 英國南部或英國西部
    美國 | 美國中部或美國西部2
    加拿大 | 加拿大中部
    印度  | 印度中部或印度南部
    澳大利亞 | 澳大利亞東南部

    針對專案所指定的地理位置，僅會用於儲存從內部部署虛擬機器收集的中繼資料。 您可以選取任何目標區域以進行實際移轉。

    如果您想要指定地理位置內的特定區域來部署遷移專案及其相關聯的資源（訂用帳戶中的原則限制可能只允許將 Azure 資源部署到特定的 Azure 區域），您可以使用下列 API 來建立遷移專案。 指定訂用帳戶識別碼、資源組名、遷移專案名稱以及位置（在其中部署 Azure Migrate 的資料表中所提及的任何 Azure 區域）。

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. 按 **[下一步]** ，然後新增評量或遷移工具。

    > [!NOTE]
    > 當您建立專案時，您必須新增至少一個評估或遷移工具。

5. 在 [**選取評估工具**] 中，新增評定工具。 如果您不需要評估工具，請選取 **立即略過新增評估工具** ** > 下一步**。 
2. 在 [**選取遷移工具**] 中，視需要新增遷移工具。 如果您目前不需要遷移工具，請選取 **立即略過新增遷移工具** > **下一步**。
3. 在 [審核] 和 [**新增工具**] 中，檢查設定，然後按一下 [**新增工具**]。

建立專案之後，您可以選取其他工具來評估和遷移伺服器和工作負載、資料庫和 web 應用程式。

## <a name="create-additional-projects"></a>建立其他專案

在某些情況下，您可能需要建立額外的 Azure Migrate 專案。 例如，如果您的資料中心位於不同的地理位置，或您需要將中繼資料儲存在不同的地理區域中。 建立其他專案，如下所示：

1. 在目前的 Azure Migrate 專案中，按一下 [**伺服器**] 或 [**資料庫**]。
2. 在右上角，按一下目前專案名稱旁的 [**變更**]。
3. 在 [**設定**] 中，選取 [**按一下這裡以建立新的專案**]。
4. 建立新的專案，並新增新的工具，如先前的程式所述。

## <a name="next-steps"></a>後續步驟

瞭解如何新增其他[評估](how-to-assess.md)和[遷移](how-to-migrate.md)工具。 
