---
title: 教學課程 - 在 Azure API 管理中存取和自訂開發人員入口網站 | Microsoft Docs
description: 遵循此教學課程了解如何使用 API 的文件來自訂 API 管理開發人員入口網站，這是一個自動產生、可完全自訂的網站。
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 90544fbafe7393630c3f3fbc694ae367eccb7f90
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012967"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>教學課程：存取及自訂開發人員入口網站

「開發人員入口網站」是自動產生、可完全自訂的網站，其中包含您的 API 文件。 API 取用者可以在此探索您的 API、了解如何使用，以及要求存取權。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 存取開發人員入口網站的受控版本
> * 瀏覽系統管理介面
> * 自訂內容
> * 發佈變更
> * 檢視已發佈的入口網站

如需開發人員入口網站的詳細資訊，請參閱 [Azure API 管理開發人員入口網站概觀](api-management-howto-developer-portal.md)。

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="API 管理開發人員入口網站 - 管理員模式" border="false":::

## <a name="prerequisites"></a>Prerequisites

- 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)
- 匯入並發佈 Azure API 管理執行個體。 如需詳細資訊，請參閱[匯入和發佈](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>以管理員身分存取入口網站

請遵循下列步驟來存取入口網站的受控版本。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 API 管理執行個體。
1. 在上方導覽列中，選取 [開發人員入口網站] 按鈕。 具有入口網站管理版本的新瀏覽器索引標籤隨即開啟。

## <a name="understand-the-portals-administrative-interface"></a>了解入口網站的系統管理介面

### <a name="default-content"></a>預設內容 

如果您是第一次存取入口網站，則會在背景自動佈建預設內容。 預設內容設計為展示入口網站的功能，並將個人化您的入口網站所需的自訂項目降至最低。 您可以在 [Azure API 管理開發人員入口網站概觀](api-management-howto-developer-portal.md)中深入了解入口網站內容中包含的項目。

### <a name="visual-editor"></a>視覺化編輯器

您可以使用視覺化編輯器自訂入口網站的內容。 
* 左側的功能表區段可讓您建立或修改頁面、媒體、版面配置、功能表、樣式或網站設定。 
* 底部的功能表項目可讓您在不同的物件之間切換 (例如行動或桌面)、檢視已驗證或匿名使用者可以看見的入口網站元素，或儲存或復原動作。
* 按一下有加號的藍色圖示，將資料列新增至頁面。 
* 按下加號的灰色圖示可新增小工具 (例如，文字、映像或 API 清單)。
* 使用拖放功能重新排列頁面中的項目。 

### <a name="layouts-and-pages"></a>版面配置和頁面

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="頁面和版面配置" border="false":::

版面配置定義頁面的顯示方式。 例如在預設內容中，有兩個配置：一個會套用至首頁，另一個則用於所有其他頁面。

配置會將其 URL 範本與頁面的 URL 進行比對，以套用至頁面。 例如，具有 `/wiki/*` URL 範本的配置會套用至 URL 中具有 `/wiki/` 區段的每個頁面：`/wiki/getting-started`、`/wiki/styles` 等等。

在上圖中，屬於配置的內容會以藍色標示，而頁面則會以紅色標示。 功能表區段會分別標示。

### <a name="styling-guide"></a>樣式指南

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="樣式指南" border="false":::

樣式指南是以設計工具建立的面板， 可讓您監督和設定入口網站中所有視覺元素的樣式。 樣式設定是階層式的，許多元素會從其他元素繼承屬性。 例如，按鈕元素會使用文字和背景的色彩。 若要變更按鈕的色彩，您需要變更原始的色彩變化。

若要編輯變數，請選取要編輯的項目，然後選取出現在其上方的鉛筆圖示。 在快顯視窗中進行變更之後，請將其關閉。

### <a name="save-button"></a>[儲存] 按鈕

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="[儲存] 按鈕" border="false":::

每當您在入口網站中進行變更時，都必須選取底部功能表中的 [儲存] 按鈕，或按 Ctrl + S，以手動方式儲存。 儲存變更時，修改過的內容會自動上傳到您的 API 管理服務。

## <a name="customize-the-portals-content"></a>自訂入口網站內容

在您將入口網站提供給訪客使用之前，應該將自動產生的內容個人化。 建議的變更包括歡迎頁面的配置、樣式和內容。

> [!NOTE]
> 基於整合考量，下列頁面無法在不同的 URL 下移除或移動：`/404`、`/500`、`/captcha`、`/change-password`、`/config.json`、`/confirm/invitation`、`/confirm-v2/identities/basic/signup`、`/confirm-v2/password`、`/internal-status-0123456789abcdef`、`/publish`、`/signin`、`/signin-sso`、`/signup`。

### <a name="home-page"></a>首頁

預設的 **歡迎** 頁面會填入預留位置內容。 您可以移除包含此內容的整個區段，或保留結構並逐一調整元素。 以您自己的方式取代產生的文字和影像，並確定連結指向所需的位置。

### <a name="layouts"></a>版面配置

以您自己的影像取代導覽列中自動產生的標誌。

### <a name="styling"></a>樣式

雖然不需要調整任何樣式，但建議考慮調整特定元素。 例如，變更主要色彩以符合品牌的色彩。

### <a name="customization-example"></a>自訂範例

在下列影片中，我們會示範如何編輯入口網站的內容、自訂網站外觀，以及發佈變更。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> 發佈入口網站

若要讓您的入口網站和其最新的變更可供訪客使用，您需要「發佈」內容。 您可以在入口網站的系統管理介面或 Azure 入口網站中發佈入口網站。

### <a name="publish-from-the-administrative-interface"></a>從系統管理介面發佈

1. 請確定您已儲存變更，方法是選取 [儲存] 圖示。
1. 在功能表的 **作業** 區段中，選取 [發佈網站]。 這項作業可能需要幾分鐘的時間。  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="發佈入口網站" border="false":::

### <a name="publish-from-the-azure-portal"></a>從 Azure 入口網站發佈

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 API 管理執行個體。
1. 在左側功能表中 **開發人員入口網站** 下，選取 [入口網站概觀]。
1. 在 **入口網站概觀** 視窗中，選取 [發佈]。

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="從 Azure 入口網站發佈入口網站":::

> [!NOTE]
> 入口網站必須在 API 管理服務設定變更之後重新發佈。 例如，指派自訂網域、更新身分識別提供者、設定委派，或指定登入和產品條款之後，請重新發佈入口網站。


## <a name="visit-the-published-portal"></a>造訪已發佈的入口網站

發佈入口網站後，您可以在與系統管理面板相同的 URL 上存取，例如 `https://contoso-api.developer.azure-api.net`。 在另一個瀏覽器工作階段 (使用 incognito 或私人瀏覽模式) 中，以外部訪客的身分檢視。

## <a name="apply-the-cors-policy-on-apis"></a>在 API 上套用 CORS 原則

若要讓入口網站的訪客透過內建的互動式主控台測試 API，請在您的 API 上啟用 CORS (跨原始資源分享)。 如需詳細資料，請參閱 [Azure API 管理開發人員入口網站概觀](api-management-howto-developer-portal.md#cors)。

## <a name="next-steps"></a>後續步驟

深入了解開發人員入口網站：

- [Azure API 管理開發人員入口網站概觀](api-management-howto-developer-portal.md)
- 從已淘汰的舊版入口網站[遷移至新的開發人員入口網站](developer-portal-deprecated-migration.md)。