---
title: 存取和自訂受控開發人員入口網站-Azure API 管理 |Microsoft Docs
description: 瞭解如何在 API 管理中使用 managed 版開發人員入口網站。
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
ms.openlocfilehash: 345b4950e25f48eb1a48c5e583908d0f771771ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86206242"
---
# <a name="access-and-customize-developer-portal"></a>存取和自訂開發人員入口網站

開發人員入口網站是自動產生、可完全自訂的網站，內含 Api 的檔。 API 取用者可以在此探索您的 Api、瞭解如何使用它們，以及如何要求存取權。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 存取 managed 版本的開發人員入口網站
> * 流覽其系統管理介面
> * 自訂內容
> * 發佈變更
> * 查看已發佈的入口網站

您可以在 [AZURE API 管理開發人員入口網站總覽](api-management-howto-developer-portal.md)中找到更多有關開發人員入口網站的詳細資料。

![API 管理開發人員入口網站-管理員模式](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Prerequisites

- 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)
- 匯入並發佈 Azure API 管理執行個體。 如需詳細資訊，請參閱匯 [入和發佈](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>以系統管理員身分存取入口網站

請遵循下列步驟來存取受管理的入口網站版本。

1. 移至 Azure 入口網站中的 API 管理服務實例。
1. 按一下頂端導覽列中的 [ **開發人員入口網站** ] 按鈕。 將會開啟新的瀏覽器索引標籤，其中包含入口網站的系統管理版本。

## <a name="understand-the-portals-administrative-interface"></a>瞭解入口網站的系統管理介面

### <a name="default-content"></a>預設內容 

如果您是第一次存取入口網站，則會自動在背景中布建預設內容。 預設內容設計為展示入口網站的功能，並將個人化您的入口網站所需的自訂數量降至最低。 您可以深入瞭解 [AZURE API 管理開發人員入口網站總覽](api-management-howto-developer-portal.md)中的入口網站內容包含哪些內容。

### <a name="visual-editor"></a>視覺效果編輯器

您可以使用視覺化編輯器自訂入口網站的內容。 左邊的功能表區段可讓您建立或修改頁面、媒體、版面配置、功能表、樣式或網站設定。 底部的功能表項目可讓您切換各區 (例如，行動或桌面) 、查看已驗證或匿名使用者可看見的入口網站元素，或是儲存或復原動作。

您可以按一下具有加號的藍色圖示，將資料列加入至頁面。 小工具 (例如，文字、影像或 Api 清單) 可以藉由按下灰色圖示加上加號來新增。 您可以使用拖放互動來重新排列頁面中的專案。 

### <a name="layouts-and-pages"></a>版面配置和頁面

![頁面和版面配置](media/api-management-howto-developer-portal-customize/pages-layouts.png)

版面配置會定義頁面的顯示方式。 例如，在預設內容中，有兩個版面配置：一個適用于首頁，另一個則適用于所有剩餘的頁面。

版面配置會透過將其 URL 範本與頁面的 URL 進行比對，以套用至頁面。 例如，URL 範本為的版面配置將會套用 `/wiki/*` 至 url 中有區段的每個頁面 `/wiki/` ： `/wiki/getting-started` 、 `/wiki/styles` 等等。

在上圖中，屬於配置的內容會以藍色標示，而頁面會以紅色標示。 功能表區段會分別標記。

### <a name="styling-guide"></a>樣式指南

![樣式指南](media/api-management-howto-developer-portal-customize/styling-guide.png)

樣式指南是以設計工具為考慮而建立的面板。 它可讓您監督及設定入口網站中所有視覺元素的樣式。 樣式是階層式的許多元素，會繼承其他元素的屬性。 例如，按鈕元素使用文字和背景的色彩。 若要變更按鈕的色彩，您需要變更原始的色彩變異數。

若要編輯變異，請按一下它，然後選取出現在其頂端的鉛筆圖示。 當您在快顯視窗中進行變更之後，請將其關閉。

### <a name="save-button"></a>[儲存] 按鈕

![[儲存] 按鈕](media/api-management-howto-developer-portal-customize/save-button.png)

當您在入口網站中進行變更時，您需要按底部功能表中的 [ **儲存** ] 按鈕，以手動方式儲存。 當您儲存變更時，修改過的內容會自動上傳至您的 API 管理服務。

## <a name="customize-the-portals-content"></a>自訂入口網站的內容

在您將入口網站提供給訪客使用之前，您應該將自動產生的內容個人化。 建議的變更包括首頁的版面配置、樣式和內容。

> [!NOTE]
> 由於整合考慮，無法在不同的 URL 下移除或移動下列頁面： `/404` 、 `/500` 、 `/captcha` 、、、 `/change-password` `/config.json` `/confirm/invitation` 、 `/confirm-v2/identities/basic/signup` `/confirm-v2/password` `/internal-status-0123456789abcdef` `/publish` `/signin` `/signin-sso` `/signup` 、、、、、、。

### <a name="home-page"></a>首頁

預設的 **首頁** 會填入虛擬內容。 您可以移除內容的整個區段，或保留結構並逐一調整元素。 將產生的文字和影像取代為您自己的文字，並確定連結指向所需的位置。

### <a name="layouts"></a>版面配置

使用您自己的影像來取代巡覽列中自動產生的標誌。

### <a name="styling"></a>樣式

雖然您不需要調整任何樣式，但您可以考慮調整特定的元素。 例如，變更主要色彩以符合您品牌的色彩。

### <a name="customization-example"></a>自訂範例

在下列影片中，我們將示範如何編輯入口網站的內容、自訂網站的外觀，以及發佈變更。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>發佈入口網站

若要讓您的入口網站與其最新的變更可供訪客使用，您必須將其發佈。

1. 按一下 [ **儲存** ] 圖示，確定您已儲存變更。
1. 在功能表的 [**作業**] 區段中，按一下 [**發行網站**]。 這項作業可能需要幾分鐘的時間。  
    ![發佈入口網站](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> 入口網站必須在 API 管理服務設定變更之後重新發佈，例如指派自訂網域、更新身分識別提供者、設定委派、指定登入和產品條款等等。

## <a name="visit-the-published-portal"></a>造訪已發佈的入口網站

發佈入口網站之後，您可以在與系統管理面板相同的 URL 上存取它，例如 `https://contoso-api.developer.azure-api.net` 。 請在不同的瀏覽器會話中查看它 (incognito/私用瀏覽模式) 為外部訪客。

## <a name="apply-the-cors-policy-on-apis"></a>對 Api 套用 CORS 原則

您必須在您的 Api 上啟用 CORS (跨原始資源分享) ，讓您入口網站的訪客可透過內建的互動式主控台來測試 Api。 如需詳細資訊，請參閱 [此檔文章](api-management-howto-developer-portal.md#cors) 。

## <a name="next-steps"></a>接下來的步驟
- [優化並節省您的雲端費用](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

深入瞭解開發人員入口網站：

- [Azure API 管理開發人員入口網站概觀](api-management-howto-developer-portal.md)