---
title: 建立和管理 Azure Migrate 專案
description: 在 Azure Migrate 中尋找、建立、管理和刪除專案。
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: f5079ed979d98f2c6f0c654c860c6f176f366497
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676400"
---
# <a name="create-and-manage-azure-migrate-projects"></a>建立和管理 Azure Migrate 專案

本文說明如何建立、管理和刪除[Azure Migrate](migrate-services-overview.md)專案。


## <a name="create-a-project-for-the-first-time"></a>第一次建立專案

第一次設定 Azure Migrate 時，您會建立專案並新增評量或遷移工具。 [請遵循這些指示](how-to-add-tool-first-time.md)來第一次設定。

## <a name="create-additional-projects"></a>建立其他專案

如果您已經有 Azure Migrate 專案，而且想要建立其他專案，請執行下列動作：  

1. 在[Azure 公用入口網站](https://portal.azure.com)或[Azure Government](https://portal.azure.us)中，搜尋**Azure Migrate**。
2. 在 [Azure Migrate 儀表板 >**伺服器**上，選取右上角的 [**變更**]。

   ![變更 Azure Migrate 專案](./media/create-manage-projects/switch-project.png)

3. 若要建立新的專案，請選取 [**按一下這裡**]。

   ![建立第二個 Azure Migrate 專案](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>尋找專案

尋找專案，如下所示：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋**Azure Migrate**。
2. 在 [Azure Migrate 儀表板 >**伺服器**] 中，選取右上角的 [**變更**]。

    ![切換至現有的 Azure Migrate 專案](./media/create-manage-projects/switch-project.png)

3. 選取適當的訂用帳戶，並 Azure Migrate 專案。


如果您已在[舊版](migrate-services-overview.md#azure-migrate-versions)Azure Migrate 中建立專案，請如下所示尋找：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋**Azure Migrate**。
2. 在 [Azure Migrate] 儀表板中，如果您已在舊版中建立專案，則會顯示參考較舊專案的橫幅。 選取橫幅。

    ![存取現有的專案](./media/create-manage-projects/access-existing-projects.png)

3. 檢查舊專案的清單。


## <a name="delete-a-project"></a>刪除專案

刪除，如下所示：

1. 開啟專案建立所在的 Azure 資源群組。
2. 在 [資源群組] 頁面中，選取 [**顯示隱藏的類型**]。
3. 選取您想要刪除的「遷移」專案，以及其相關聯的資源。
    - 資源類型為 [ **Microsoft. 遷移/migrateprojects**]。
    - 如果資源群組是由 Azure Migrate 專案獨佔使用，您可以刪除整個資源群組。


請注意：

- 當您刪除時，會刪除專案和有關探索到之電腦的中繼資料。
- 如果您使用較舊版本的 Azure Migrate，請開啟專案建立所在的 Azure 資源群組。 選取您想要刪除的 [遷移] 專案（[資源類型] 是 [**遷移專案**]）。
- 如果您是使用 Azure Log Analytics 工作區的相依性分析：
    - 如果您已將 Log Analytics 工作區附加至伺服器評估工具，則不會自動刪除工作區。 相同的 Log Analytics 工作區可以用於多個案例。
    - 如果您想要刪除 Log Analytics 工作區，請手動執行此動作。

### <a name="delete-a-workspace-manually"></a>手動刪除工作區

1. 瀏覽到附加到專案的 Log Analytics 工作區。

    - 如果您尚未刪除 Azure Migrate 專案，您可以在**Essentials** > **Server 評估**中找到工作區的連結。
       ![LA 工作](./media/create-manage-projects/loganalytics-workspace.png)區。
       
    - 如果您已刪除 Azure Migrate 專案，請在 Azure 入口網站的左窗格中選取 [**資源群組**]，然後尋找工作區。
       
2. [遵循指示](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)來刪除工作區。

## <a name="next-steps"></a>後續步驟

新增[評定](how-to-assess.md)或[遷移](how-to-migrate.md)工具以 Azure Migrate 專案。
