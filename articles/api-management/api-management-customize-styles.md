---
title: 在 API 管理舊版開發人員入口網站上自訂網頁樣式
titleSuffix: Azure API Management
description: 若要在 Azure API 管理開發人員入口網站上自訂元素樣式，請遵循此快速入門中的步驟。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 664686511df8f310295a9f6ed6bc689b3a999544
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75430741"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>自訂開發人員入口網站頁面的風格

在 Azure API 管理中自訂開發人員入口網站最常用的方式有三種：
 
* [編輯靜態頁面和頁面配置元素的內容](api-management-modify-content-layout.md)
* 更新用於開發人員入口網站上頁面元素的樣式 (如本指南所述)
* [修改入口網站所產生的網頁用範本](api-management-developer-portal-templates.md) (例如 API 文件、產品、使用者驗證)

在本文中，您將瞭解如何在舊版**開發人員**入口網站的頁面上自訂元素的樣式，並查看您的變更。

![自訂樣式](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Prerequisites

+ 了解 [Azure API 管理術語](api-management-terminology.md)。
+ 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 同時也請完成下列教學課程：[匯入和發佈您的第一個 API](import-and-publish.md)。

## <a name="customize-the-developer-portal"></a>自訂開發人員入口網站

1. 選取 [概觀]  。
2. 按一下 [概觀]  視窗頂端的 [開發人員入口網站 (舊版)]  按鈕。
3. 在螢幕的左上方，您會看到由兩支筆刷組成的圖示。 將滑鼠停留在此圖示上可開啟入口網站的自訂功能表。

    ![自訂樣式](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. 選取功能表中的 [樣式]  ，開啟自訂樣式窗格。

    您可以使用頁面上出現的 [樣式]  自訂所有元素
5. 在 [變更變數值以自訂開發人員入口網站外觀：]  欄位中輸入 "headings-color"。

    **\@headings-color** 元素會出現在頁面上。 此變數會控制文字的色彩。

    ![自訂樣式](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. 按一下 **\@headings-color** 變數的欄位。 
    
    此時會開啟色彩選擇器下拉式清單。
7. 從色彩選擇器下拉式清單選取新的色彩。

    > [!TIP]
    > 您可以即時預覽所有變更。 進度列指示器會出現在自訂窗格的頂端。 幾秒之後，頁首文字就會變更為新選取的色彩。

8. 在自訂窗格功能表的左下方，選取 [發佈]  。
9. 選取 [發佈自訂]  讓變更可公開使用。

## <a name="view-your-change"></a>檢視變更

1. 瀏覽至開發人員入口網站。
2. 您可以看到您所做的變更。

## <a name="next-steps"></a>後續步驟

您可能想要了解[如何使用範本自訂 Azure API 管理開發人員入口網站](api-management-developer-portal-templates.md)。