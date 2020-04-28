---
title: 在 API 管理中修改開發人員入口網站中的頁面內容
titleSuffix: Azure API Management
description: 了解如何在 Azure API 管理中編輯開發人員入口網站的頁面內容。
services: api-management
documentationcenter: ''
author: vlvinogr
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: vlvinogr
ms.openlocfilehash: ebf2cbd430339378a09d10d91ad61327d24842e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75430624"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>在 Azure API 管理中修改開發人員入口網站的頁面內容和配置
在 Azure API 管理中自訂開發人員入口網站的基本方式有三種：

* [編輯靜態頁面和頁面配置元素的內容][modify-content-layout] (如本指南所述)
* [更新用於開發人員入口網站上頁面元素的樣式][customize-styles]
* [修改入口網站所產生的網頁用範本][portal-templates] (例如 API 文件、產品、使用者驗證等)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="structure-of-developer-portal-pages"></a><a name="page-structure"> </a>開發人員入口網站頁面的結構

開發人員入口網站是以內容管理系統為基礎。 每個頁面的配置都是根據一組小型網頁元素 (也稱為 Widget) 來建置：

![開發人員入口網站頁面結構][api-management-customization-widget-structure]

所有 Widget 都是可編輯的。
* 每個個別頁面特定的核心內容是位於 [內容] Widget 中。 編輯網頁，表示編輯此 Widget 的內容。
* 所有頁面配置元素均隨附於其餘的 Widget 中。 對這些小工具所做的變更會套用至所有頁面。 它們也稱為「配置小工具」。

在日常頁面中，編輯某一個頁面通常只會修改內容小工具，它將針對每個個別頁面擁有不同的內容。

## <a name="modifying-the-contents-of-a-layout-widget"></a><a name="modify-layout-widget"> </a>修改配置 Widget 的內容

您可以從 Azure 入口網站存取開發人員入口網站。

1. 從 API 管理執行個體的工具列中按一下 [開發人員入口網站]****。
2. 若要編輯小工具的內容，請從左側的 [開發人員入口網站]**** 功能表中按一下由兩個筆刷所組成的圖示。
3. 若要修改標頭內容，請捲動到左側清單中的 [標頭]**** 區段。

    您可以從欄位中編輯小工具。
4. 當您準備好發佈變更時，請按一下頁面底部的 [發佈]****。

現在，您應該會在開發人員入口網站內的每個頁面上看到新的頁首。

## <a name="next-steps"></a><a name="next-steps"> </a>後續步驟
* [更新用於開發人員入口網站上頁面元素的樣式][customize-styles]
* [修改入口網站所產生的網頁用範本][portal-templates] (例如 API 文件、產品、使用者驗證等)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
