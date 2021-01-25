---
title: 建立和管理 Azure Migrate 專案
description: 在 Azure Migrate 中尋找、建立、管理和刪除專案。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 750cf0a938fffd4e5bfe25811bb276f7cb1b5583
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757554"
---
# <a name="create-and-manage-azure-migrate-projects"></a>建立和管理 Azure Migrate 專案

本文說明如何建立、管理和刪除 [Azure Migrate](migrate-services-overview.md) 專案。 如果您使用的是傳統 Azure Migrate 專案，請刪除這些專案，然後遵循步驟來建立新的 Azure Migrate 專案。 您無法將傳統 Azure Migrate 專案或元件升級為 Azure Migrate。 開始建立程式之前，請先參閱 [常見問題](https://docs.microsoft.com/azure/migrate/resources-faq#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) 。

Azure Migrate 專案可用來儲存從您評估或遷移的環境中收集到的探索、評量和移轉中繼資料。 在專案中，您可以追蹤探索到的資產、建立評量，以及協調遷移至 Azure。  

## <a name="verify-permissions"></a>驗證權限

檢查您是否有正確的許可權可建立 Azure Migrate 專案：

1. 在 Azure 入口網站中，開啟相關的訂用帳戶，然後選取 [ **存取控制] (IAM)**。
2. 在 [ **檢查存取**] 中，尋找相關的帳戶，然後選取 [view] 許可權。 您應該會具有「參與者」或「擁有者」權限。 


## <a name="create-a-project-for-the-first-time"></a>第一次建立專案

在 Azure 訂用帳戶中設定新的 Azure Migrate 專案。

1. 在 Azure 入口網站中，搜尋 *Azure Migrate*。
2. 在 [ **服務**] 中，選取 [ **Azure Migrate**]。
3. 在 [概觀] 中，選取 [評估和遷移伺服器]。

    ![評估和遷移伺服器的總覽選項](./media/create-manage-projects/assess-migrate-servers.png)

4. 在 [ **伺服器**] 中，選取 [ **建立專案**]。

    ![開始建立專案的按鈕](./media/create-manage-projects/create-project.png)

5. 在 [ **建立專案**] 中，選取 Azure 訂用帳戶和資源群組。 如果您還沒有資源群組，請加以建立。
6. 在 [專案詳細資料] 中，指定專案名稱以及要在其中建立專案的地理位置。
    - 地理位置只會用來儲存從內部部署電腦收集的中繼資料。 您可以選取任何目的地區域以進行遷移。 
    - 請檢閱[公用](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府雲端](migrate-support-matrix.md#supported-geographies-azure-government)支援的地理位置。

8. 選取 [建立]  。

   ![輸入專案設定的頁面](./media/create-manage-projects/project-details.png)


等候幾分鐘讓 Azure Migrate 專案完成部署。

## <a name="create-a-project-in-a-specific-region"></a>在特定區域中建立專案

在入口網站中，您可以選取要在其中建立專案的地理位置。 如果您想要在特定 Azure 區域中建立專案，請使用下列 API 命令來建立專案。

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>建立其他專案

如果您已經有 Azure Migrate 專案，而且想要建立其他專案，請執行下列動作：  

1. 在 [Azure 公用入口網站](https://portal.azure.com) 或 [Azure Government](https://portal.azure.us)中，搜尋 **Azure Migrate**。
2. 在 [Azure Migrate 儀表板 > **伺服器**] 上，選取右上角的 [ **變更** ]。

   ![變更 Azure Migrate 專案](./media/create-manage-projects/switch-project.png)

3. 若要建立新的專案，請選取 [ **按一下這裡**]。


## <a name="find-a-project"></a>尋找專案

尋找專案，如下所示：

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋 *Azure Migrate*。
2. 在 Azure Migrate 儀表板 > **伺服器**] 中，選取右上角的 [ **變更** ]。

    ![切換至現有的 Azure Migrate 專案](./media/create-manage-projects/switch-project.png)

3. 選取適當的訂用帳戶，並 Azure Migrate 專案。


### <a name="find-a-legacy-project"></a>尋找舊版專案

如果您在 [舊版](migrate-services-overview.md#azure-migrate-versions) Azure Migrate 中建立專案，請依下列方式找出：

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋 *Azure Migrate*。
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

    - 如果您尚未刪除 Azure Migrate 專案，您可以在 **Essentials**  >  **伺服器評** 量中找到工作區的連結。
       ![LA 工作區 ](./media/create-manage-projects/loganalytics-workspace.png) 。
       
    - 如果您已刪除 Azure Migrate 專案，請在 Azure 入口網站的左窗格中選取 [ **資源群組** ]，然後尋找工作區。
       
2. [請依照指示](../azure-monitor/platform/delete-workspace.md) 來刪除工作區。

## <a name="next-steps"></a>後續步驟

新增 [評定](how-to-assess.md) 或 [遷移](how-to-migrate.md) 工具以 Azure Migrate 專案。
