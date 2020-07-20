---
title: Azure 入口網站概觀 | Microsoft Docs
description: Azure 入口網站是可用來管理 Azure 服務的圖形化使用者介面。 了解如何在 Azure 入口網站中瀏覽及尋找資源。
services: azure-portal
keywords: 入口網站
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: overview
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 4aa1ccbd9b605850052e190949542eb014fe90a8
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763919"
---
# <a name="azure-portal-overview"></a>Azure 入口網站概觀

本文介紹 Azure 入口網站、識別入口網站頁面元素，並協助您熟悉 Azure 入口網站管理體驗。

## <a name="what-is-the-azure-portal"></a>什麼是 Azure入口網站？

Azure 入口網站是統一的 Web 式主控台，提供有別於命令列工具的替代方案。 透過 Azure 入口網站，您可使用圖形化使用者介面來管理 Azure 訂用帳戶。 您可以建置、管理和監視各種事件，包含簡單 Web 應用程式到複雜的雲端部署等。 為井然有序的資源檢視建立自訂儀表板。 設定協助工具選項以獲得最佳體驗。

Azure 入口網站專門設計來提供復原和持續可用性。 其存在於每個 Azure 資料中心。 此設定可讓 Azure 入口網站從個別資料中心失敗復原，並藉由接近使用者來避免網路變慢。 Azure 入口網站會持續更新，而且不需要停機進行維護活動。

## <a name="azure-portal-menu"></a>Azure 入口網站功能表

您可以選擇入口網站功能表的預設模式。 其可停駐，也可當作飛出視窗面板。

當入口網站功能表處於飛出視窗模式時，在您需要使用前，該功能表會隱藏起來。 選取功能表圖示以開啟或關閉功能表。

![處於飛出視窗模式的 Azure 入口網站功能表](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

如果您為入口網站功能表選擇停駐模式，則一律會顯示。 您可以摺疊功能表來提供更多的工作空間。

![處於停駐的 Azure 入口網站功能表](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Azure 首頁

身為 Azure 服務的新訂閱者，您在[登入入口網站](https://portal.azure.com)之後，首先會看到 **Azure 首頁**。 此頁面會編譯資源，協助您充分利用您的 Azure 訂用帳戶。 我們已包含免費線上課程、文件、核心服務和實用網站的連結，可讓您保持最新狀態並管理貴組織的變更。 為了讓您快速而輕鬆地存取進行中的工作，我們也會顯示您最近瀏覽過的資源清單。 您無法自訂此頁面，但可以選擇要看到 [Azure 首頁]**** 或 [Azure 儀表板]**** 作為預設檢視。 第一次登入時，頁面頂端會出現提示，您可以在其中儲存您的喜好設定。

![顯示預設檢視選取器的螢幕擷取畫面](./media/azure-portal-overview/azure-portal-default-view.png)

Azure 入口網站功能表和 Azure 預設檢視都可以在 [入口網站設定]**** 中進行變更。 如果您變更您的選取項目，就會立即套用變更。

![顯示預設檢視選取器的螢幕擷取畫面](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Azure 儀表板

儀表板會針對您的訂用帳戶中對您最重要的資源，提供聚焦的檢視。 我們為您提供了預設儀表板，讓您可以開始使用。 您可以自訂此儀表板，將您經常使用的資源帶入單一檢視中。 您對預設檢視進行的任何變更都只會影響您的體驗。 不過，您可以建立其他儀表板供自己使用，或發佈自訂的儀表板並與貴組織中的其他使用者共用。 如需詳細資訊，請參閱[在 Azure 入口網站中建立和共用儀表板](../azure-portal/azure-portal-dashboards.md)。

## <a name="getting-around-the-portal"></a>輕鬆瀏覽入口網站

了解基本入口網站配置，以及其互動方式很有幫助。 在此，我們將介紹使用者介面的元件，以及我們用來提供指示的一些術語。 如需入口網站的詳細導覽，請參閱[瀏覽入口網站](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal)課程。

Azure 入口網站功能表和頁首是一律存在的全域元素。 這些持續性功能是與每項個別服務或功能相關聯之使用者介面的「殼層」，而標頭可提供對全域控制項的存取權。 資源的設定頁面 (有時稱為「刀鋒視窗」) 可能也有資源功能表可協助您在各項功能間移動。

下圖標示出 Azure 入口網站的基本元素，而每個元素都會在下表中加以說明。

![顯示全螢幕入口網站檢視和 UI 元素索引鍵的螢幕擷取畫面](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![顯示已展開入口網站功能表的螢幕擷取畫面](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Key|描述
|:---:|---|
|1|頁首。 出現在每個入口網站頁面的頂端並保留全域元素。|
|2| 全域搜尋。 使用搜尋列來快速尋找特定資源、服務或文件。|
|3|全域控制項。 就像所有全域元素一樣，這些功能會存留在入口網站各處，包括：Cloud Shell、訂用帳戶篩選、通知、入口網站設定、說明及支援，以及傳送意見反應。|
|4|您的帳戶。 檢視您帳戶的相關資訊、切換目錄、登出，或使用不同帳戶登入。|
|5|入口網站功能表。 入口網站功能表是一個全域元素，可協助您在各項服務間瀏覽。 有時也稱為資訊看板，在 [入口網站設定]**** 中可以變更入口網站功能表模式。|
|6|資源功能表。 許多服務都包含資源功能表可協助您管理服務。 您可能會看到此元素稱為左窗格。|
|7|命令列。 命令列上的控制項會與您目前的焦點有關聯性。|
|8|工作窗格。  顯示目前焦點所在資源的詳細資料。|
|9|階層連結。 您可以使用階層連結的連結，在工作流程中往回移動一層。|
|10|用於在目前訂用帳戶中建立新資源的主要控制項。 展開或開啟入口網站功能表來尋找 [+ 建立資源]****。 搜尋或瀏覽 Azure Marketplace 中您想要建立的資源類型。|
|11|我的最愛清單。 請參閱[新增、移除和排序我的最愛](../azure-portal/azure-portal-add-remove-sort-favorites.md)，以了解如何自訂清單。|

## <a name="get-started-with-services"></a>開始使用服務

如果您是新的訂閱者，則必須先建立資源，才能進行管理。 選取 [+ 建立資源]**** 以檢視 Azure Marketplace 中可用的服務。 您會在此找到來自數百個提供者的應用程式和服務，全都經過認證可在 Azure 上執行。

我們已在資訊看板中預先填入您的最愛，並提供常用服務的連結。  若要檢視所有可用的服務，請從資訊看板中選取 [所有服務]****。

> [!TIP]
> 尋找資源、服務或文件的最快方式是使用全域標頭中的 [搜尋]**。 使用階層連結的連結來回到先前的頁面。
>
觀看本影片，以取得如何在 Azure 入口網站中使用全域搜尋的示範。


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[如何在 Azure 入口網站中使用全域搜尋](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>後續步驟

* 深入了解在[支援的瀏覽器和裝置](../azure-portal/azure-portal-supported-browsers-devices.md)中執行 Azure 入口網站的位置
* 透過 [Azure 行動應用程式](https://azure.microsoft.com/features/azure-portal/mobile-app/)隨時保持連線
* 透過 [Azure 快速入門中心](../azure-portal/azure-portal-quickstart-center.md)將您的雲端環境上線並加以設定
