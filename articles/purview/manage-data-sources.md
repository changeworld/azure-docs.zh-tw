---
title: " (預覽版) 管理 Azure 範疇中的資料來源"
description: 瞭解如何在 Azure 範疇 (Preview) 中註冊新的資料來源、管理資料來源的集合，以及查看來源。
author: mamccrea
ms.author: mamccrea
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: ef54c40715c874364beb188d8850a9ce953aa112
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552107"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a> (預覽版) 管理 Azure 範疇中的資料來源

在本文中，您將瞭解如何在 Azure 範疇 (Preview) 中註冊新的資料來源、管理資料來源的集合，以及查看來源。 Azure 範疇支援下列資料來源：

* SQL server 內部部署
* Azure Data Lake Storage Gen1 
* Azure Data Lake Storage Gen2
* Azure Blob 儲存體
* Azure 資料總管
* Azure SQL DB
* Azure SQL DB 受控執行個體
* Azure Synapse Analytics (先前為 SQL DW)
* Azure Cosmos DB
* Power BI

## <a name="register-a-new-source"></a>註冊新的來源

使用下列步驟來註冊新的來源。

1. 開啟範疇 Studio，然後選取 [ **註冊來源** ] 磚。

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure 範疇 Studio":::

1. 選取 [ **註冊**]，然後選取來源類型。 這個範例會使用 Azure Blob 儲存體。 選取 [繼續]。

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="在 [註冊來源] 頁面中選取資料來源類型":::

1. 填寫 [ **註冊來源** ] 頁面上的表單。 選取來源的名稱，然後輸入相關資訊。 如果您選擇 [ **從 Azure 訂用** 帳戶] 作為帳戶選取方法，則訂用帳戶中的來源會出現在下拉式清單中。 或者，您可以手動輸入來源資訊。

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="資料來源資訊的表單":::

1. 選取 [完成]。

## <a name="view-sources"></a>查看來源

您可以在 Azure 範疇 Studio 的 [ **來源** ] 索引標籤上，查看所有已註冊的來源。 有兩種檢視類型：地圖視圖和清單視圖。

### <a name="map-view"></a>地圖檢視

在 [地圖視圖] 中，您可以看到所有來源和集合。 在下圖中，有一個 Azure Blob 儲存體的來源。 您可以從每個來源磚編輯來源、開始新的掃描，或查看來源詳細資料。

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Azure 範疇資料來源地圖視圖":::

### <a name="list-view"></a>清單檢視

在清單視圖中，您可以看到可排序的來源清單。 將滑鼠停留在來源以取得編輯的選項、開始新的掃描，或刪除。

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Azure 範疇資料來源清單視圖":::

## <a name="manage-collections"></a>管理集合

您可以將資料來源分組到集合中。 若要建立新的集合，請在 Azure 範疇 Studio 的 [*來源*] 頁面上，選取 [ **+ 新增集合**]。 提供集合的名稱，並選取 [ *無* ] 做為父系。 新的集合會出現在地圖視圖中。

若要將來源加入至集合，請選取 [ **編輯** 來源上的鉛筆]，然後從 [ **選取集合** ] 下拉式功能表中選擇一個集合。

若要建立集合階層，請將較高層級的集合指派為較低層級集合的父系。 在下圖中， *Fabrikam* 是 *財務* 集合的父系，其中包含 Azure Blob 儲存體資料來源。 您可以按一下附加至 [層級] 之間箭號的圓形來折迭或展開集合。

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Azure 範疇 Studio 中的集合階層":::

您可以針對父系選取 [ *無* ] 來移除階層中的來源。 無上層來源會在地圖視圖中以虛線方塊分組，而不會有箭號將它們連結至父代。

## <a name="next-steps"></a>後續步驟

瞭解如何註冊和掃描各種資料來源：

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Power BI 租用戶](register-scan-power-bi-tenant.md)
* [Azure SQL Database](register-scan-azure-sql-database.md)
