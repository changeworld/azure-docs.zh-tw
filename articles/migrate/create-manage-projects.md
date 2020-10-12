---
title: 建立和管理 Azure Migrate 專案
description: 在 Azure Migrate 中尋找、建立、管理和刪除專案。
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: d60868f9d0d4c60291cfd92a9e8d11fd3f9a42b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87071794"
---
# <a name="create-and-manage-azure-migrate-projects"></a>建立和管理 Azure Migrate 專案

本文說明如何建立、管理和刪除 [Azure Migrate](migrate-services-overview.md) 專案。


## <a name="create-a-project-for-the-first-time"></a>第一次建立專案

當您第一次設定 Azure Migrate 時，您會建立專案並新增評量或遷移工具。 [請遵循下列指示](how-to-add-tool-first-time.md) ，以進行第一次設定。

## <a name="create-additional-projects"></a>建立其他專案

如果您已經有 Azure Migrate 專案，而且想要建立其他專案，請執行下列動作：  

1. 在 [Azure 公用入口網站](https://portal.azure.com) 或 [Azure Government](https://portal.azure.us)中，搜尋 **Azure Migrate**。
2. 在 [Azure Migrate 儀表板 > **伺服器**] 上，選取右上角的 [ **變更** ]。

   ![變更 Azure Migrate 專案](./media/create-manage-projects/switch-project.png)

3. 若要建立新的專案，請選取 [ **按一下這裡**]。

   ![建立第二個 Azure Migrate 專案](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>尋找專案

尋找專案，如下所示：

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋 **Azure Migrate**。
2. 在 Azure Migrate 儀表板 > **伺服器**] 中，選取右上角的 [ **變更** ]。

    ![切換至現有的 Azure Migrate 專案](./media/create-manage-projects/switch-project.png)

3. 選取適當的訂用帳戶，並 Azure Migrate 專案。


如果您在 [舊版](migrate-services-overview.md#azure-migrate-versions) Azure Migrate 中建立專案，請依下列方式找出：

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋 **Azure Migrate**。
2. 在 [Azure Migrate 儀表板] 中，如果您已在先前的版本中建立專案，則會顯示參考較舊專案的橫幅。 選取橫幅。

    ![存取現有的專案](./media/create-manage-projects/access-existing-projects.png)

3. 檢查舊專案的清單。


## <a name="delete-a-project"></a>刪除專案

如下所示加以刪除：

1. 開啟專案建立所在的 Azure 資源群組。
2. 在 [資源群組] 頁面中，選取 [ **顯示隱藏的類型**]。
3. 選取您要刪除的遷移專案及其相關聯的資源。
    - 資源類型為 **Microsoft. 遷移/migrateprojects**。
    - 如果資源群組僅供 Azure Migrate 專案使用，您可以刪除整個資源群組。

請注意：

- 當您刪除時，會刪除與探索到的機器相關的專案和中繼資料。
- 如果您使用較舊版本的 Azure Migrate，請開啟建立專案的 Azure 資源群組。 選取您要刪除的遷移專案 (資源類型是 [ **遷移專案** ]) 。
- 如果您要使用 Azure Log Analytics 工作區的相依性分析：
    - 如果您已將 Log Analytics 工作區附加至伺服器評量工具，則不會自動刪除工作區。 相同的 Log Analytics 工作區可用於多個案例。
    - 如果您想要刪除 Log Analytics 工作區，請以手動方式進行。
- 專案刪除無法復原。 無法復原已刪除的物件。

### <a name="delete-a-workspace-manually"></a>手動刪除工作區

1. 瀏覽到附加到專案的 Log Analytics 工作區。

    - 如果您尚未刪除 Azure Migrate 專案，您可以在**Essentials**  >  **伺服器評**量中找到工作區的連結。
       ![LA 工作區 ](./media/create-manage-projects/loganalytics-workspace.png) 。
       
    - 如果您已刪除 Azure Migrate 專案，請在 Azure 入口網站的左窗格中選取 [ **資源群組** ]，然後尋找工作區。
       
2. [請依照指示](../azure-monitor/platform/delete-workspace.md) 來刪除工作區。

## <a name="next-steps"></a>接下來的步驟

新增 [評定](how-to-assess.md) 或 [遷移](how-to-migrate.md) 工具以 Azure Migrate 專案。
