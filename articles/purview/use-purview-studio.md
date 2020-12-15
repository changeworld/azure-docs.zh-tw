---
title: 使用 Purview Studio
description: 此概念文章說明如何使用 Azure 範疇 Studio。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: d8e6c4b2addf9745b2ddabe8f6fdad9d82dce59f
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503945"
---
# <a name="use-purview-studio"></a>使用 Purview Studio

本文概述 Azure 範疇的一些主要功能。

## <a name="prerequisites"></a>Prerequisites

* 使用中的範疇帳戶已在 Azure 入口網站中建立，且使用者具有存取範疇 Studio 的許可權。

## <a name="launch-purview-account"></a>啟動範疇帳戶

* 若要啟動您的範疇帳戶，請移至 Azure 入口網站中的 [範疇帳戶]，選取您要啟動的帳戶，並啟動該帳戶。

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="選取專案的螢幕擷取畫面，以啟動 Azure 範疇帳戶類別目錄。":::

* 另一種啟動範疇帳戶的方法是移至 `https://web.purview.azure.com` 、選取 **Azure Active Directory** ，以及用來啟動帳戶的帳戶名稱。

## <a name="home-page"></a>首頁

**Home** 是 Azure 範疇用戶端的起始頁面。

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="首頁的螢幕擷取畫面。":::

下列清單摘要說明 [ **首頁**] 的主要功能。 清單中的每個數位都會對應到上一個螢幕擷取畫面中反白顯示的數位。

1. 目錄的易記名稱。 您可以在 [ **管理中心** ] 中設定目錄名稱 > * [*帳戶資訊*]。

2. 目錄分析會顯示下列各項的數目：
    - 使用者、群組和應用程式
    - 資料來源
    - Assets
    - 字彙

3. 搜尋方塊可讓您搜尋資料目錄中的資料資產。

4. [快速存取] 按鈕可讓您存取應用程式的常用功能。 顯示的按鈕取決於指派給您的使用者帳戶的角色。

    - 針對 *資料編者*，按鈕為 **知識中心**、 **流覽資產**、 **管理詞彙** 和 **查看見解**。
    - 針對 *資料讀取器*，精選按鈕包括 **知識中心**、 **流覽資產**、 **查看詞彙** 和 **查看見解**。
    - 針對 *資料來源管理員*  +  *資料編者*，精選按鈕為 **知識中心**、**註冊資料來源**、**流覽資產**，以及 **管理詞彙**。
    - 針對 *資料來源系統管理員*  +  *資料讀取器*，精選按鈕為 **知識中心**、**註冊資料來源**、**流覽資產**，以及 **查看詞彙**。

5. 左側導覽列可協助您找出應用程式的主頁面。 顯示的按鈕取決於指派給您的使用者帳戶的角色。

    - 針對 *資料編者*，按鈕為 **Home**、 **詞彙**、 **見解** 和 **管理中心**。
    - 針對 *資料讀取器*，按鈕為 **Home**、 **詞彙**、 **見解** 和 **管理中心**。
    - 針對 *資料來源管理員*  +  *資料編者/讀取器*，這些按鈕是 **首頁**、**來源**、**詞彙**、**見解** 和 **管理中心**。
  
6. [ **最近存取** ] 索引標籤會顯示最近存取之資料資產的清單。 如需存取資產的相關資訊，請參閱 [搜尋資料目錄](how-to-search-catalog.md) 和 [依資產類型流覽](how-to-browse-catalog.md#browse-experience)。  [**我的專案**] 索引標籤是已登入之使用者所擁有的資料資產清單。
7. **有用的連結** 包含區域狀態、檔、定價、總覽和範疇狀態的連結
8. 頂端導覽列包含版本資訊/更新、變更範疇帳戶、通知、說明和意見反應章節的相關資訊。

## <a name="knowledge-center"></a>知識中心

知識中心可讓您找到與範疇相關的所有影片和教學課程。

## <a name="guided-tours"></a>導覽

Azure 範疇 Studio 中的每個 UX 都會有引導式導覽，以提供頁面的總覽。 若要開始導覽， **請選取頂端** 列的 [說明]，然後選取 [ **引導式** 導覽]。

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="導覽的螢幕擷取畫面。":::

> [!Important]
   > 資料來源管理員角色本身無法存取範疇 Studio。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增安全性主體](tutorial-scan-data.md)