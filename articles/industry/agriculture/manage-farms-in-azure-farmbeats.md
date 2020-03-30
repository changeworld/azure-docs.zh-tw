---
title: 管理伺服器場
description: 描述如何管理伺服器場
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271716"
---
# <a name="manage-farms"></a>管理農場

您可以在 Azure FarmBeats 中管理伺服器場。 本文提供有關如何創建伺服器場、安裝設備、感應器和無人機的資訊，以説明您管理伺服器場。

## <a name="create-farms"></a>創建伺服器場

使用下列步驟：

1. 登錄到伺服器場加速器，將顯示 **"伺服器場"** 頁面。
    "**伺服器場"** 頁顯示伺服器場的清單，以防它們已在訂閱中創建。

    下面是示例圖像：

    ![專案 FarmBeats](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. 選擇 **"創建伺服器場**"並提供**名稱**、**裁剪**和**位址**。
3. 在**定義伺服器場邊界**中，（必要欄位）選擇 **"標記"** 或 **"粘貼 GeoJSON 代碼**"。

以下是定義伺服器場邊界的兩種方法：

1. **在地圖上標記**：使用地圖控制工具繪製和標記伺服器場的邊界。 要標記邊界，![專案伺服器場節拍](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png)並標記確切的邊界。

    ![專案 FarmBeats](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **粘貼 GeoJson 代碼**： GeoJSON 是一種使用 JavaScript 物件標記法 （JSON） 編碼地理資料結構的格式。 此選項顯示一個文字方塊，其中可以輸入 GeoJSON 字串來標記伺服器場邊界。 您還可以從GeoJSON.io創建 GeoJSON 代碼。
使用工具提示説明填寫資訊。

    ![專案 FarmBeats](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  選擇 **"提交**"以創建伺服器場。 將創建新伺服器場，並在 **"伺服器場"** 頁中顯示。

## <a name="view-farm"></a>查看伺服器場

"伺服器場"清單頁顯示已創建伺服器場的清單。 選擇伺服器場以查看以下清單：

 - **設備計數**= 顯示在伺服器場中部署的設備的數量和狀態。
 - **映射**– 使用伺服器場中部署的設備繪製伺服器場的地圖。
 - **遙測**= 顯示伺服器場中部署的感應器的遙測資料。
 - **最新精度圖**– 顯示最新的衛星指數圖（EVI、NDWI）、土壤水分熱圖和感應器放置圖。

## <a name="edit-farm"></a>編輯服務器場

"**伺服器場"** 頁顯示已創建伺服器場的清單。

1.  選擇要查看和編輯服務器場的伺服器場。
2.  選擇 **"編輯服務器場**"以編輯服務器場資訊。 在 **"伺服器場詳細資訊"** 視窗中，可以編輯**名稱**、**裁剪、****位址**和定義**伺服器場邊界**欄位。

    ![專案 FarmBeats](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. 選擇 **"提交"** 以保存已編輯的詳細資訊。

## <a name="delete-farm"></a>刪除伺服器場

"**伺服器場"** 頁顯示創建的伺服器場的清單。 使用以下步驟刪除伺服器場：

1.  從清單中選擇伺服器場以刪除伺服器場詳細資訊。
2.  選擇 **"刪除伺服器場**"以刪除伺服器場。

    ![專案 FarmBeats](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > 刪除伺服器場時，不會刪除與伺服器場關聯的設備和映射。 與設備和地圖關聯的任何伺服器場資訊都將不相關。 您可以繼續從 FarmBeats 服務查看設備、遙測和地圖。


## <a name="next-steps"></a>後續步驟

現在，您已經創建了伺服器場，請瞭解如何[使感應器資料](get-sensor-data-from-sensor-partner.md)流入伺服器場。
