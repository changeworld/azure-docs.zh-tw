---
title: Azure 入口網站概觀 | Microsoft Docs
description: Azure 門戶是一個圖形化使用者介面，可用於管理 Azure 服務。 瞭解如何在 Azure 門戶中導航和查找資源。
services: azure-portal
keywords: 入口網站
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5e02e791185db3713c67b8ff97b8f7eebe9da77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310725"
---
# <a name="azure-portal-overview"></a>Azure 入口網站概觀

本文介紹 Azure 門戶，標識門戶頁面元素，並説明您熟悉 Azure 門戶管理體驗。

## <a name="what-is-the-azure-portal"></a>什麼是 Azure入口網站？

Azure 入口網站是統一的 Web 式主控台，提供有別於命令列工具的替代方案。 使用 Azure 門戶，可以使用圖形化使用者介面管理 Azure 訂閱。 您可以構建、管理和監視從簡單 Web 應用到複雜雲部署的所有內容。 為有組織的資源檢視創建自訂儀表板。 配置協助工具選項以獲得最佳體驗。

Azure 門戶專為恢復性和持續可用性而設計。 它在每個 Azure 資料中心中都有一個存在。 此配置使 Azure 門戶能夠抵禦單個資料中心故障，並通過接近使用者來避免網路速度減慢。 Azure 門戶會持續更新，並且無需停機進行維護活動。

## <a name="azure-portal-menu"></a>Azure 門戶功能表

您可以選擇門戶功能表的預設模式。 它可以停靠，也可以充當飛出面板。

當門戶功能表處於快顯視窗時，它將隱藏，直到您需要它。 選擇功能表圖示以打開或關閉功能表。

![在快顯視窗模式下的 Azure 門戶功能表](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

如果為門戶功能表選擇停靠模式，它將始終可見。 您可以折疊功能表以提供更多的工作空間。

![停靠模式下的 Azure 門戶功能表](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Azure 主頁

作為 Azure 服務的新訂閱者，登錄[門戶](https://portal.azure.com)後首先看到的是**Azure Home**。 此頁編譯可説明您從 Azure 訂閱中獲得最大資源的資源。 我們包括指向免費線上課程、文檔、核心服務和有用網站的連結，用於保持最新狀態並管理組織的更改。 為了快速、輕鬆地訪問正在進行的工作，我們還顯示您最近訪問的資源清單。 不能自訂此頁面，但可以選擇是將**Azure 主頁**或**Azure 儀表板**視為預設視圖。 首次登錄時，頁面頂部會提示您可以節省首選項。

![顯示預設視圖選擇器的螢幕截圖](./media/azure-portal-overview/azure-portal-default-view.png)

Azure 門戶功能表和 Azure 預設視圖都可以在**門戶設置**中更改。 如果更改所選內容，將立即應用更改。

![顯示預設視圖選擇器的螢幕截圖](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Azure 儀表板

儀表板提供訂閱中最重要的資源的集中視圖。這些資源對您至關重要。 我們為您提供一個預設儀表板來説明您入門。 您可以自訂此儀表板，將經常使用的資源引入單個視圖。 對預設視圖所做的任何更改僅影響您的體驗。 但是，您可以創建其他儀表板供自己使用，也可以發佈自訂儀表板，並與組織中的其他使用者共用這些儀表板。 有關詳細資訊，請參閱在[Azure 門戶中創建和共用儀表板](../azure-portal/azure-portal-dashboards.md)。

## <a name="getting-around-the-portal"></a>繞過門戶

瞭解基本門戶佈局以及如何與其交互很有説明。 在這裡，我們將介紹使用者介面的元件以及我們用於說明的一些術語。 有關門戶的更詳細的教程，請參閱課程課程[導航門戶](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal)。

Azure 門戶功能表和頁面標題是始終存在的全域元素。 這些持久功能是與每個服務或功能關聯的使用者介面的"外殼"，標頭提供對全域控制項的訪問。 資源的配置頁（有時稱為"邊欄選項卡"）可能還有一個資源功能表，可説明您在要素之間移動。

下圖示記了 Azure 門戶的基本元素，下表中描述了每個元素。

![顯示全屏門戶視圖和 UI 元素鍵的螢幕截圖](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![顯示擴展門戶功能表的螢幕截圖](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Key|描述
|:---:|---|
|1|頁眉。 出現在每個門戶頁面的頂部，並保存全域元素。|
|2| 全域搜索。 使用搜索欄快速查找特定資源、服務或文檔。|
|3|全域控制。 與所有全域元素一樣，這些功能會在整個門戶中持續存在，包括：雲外殼、訂閱篩選器、通知、門戶設置、説明和支援，以及向我們發送回饋。|
|4|您的帳戶。 查看有關您的帳戶、切換目錄、登出或使用其他帳戶登錄的資訊。|
|5|門戶功能表。 門戶功能表是一個全域元素，可説明您在服務之間導航。 有時稱為側邊欄，門戶功能表模式可以在**門戶設置**中更改。|
|6|資源功能表。 許多服務包括一個資源功能表，以説明您管理該服務。 您可能會看到此元素稱為左側窗格。|
|7|命令列。 命令列上的控制項與當前焦點是上下文的。|
|8|工作窗格。  顯示有關當前焦點資源的詳細資訊。|
|9|麵包 屑。 您可以使用痕跡連結在工作流中移回級別。|
|10|主控制項以在當前訂閱中創建新資源。 展開或打開門戶功能表以查找 **+ 創建資源**。 搜索或流覽 Azure 應用商店以獲取要創建的資源類型。|
|11|我的最愛清單。 請參閱[添加、刪除和排序我的最愛](../azure-portal/azure-portal-add-remove-sort-favorites.md)，瞭解如何自訂清單。|

## <a name="get-started-with-services"></a>開始使用服務

如果您是新訂閱者，則在需要管理任何內容之前，必須創建資源。 選擇 **= 創建資源**以查看 Azure 應用商店中可用的服務。 您將在此處找到來自數百家供應商的應用程式和服務，這些提供程式都經過 Azure 認證。

我們預先填充了您的我的最愛在側邊欄與常用服務的連結。  要查看所有可用服務，請從側邊欄中選擇 **"所有服務**"。

> [!TIP]
> 查找資源、服務或文檔的最快方法是在全域標頭中使用 *"搜索*"。 使用痕跡連結返回前幾頁。
>
觀看此視頻，瞭解如何在 Azure 門戶中使用全域搜索。


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[如何在 Azure 門戶中使用全域搜索](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>後續步驟

* 詳細瞭解在[受支援的瀏覽器和設備](../azure-portal/azure-portal-supported-browsers-devices.md)中運行 Azure 門戶的位置
* 使用[Azure 移動應用](https://azure.microsoft.com/features/azure-portal/mobile-app/)保持連接
* 使用[Azure 快速啟動中心](../azure-portal/azure-portal-quickstart-center.md)進行板載並設置雲環境
