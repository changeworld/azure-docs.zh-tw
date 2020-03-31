---
title: 連接至 Dropbox。
description: 使用 Azure 邏輯應用自動執行上載和管理 Dropbox 中檔的任務和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665746"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>使用 Azure 邏輯應用在 Dropbox 中上載和管理檔

使用 Dropbox 連接器和 Azure 邏輯應用，您可以創建自動工作流來上載和管理 Dropbox 帳戶中的檔。 

本文演示如何從邏輯應用連接到 Dropbox，然後在**創建檔觸發器時**添加 Dropbox，以及**使用路徑操作獲取檔內容**的 Dropbox 獲取檔內容。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [一個Dropbox帳戶](https://www.dropbox.com/)，你可以免費註冊。 在邏輯應用和 Dropbox 帳戶之間創建連接時，您的帳戶憑據是必需的。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 針對此範例，您需要空白的邏輯應用程式。

## <a name="add-trigger"></a>新增觸發程序

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 在搜尋方塊下方，選擇 [全部]****。 在搜尋方塊中，輸入 "dropbox" 作為篩選條件。
從觸發程序清單中選取此觸發程序：**建立檔案時**

   ![選取 Dropbox 觸發程序](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. 使用您的 Dropbox 帳戶認證登入，並授與 Azure Logic Apps 存取您的 Dropbox 資料的權限。

1. 為您的觸發程序提供必要的資訊。 

   在此示例中，選擇要追蹤檔案創建的資料夾。 要流覽資料夾，請選擇 **"資料夾**"框旁邊的資料夾圖示。

## <a name="add-action"></a>新增動作

現在添加從任何新檔獲取內容的操作。

1. 在觸發程序下方，選擇 [下一個步驟]****。 

1. 在搜尋方塊下方，選擇 [全部]****。 在搜尋方塊中，輸入 "dropbox" 作為篩選條件。
從動作清單中選擇此操作：**使用路徑獲取檔內容**

1. 如果尚未授權 Azure 邏輯應用訪問 Dropbox，則立即授權訪問。

1. 要流覽到要使用的檔路徑，請選擇省略號 （**...）** 按鈕旁邊的檔**路徑**。 

   您還可以在 **"檔路徑**"框中按一下，並從動態內容清單中選擇 **"檔路徑**"，其值可從上一節中添加的觸發器的輸出中獲取。

1. 完成後，儲存邏輯應用程式。

1. 要觸發邏輯應用，請在 Dropbox 中創建新檔。

## <a name="connector-reference"></a>連接器參考

如需連接器的 Swagger 檔案所敘述的技術詳細資料 (例如，觸發程序、動作和限制)，請參閱[連接器的參考頁面](/connectors/dropbox/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
