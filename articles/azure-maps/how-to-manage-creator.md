---
title: '管理 Microsoft Azure Maps Creator (Preview) '
description: 在本文中，您將瞭解如何管理 Microsoft Azure Maps Creator (Preview) 。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 1ed3a9033f9be39774e1c52982f63259cc477d29
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906092"
---
# <a name="manage-azure-maps-creator-preview"></a>管理 Azure 地圖服務建立者 (預覽)  

> [!IMPORTANT]
> Azure 地圖服務 Creator 服務目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 地圖服務建立工具可讓您建立私人室內地圖資料。 您可以使用 Azure 地圖服務 API 和室內地圖服務模組，開發互動式和動態的室內地圖 web 應用程式。 目前，只有使用 S1 定價層的美國區域可以使用建立工具。

本文會引導您完成在 Azure 地圖服務帳戶中建立和刪除建立工具資源的步驟。

## <a name="create-creator-preview-resource"></a>建立 Creator (Preview) 資源

1. 登入 [Azure 入口網站](https://portal.azure.com)

2. 選取您的 Azure 地圖服務帳戶。 如果您在 **最近使用的資源** 下看不到您的 Azure 地圖服務帳戶，請瀏覽至 Azure 入口網站功能表。 選取 [所有資源]。 尋找並選取您的 Azure 地圖服務帳戶。

    ![Azure 地圖服務入口網站首頁](./media/how-to-manage-creator/select-maps-account.png)

3. 在 Azure 地圖服務帳戶頁面上，瀏覽至 [建立工具] 下的 [概觀]選項。 按一下 [建立]，以建立 Azure 地圖服務建立工具資源。

    ![「建立 Azure 地圖服務建立工具」頁面](./media/how-to-manage-creator/creator-blade-settings.png)

4. 輸入您的建立工具資源的名稱和位置。 目前，只有在美國才支援使用「建立工具」。 按一下 [檢閱 + 建立]。

   ![「輸入建立工具帳戶資訊」頁面](./media/how-to-manage-creator/creator-creation-dialog.png)

5. 檢閱設定，然後按一下 [建立]。

    ![「確認建立工具帳戶設定「頁面](./media/how-to-manage-creator/creator-create-dialog.png)

6. 部署完成時，您會看到包含成功或失敗訊息的頁面。

   ![「資源部署狀態」頁面](./media/how-to-manage-creator/creator-resource-created.png)

7. 按一下 [前往資源]。 您的建立工具資源檢視頁面會顯示建立工具資源和所選的人口統計區域狀態。

    ![「建立工具狀態」頁面](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >從「建立工具資源」頁面中，按一下 [Azure 地圖服務帳戶]，即可瀏覽回到所屬的 Azure 地圖服務帳戶。

## <a name="delete-creator-preview-resource"></a>刪除 Creator (預覽版) 資源

若要刪除建立工具資源，請瀏覽至您的 Azure 地圖服務帳戶。 在 [建立工具] 下，選取 [概觀]。 按一下 [刪除]  按鈕。

>[!WARNING]
>刪除 Azure 地圖服務帳戶的建立工具資源時，也會刪除使用建立工具服務建立的資料集、地圖底圖集和功能狀態集。

![具有刪除按鈕的建立工具頁面](./media/how-to-manage-creator/creator-delete.png)

按一下 [刪除] 按鈕，然後輸入您的建立工具名稱以確認刪除。 刪除資源後，您會看到確認頁面，如下圖所示：

![顯示刪除確認的建立工具頁面](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>驗證

Creator (Preview) 繼承 Azure 地圖服務的存取控制 (IAM) 設定。 所有用於資料存取的 API 呼叫都必須透過驗證和授權規則傳送。

建立工具使用量資料會併入您的 Azure 地圖服務使用量圖表和活動記錄中。  如需詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

## <a name="access-to-creator-services"></a>存取建立工具服務

您只能從建立期間選取的位置記憶體取建立者服務 (預覽) 。 如果從選取的位置外部呼叫建立工具服務，則會傳回使用者錯誤訊息。 若要從選取的位置外部呼叫，服務 URL 必須包含所選位置的地理前置碼。 例如，如果在美國建立了建立工具，所有對轉換服務的呼叫都必須提交給 `us.atlas.microsoft.com/conversion/convert`。

此外，匯入建立工具的所有資料都應該上傳到與建立工具資源相同的地理位置。 例如，如果在美國佈建了建立工具，則所有未經處理資料都應該透過 `us.atlas.microsoft.com/mapData/upload`上傳。

## <a name="next-steps"></a>後續步驟

適用于室內對應的 Creator 服務 (預覽) 簡介：

> [!div class="nextstepaction"]
> [資料上傳](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [資料轉換](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [資料集](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [地圖底圖集](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [功能狀態集](creator-indoor-maps.md#feature-statesets)

瞭解如何使用建立者服務 (預覽) 在您的應用程式中呈現室內地圖：

> [!div class="nextstepaction"]
> [Azure 地圖服務建立工具教學課程](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [室內地圖動態樣式](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [使用室內地圖模組](how-to-use-indoor-module.md)