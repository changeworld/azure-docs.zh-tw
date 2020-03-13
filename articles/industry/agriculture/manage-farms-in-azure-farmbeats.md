---
title: 管理伺服器陣列
description: 描述如何管理伺服器陣列
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271716"
---
# <a name="manage-farms"></a>管理農場

您可以在 Azure FarmBeats 中管理您的伺服器陣列。 本文提供如何建立伺服器陣列、安裝裝置、感應器和無人機的相關資訊，以協助您管理伺服器陣列。

## <a name="create-farms"></a>建立伺服器陣列

使用下列步驟：

1. 登入伺服器陣列加速器時，會顯示 [**伺服器**陣列] 頁面。
    [**伺服器**陣列] 頁面會顯示已在訂用帳戶中建立的伺服器陣列清單。

    以下是範例影像：

    ![專案 FarmBeats](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. 選取 [**建立伺服器**陣列]，並提供**名稱**、**裁剪**和**位址**。
3. 在 [**定義伺服器陣列界限**] （[必要欄位]）中，選取 [**對應**] 或 [貼上**GeoJSON 程式碼**]。

以下是定義伺服器陣列界限的兩種方式：

1. **地圖上的標記**：使用地圖控制項工具來繪製和標示伺服器陣列的界限。 為了標記界限，![Project 伺服器陣列的](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) 並標示出確切的界限。

    ![專案 FarmBeats](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **貼上 GeoJson 程式碼**： GeoJson 是使用 JAVASCRIPT 物件標記法（JSON）來編碼地理資料結構的格式。 此選項會顯示一個文字方塊，您可以在其中輸入 GeoJSON 字串來標示伺服器陣列界限。 您也可以從 GeoJSON.io 建立 GeoJSON 程式碼。
使用工具提示來協助填入資訊。

    ![專案 FarmBeats](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  選取 [**提交**] 以建立伺服器陣列。 新的伺服器陣列隨即建立，並顯示在 [**伺服器**陣列] 頁面中。

## <a name="view-farm"></a>View 伺服器陣列

[伺服器陣列清單] 頁面會顯示已建立之伺服器陣列的清單。 選取要查看其清單的伺服器陣列：

 - **裝置計數**：顯示在伺服器陣列中部署之裝置的數目和狀態。
 - **Map** —將伺服器陣列對應至部署在伺服器陣列中的裝置。
 - **遙測**—顯示伺服器陣列中部署之感應器的遙測。
 - **最新的有效位數對應**-顯示最新的衛星索引對應（EVI、NDWI）、泥土濕度熱度圖和感應器放置圖。

## <a name="edit-farm"></a>編輯服務器陣列

[**伺服器**陣列] 頁面會顯示已建立之伺服器陣列的清單。

1.  選取要查看和編輯服務器陣列的伺服器陣列。
2.  選取 [**編輯服務器**陣列] 以編輯服務器陣列資訊。 在 [**伺服器陣列詳細資料**] 視窗中，您可以編輯**名稱**、**裁剪**、**位址**和定義**伺服器陣列界限**欄位。

    ![專案 FarmBeats](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. 選取 [**提交**] 以儲存編輯的詳細資料。

## <a name="delete-farm"></a>刪除伺服器陣列

[**伺服器**陣列] 頁面會顯示已建立的伺服器陣列清單。 使用下列步驟來刪除伺服器陣列：

1.  從清單中選取伺服器陣列，以刪除伺服器陣列詳細資料。
2.  選取 [**刪除伺服器**陣列] 以刪除伺服器陣列。

    ![專案 FarmBeats](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > 當您刪除伺服器陣列時，與伺服器陣列相關聯的裝置和對應不會被刪除。 與裝置和對應相關聯的任何伺服器陣列資訊都不相關。 您可以繼續從 FarmBeats 服務查看裝置、遙測和對應。


## <a name="next-steps"></a>後續步驟

既然您已建立伺服器陣列，請瞭解如何將[感應器資料](get-sensor-data-from-sensor-partner.md)流入您的伺服器陣列。
