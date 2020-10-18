---
title: 管理伺服器陣列
description: 說明如何管理伺服器陣列
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: dc06f7c75e27d831994b97b97f3d20bb34c3a6c5
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167928"
---
# <a name="manage-farms"></a>管理農場

您可以在 Azure FarmBeats 中管理您的伺服器陣列。 本文提供如何建立伺服器陣列、安裝裝置、感應器和無人機的相關資訊，以協助您管理您的伺服器陣列。

## <a name="create-farms"></a>建立伺服器陣列

使用下列步驟：

1. 登入伺服器陣列加速器，[ **伺服器** 陣列] 頁面隨即顯示。
    如果伺服器陣列已在訂用帳戶中建立，則 [ **伺服器** 陣列] 頁面會顯示伺服器陣列的清單。

    以下是範例映射：

    ![顯示 [伺服器陣列] 頁面的螢幕擷取畫面。](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. 選取 [ **建立伺服器** 陣列]，然後提供 **名稱**、 **裁切** 和 **位址**。
3. 在 [ **定義伺服器陣列界限**] 中， ([必要欄位]) 選取 [ **在地圖上標記** 或 **貼上 GeoJSON 程式碼**]。

以下是兩種定義伺服器陣列界限的方式：

1. **地圖上的標示**：使用地圖控制項工具來繪製和標記伺服器陣列的界限。 若要標示界限，請在  ![ 地圖上顯示繪製邊界的鉛筆圖示， ](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) 並標示確切的界限。

    ![顯示地圖上繪製界限的螢幕擷取畫面。](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **貼上 GeoJson 程式碼**： GeoJson 是使用 JAVASCRIPT 物件標記法 (JSON) 來編碼地理資料結構的格式。 此選項會顯示一個文字方塊，其中可輸入 GeoJSON 字串以標示伺服器陣列界限。 您也可以從 GeoJSON.io 建立 GeoJSON 程式碼。
使用工具提示來協助填寫資訊。

    ![在 [建立伺服器陣列] 畫面上醒目顯示 [貼上 GeoJson 程式碼] 選項的螢幕擷取畫面。](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  選取 [ **提交** ] 以建立伺服器陣列。 新的伺服器陣列隨即建立並顯示在 [ **伺服器** 陣列] 頁面中。

## <a name="view-farm"></a>View farm

[伺服器陣列清單] 頁面會顯示已建立的伺服器陣列清單。 選取伺服器陣列以查看下列清單：

 - **裝置計數** ：顯示在伺服器陣列中部署之裝置的數目和狀態。
 - **Map** ：伺服器陣列與部署在伺服陣列中的裝置的對應。
 - **遙測** -顯示伺服器陣列中部署之感應器的遙測。
 - **最新的有效位數對應** ：顯示最新的附屬索引地圖 (EVI、NDWI) 、中料濕度熱度圖和感應器放置圖。

## <a name="edit-farm"></a>編輯服務器陣列

[ **伺服器** 陣列] 頁面會顯示已建立的伺服器陣列清單。

1.  選取伺服器陣列以查看和編輯服務器陣列。
2.  選取 [ **編輯服務器** 陣列] 以編輯陣列資訊。 在 [ **伺服器陣列詳細資料** ] 視窗中，您可以編輯 **名稱**、 **裁剪**、 **定址**和定義陣列 **界限** 欄位。

    ![專案 FarmBeats](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. 選取 [ **提交** ] 以儲存已編輯的詳細資料。

## <a name="delete-farm"></a>刪除伺服器陣列

[ **伺服器** 陣列] 頁面會顯示已建立的伺服器陣列清單。 使用下列步驟來刪除伺服器陣列：

1.  從清單中選取伺服器陣列以刪除伺服器陣列詳細資料。
2.  選取 [ **刪除伺服器** 陣列] 以刪除伺服器陣列。

    ![顯示 [刪除伺服器陣列] 畫面並反白顯示 [刪除] 按鈕的螢幕擷取畫面。](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > 當您刪除伺服器陣列時，不會刪除與伺服器陣列相關聯的裝置和對應。 與裝置和對應相關聯的任何伺服器陣列資訊都不相關。 您可以繼續從 FarmBeats 服務查看裝置、遙測和地圖。


## <a name="next-steps"></a>後續步驟

既然您已建立伺服器陣列，請瞭解如何 [讓感應器資料](get-sensor-data-from-sensor-partner.md) 流入您的伺服器陣列。
