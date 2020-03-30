---
title: 訪問和自訂託管開發人員門戶 - Azure API 管理 |微軟文檔
description: 瞭解如何在 API 管理中使用開發人員門戶的託管版本。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: af7c995c11322a538dd9e27a905f1ddbc723e8ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244091"
---
# <a name="access-and-customize-developer-portal"></a>訪問和自訂開發人員門戶

開發人員門戶是一個自動生成的、完全可自訂的網站，包含 API 的文檔。 這是 API 消費者可以發現 API、瞭解如何使用它們和請求訪問的地方。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 訪問開發人員門戶的託管版本
> * 導航其管理介面
> * 自訂內容
> * 發佈更改
> * 查看已發佈的門戶

您可以在[Azure API 管理開發人員門戶概述](api-management-howto-developer-portal.md)中找到開發人員門戶上的更多詳細資訊。

![API 管理開發人員門戶 - 管理模式](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Prerequisites

- 完成以下快速入門：[創建 Azure API 管理實例](get-started-create-service-instance.md)
- 匯入並發佈 Azure API 管理執行個體。 有關詳細資訊，請參閱[導入和發佈](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>以管理員身份訪問門戶

按照以下步驟訪問門戶的託管版本。

1. 轉到 Azure 門戶中的 API 管理服務實例。
1. 按一下頂部巡覽列中的 **"開發人員門戶**"按鈕。 將打開具有門戶管理版本的新瀏覽器選項卡。

## <a name="understand-the-portals-administrative-interface"></a>瞭解門戶的管理介面

### <a name="default-content"></a>預設內容 

如果您是首次訪問門戶，則預設內容將自動預配到後臺。 預設內容旨在展示門戶的功能，並最大限度地減少個人化門戶所需的自訂量。 您可以瞭解有關[Azure API 管理開發人員門戶概述](api-management-howto-developer-portal.md)中門戶內容中包含的內容的詳細資訊。

### <a name="visual-editor"></a>視覺化編輯器

您可以使用視覺化編輯器自訂門戶的內容。 左側的功能表部分允許您創建或修改頁面、媒體、佈局、功能表、樣式或網站設置。 底部的功能表項目允許您在視口（例如移動或桌面）之間切換，查看門戶的元素，這些元素對經過身份驗證或匿名的使用者可見，或者保存或撤銷操作。

您可以通過按一下帶有加號的藍色圖示將行添加到頁面。 可以通過按帶有加號的灰色圖示來添加小部件（例如，文本、圖像或 API 清單）。 您可以使用拖放交互重新排列頁面中的專案。 

### <a name="layouts-and-pages"></a>佈局和頁面

![頁面和佈局](media/api-management-howto-developer-portal-customize/pages-layouts.png)

佈局定義頁面的顯示方式。 例如，在預設內容中，有兩個佈局 - 一個應用於主頁，另一個應用於所有剩餘的頁面。

佈局通過將頁面的 URL 範本與頁面的 URL 匹配而應用於頁面。 例如，具有 URL 範本`/wiki/*`的佈局將應用於 URL 中的`/wiki/`段：、、`/wiki/getting-started`等`/wiki/styles`具有該段的每個頁面。

在上面的圖像中，屬於佈局的內容以藍色標記，而頁面以紅色標記。 功能表部分分別標記。

### <a name="styling-guide"></a>造型指南

![造型指南](media/api-management-howto-developer-portal-customize/styling-guide.png)

造型指南是一個面板創建與設計師在心。 它允許監督和設置門戶中的所有可視元素。 樣式是階層式 - 許多元素從其他元素繼承屬性。 例如，按鈕元素對文本和背景使用顏色。 要更改按鈕的顏色，您需要更改原始顏色變體。

要編輯變體，請按一下它並選擇顯示在變體頂部的鉛筆圖示。 在快顯視窗中進行更改後，關閉它。

### <a name="save-button"></a>[儲存] 按鈕

![[儲存] 按鈕](media/api-management-howto-developer-portal-customize/save-button.png)

每當您在門戶中進行更改時，都需要通過按下底部功能表中的 **"保存"** 按鈕來手動保存它。 保存更改時，修改後的內容將自動上載到 API 管理服務。

## <a name="customize-the-portals-content"></a>自訂門戶的內容

在將門戶提供給訪問者之前，應個人化自動生成的內容。 建議的更改包括主頁的佈局、樣式和內容。

> [!NOTE]
> 出於集成方面的`/404`考慮，無法在不同的 URL 下刪除或移動以下頁面： `/500`、 、 `/captcha`、 `/change-password` `/config.json`、 `/confirm/invitation` `/confirm-v2/identities/basic/signup` `/confirm-v2/password` `/internal-status-0123456789abcdef` `/publish` `/signin` `/signin-sso` `/signup`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、

### <a name="home-page"></a>首頁

預設**主頁**中填充了虛擬內容。 您可以刪除包含內容的整個部分，也可以保留結構並逐個調整元素。 將生成的文本和圖像替換為您自己的文本和圖像，並確保連結指向所需位置。

### <a name="layouts"></a>版面配置

將巡覽列中自動生成的徽標替換為您自己的圖像。

### <a name="styling"></a>樣式

儘管不需要調整任何樣式，但您可以考慮調整特定元素。 例如，更改主顏色以匹配品牌的顏色。

### <a name="customization-example"></a>自訂示例

在下面的視頻中，我們將演示如何編輯門戶的內容、自訂網站的外觀以及發佈更改。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>發佈門戶

要使門戶及其最新更改可供訪問者使用，您需要發佈它。

1. 通過按一下"**保存"** 圖示，確保保存更改。
1. 按一下功能表"**操作"** 部分中的 **"發佈網站**"。 這項作業可能需要幾分鐘的時間。  
    ![發佈門戶](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> 在 API 管理服務配置更改（如分配自訂域、更新標識提供程式、設置委派、指定登錄和產品術語等）後，需要重新發佈門戶。

## <a name="visit-the-published-portal"></a>訪問已發佈的門戶

發佈門戶後，可以使用與管理面板相同的 URL 訪問它，例如`https://contoso-api.developer.azure-api.net`。 以外部訪問者身份在單獨的瀏覽器會話（隱身/私人瀏覽模式）中查看它。

## <a name="apply-the-cors-policy-on-apis"></a>在 API 上應用 CORS 策略

您需要在 API 上啟用 CORS（跨源資源分享），以便門戶訪問者通過內置的互動式主控台測試 API。 有關詳細資訊，請參閱[本文檔。](api-management-howto-developer-portal.md#cors)

## <a name="next-steps"></a>後續步驟

瞭解有關開發人員門戶的更多內容：

- [Azure API 管理開發人員入口網站概觀](api-management-howto-developer-portal.md)
