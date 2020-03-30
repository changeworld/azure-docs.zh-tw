---
title: 建立和管理 Azure Migrate 專案
description: 在 Azure 遷移中查找、創建、管理和刪除專案。
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: a49595f0580e71048239d5c5d8f4d1a66e24fe6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269636"
---
# <a name="create-and-manage-azure-migrate-projects"></a>建立和管理 Azure Migrate 專案

本文介紹如何創建、管理和刪除[Azure 遷移](migrate-services-overview.md)專案。


## <a name="create-a-project-for-the-first-time"></a>首次創建專案

首次設置 Azure 遷移時，將創建專案並添加評估或遷移工具。 [請按照這些說明](how-to-add-tool-first-time.md)進行首次設置。

## <a name="create-additional-projects"></a>創建其他專案

如果已有 Azure 遷移專案，並且想要創建其他專案，請執行以下操作：  

1. 在[Azure 門戶](https://portal.azure.com)中，搜索**Azure 遷移**。
2. 在 Azure 遷移儀表板>**伺服器**上，選擇右上角的**更改**。

   ![更改 Azure 遷移專案](./media/create-manage-projects/switch-project.png)

3. 要創建新專案，請選擇**此處按一下**。

   ![創建第二個 Azure 遷移專案](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>查找專案

查找專案，如下所示：

1. 在[Azure 門戶](https://portal.azure.com)中，搜索**Azure 遷移**。
2. 在 Azure 遷移儀表板>**伺服器**中，選擇右上角的**更改**。

    ![切換到現有的 Azure 遷移專案](./media/create-manage-projects/switch-project.png)

3. 選擇適當的訂閱和 Azure 遷移專案。


如果在以前的 Azure 遷移[版本中](migrate-services-overview.md#azure-migrate-versions)創建了專案，則查找它，如下所示：

1. 在[Azure 門戶](https://portal.azure.com)中，搜索**Azure 遷移**。
2. 在 Azure 遷移儀表板中，如果在以前的版本中創建了專案，則會出現引用舊專案的橫幅。 選擇橫幅。

    ![訪問現有專案](./media/create-manage-projects/access-existing-projects.png)

3. 查看舊專案的清單。


## <a name="delete-a-project"></a>刪除專案

刪除如下：

1. 打開在其中創建專案的 Azure 資源組。
2. 在資源組頁中，選擇 **"顯示隱藏類型**"。
3. 選擇要刪除的遷移專案及其關聯的資源。
    - 資源類型是**Microsoft.遷移/遷移專案**。
    - 如果資源組由 Azure 遷移專案獨佔使用，則可以刪除整個資源組。


請注意：

- 刪除時，將刪除專案和有關已發現電腦的中繼資料。
- 如果使用舊版本的 Azure 遷移，則打開創建專案的 Azure 資源組。 選擇要刪除的遷移專案（資源類型是**遷移專案**）。
- 如果將依賴項分析與 Azure 日誌分析工作區一起使用：
    - 如果將日誌分析工作區附加到伺服器評估工具，則不會自動刪除工作區。 同一日誌分析工作區可用於多個方案。
    - 如果要刪除日誌分析工作區，請手動執行此操作。

### <a name="delete-a-workspace-manually"></a>手動刪除工作區

1. 瀏覽到附加到專案的 Log Analytics 工作區。

    - 如果尚未刪除 Azure 遷移專案，則可以在 **"基本伺服器** > **評估**"中找到指向工作區的連結。
       ![LA](./media/create-manage-projects/loganalytics-workspace.png)工作區 .
       
    - 如果已刪除 Azure 遷移專案，請在 Azure 門戶的左側窗格中選擇 **"資源組"** 並查找工作區。
       
2. [按照說明](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)刪除工作區。

## <a name="next-steps"></a>後續步驟

向 Azure 遷移專案添加[評估](how-to-assess.md)或[遷移](how-to-migrate.md)工具。
