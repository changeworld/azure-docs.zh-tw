---
title: 在 API 管理中使用修訂項目安全地進行不間斷的變更
titleSuffix: Azure API Management
description: 依照本教學課程的步驟以了解如何使用 API 管理中的修訂進行不間斷的變更。
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
ms.openlocfilehash: 7a4655b20fabcc72e02037de05dd0ef7c4671e52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254906"
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>使用修訂項目安全地進行不間斷的變更
當您的 API 已準備就緒，且開發人員也已準備好使用時，您就必須對該 API 進行變更，同時避免中斷 API 呼叫者的作業。 這對於讓開發人員了解您所做的更改也相當有用。 我們可以使用**修訂**在 Azure API 管理中進行此動作。 如需詳細資訊，請參閱[版本與修訂](https://azure.microsoft.com/blog/versions-revisions/)和[使用 Azure API 管理的 API 版本設定](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增修訂
> * 對您的修訂進行不間斷變更
> * 請讓您的修訂為最新版本，並新增變更記錄項目
> * 瀏覽開發人員入口網站，以查看變更與變更記錄檔

![開發人員入口網站上的變更記錄檔](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Prerequisites

+ 了解 [Azure API 管理術語](api-management-terminology.md)。
+ 完成下列快速入門：[建立 Azure APIM 執行個體](get-started-create-service-instance.md)。
+ 此外，請完成下列教學課程：[匯入和發佈您的第一個 API](import-and-publish.md)。

## <a name="add-a-new-revision"></a>新增修訂

![新增 API 修訂](media/api-management-getstarted-revise-api/07-AddRevisions-01-AddNewRevision.png)

1. 選取 [API]  頁面。
2. 從 API 清單中選取 [Demo Conference API]  (或其他您要新增修訂的 API)。
3. 從頁面頂端附近的功能表按一下 [修訂]  索引標籤。
4. 選取 [+ 新增修訂] 

    > [!TIP]
    > 您也可以在 API 上選擇操作功能表 ( **...** ) 中的 [新增修訂]  。

5. 提供新修訂的描述，以協助您記住其用途。
6. 選取 [建立] 
7. 現在已建立新的修訂。

    > [!NOTE]
    > 原始的 API 會保留在**修訂 1**。 這是您的使用者將繼續呼叫的修訂，直到您選擇進行其他修訂為止。

## <a name="make-non-breaking-changes-to-your-revision"></a>對您的修訂進行不間斷變更

![修改修訂](media/api-management-getstarted-revise-api/07-AddRevisions-02-MakeChanges.png)

1. 從 API 清單中選取 [Demo Conference API]  。
2. 選取畫面頂端附近的 [設計]  索引標籤。
3. 請注意，**修訂選取器** (在 [設計] 索引標籤的正上方) 會顯示目前已選取**修訂 2**。

    > [!TIP]
    > 使用修訂選取器在您要進行的修訂之間切換。

4. 選取 [+ 新增作業]  。
5. 將您的新作業設定為 **POST**，並將作業的「名稱、顯示名稱和 URL」設定為**測試**。
6. **儲存**您的新作業。
7. 現在，我們已對**修訂 2** 進行變更。 使用頁面頂端附近的 [修訂選取器]  ，即可切換回**修訂 1**。
8. 請注意，新的作業不會出現在**修訂 1**。 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>請讓您的修訂為最新版本，並新增變更記錄項目

1. 從頁面頂端附近的功能表選取 [修訂]  索引標籤。

    ![修訂畫面上的 [修訂] 功能表。](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)

2. 針對**修訂 2** 開啟操作功能表 ( **...** )。
3. 選取 [設為最新]  。
4. 如果您想要發佈這項變更的相關注意事項，請核取 [發佈到此 API 的公用變更記錄檔]  。 提供讓開發人員查看的變更說明，例如：**測試修訂。已新增「測試」作業。**
5. **修訂 2** 目前為最新狀態。

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>瀏覽開發人員入口網站，以查看變更與變更記錄檔

1. 在 Azure 入口網站中，選取 [API]  。
2. 從頂端功能表選取 [開發人員入口網站]  。
3. 選取 [API]  ，然後選取 [Demo Conference API]  。
4. 請注意，您現在可以使用新的**測試**作業。
5. 按一下 [API 名稱] 附近的 [變更記錄]  。
6. 請注意，您的變更記錄項目會出現在此清單中。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 新增修訂
> * 對您的修訂進行不間斷變更
> * 請讓您的修訂為最新版本，並新增變更記錄項目
> * 瀏覽開發人員入口網站，以查看變更與變更記錄檔

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [為您的 API 發佈多個版本](api-management-get-started-publish-versions.md)
