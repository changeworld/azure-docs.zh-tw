---
title: 教學課程 - 使用 Azure API 管理發佈 API 版本
description: 依照本教學課程的步驟，了解如何在 API 管理中發佈多個 API 版本。
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: e6afa26c65f097683a5b471dc34621cca38c01e6
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377371"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>教學課程：為您的 API 發佈多個版本 

有時候，讓 API 的所有呼叫者使用完全相同的版本不太實際。 當呼叫者想要升級至較新版本時，他們需要一個容易了解的方法。 如本教學課程所示，在 Azure API 管理中提供多個 *版本*，是可行的作業。 

如需背景資訊，請參閱[版本與修訂](https://azure.microsoft.com/blog/versions-revisions/)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 將新版本新增至現有 API
> * 選擇版本配置
> * 將版本新增至產品
> * 瀏覽開發人員入口網站以查看版本

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Azure 入口網站中顯示的版本":::

## <a name="prerequisites"></a>Prerequisites

+ 了解 [Azure API 管理術語](api-management-terminology.md)。
+ 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 同時也請完成下列教學課程：[匯入和發佈您的第一個 API](import-and-publish.md)。

## <a name="add-a-new-version"></a>加入新版本

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 API 管理執行個體。
1. 選取 [API]。
1. 從 API 清單中選取 [Demo Conference API]。 
1. 選取 **Demo Conference API** 旁的操作功能表 ( **...** )。
1. 選取 [新增版本]。

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="API 操作功能表 - 新增版本":::


> [!TIP]
> 您也可以在建立新的 API 時啟用版本。 在 [新增 API] 畫面上，選取 [要為此 API 設定版本嗎？]。

## <a name="choose-a-versioning-scheme"></a>選擇版本設定配置

在 Azure API 管理中，您可以選取下列 *版本設定配置*，以選擇呼叫者指定 API 版本的方式：**路徑、標頭** 或 **查詢字串**。 下列範例使用 *路徑* 作為版本設定配置。

從下表中輸入值。 然後，選取 [建立] 以建立您的版本。

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="新增版本視窗":::



|設定   |值  |描述  |
|---------|---------|---------|
|**名稱**     |  *demo-conference-api-v1*       |  API 管理執行個體中的唯一名稱。<br/><br/>版本實際上是以 API 的[修訂](api-management-get-started-revise-api.md)為基礎的新 API，因此這項設定就是新的 API 名稱。   |
|**版本設定配置**     |  **路徑**       |  呼叫者指定 API 版本的方式。     |
|**版本識別碼**     |  *v1*       |  版本的配置特定指標。 **路徑** 的識別碼為 API URL 路徑的尾碼。 <br/><br/> 如果選取 [標頭] 或 [查詢字串]，請輸入額外的值：標頭的名稱或查詢字串參數。<br/><br/> 請參考顯示的使用範例。        |
|**產品**     |  **無限制**       |  (選擇性) 與 API 版本相關聯的一或多個產品。 若要發佈 API，您必須將它與產品產生關連。 您也可於後續[將版本新增至產品](#add-the-version-to-a-product)。      |

建立版本後，版本此時會顯示在 API 清單中的 **Demo Conference API** 下方。 您現在會看到兩個 API：**原始** 和 **v1**。

![在 Azure 入口網站中 API 下列出的版本](media/api-management-getstarted-publish-versions/version-list.png)

您現在可以編輯 **v1**，並將其設定為與 **原始** 不同的 API。 對某個版本進行變更不會影響另一個版本。

> [!Note]
> 如果您將版本新增至未設定版本的 API，則也會自動建立 **原始**。 此版本會反映在預設 URL 上。 建立原始版本，可確保任何現有的呼叫者都不會因為新增版本的程序而受到干擾。 如果您在開始時建立已啟用版本的新 API，則不會建立「原始」。

## <a name="add-the-version-to-a-product"></a>將版本新增至產品

為了讓呼叫端看到新的版本，您必須將該版本新增至 *產品*。 如果您尚未將版本新增至產品，可以隨時將其新增至產品。

例如，若要將版本新增至「無限制」產品：
1. 在 Azure 入口網站中，瀏覽至您的 API 管理執行個體。
1. 選取 [產品] > [無限制] > [API] > [+ 新增]。
1. 選取 [Demo Conference API，版本 v1] 。
1. 按一下 [選取]。

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="將版本新增至產品":::

## <a name="browse-the-developer-portal-to-see-the-version"></a>瀏覽開發人員入口網站以查看版本

如果您嘗試使用[開發人員入口網站](api-management-howto-developer-portal-customize.md)，則可在該處檢視 API 版本。

1. 從頂端功能表選取 [開發人員入口網站]。
2. 選取 [API]，然後選取 [Demo Conference API]。
3. 您應該會在 API 名稱旁邊看到有多個版本的下拉式清單。
4. 選取 [v1]。
5. 請注意清單中第一項作業的 [要求 URL]。 它會顯示 API URL 路徑，包含 **v1**。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 將新版本新增至現有 API
> * 選擇版本配置 
> * 將版本新增至產品
> * 瀏覽開發人員入口網站以查看版本

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [自訂開發人員入口網站頁面的風格](api-management-customize-styles.md)
