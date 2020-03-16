---
title: 教學課程：將靜態網站裝載於 Blob 儲存體上 - Azure 儲存體
description: 了解如何設定用於靜態網站代管的儲存體帳戶，以及將靜態網站部署至「Azure 儲存體」。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 03850315a05f569d2c6ba9405b6ec38bb6b1305d
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330390"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>教學課程：將靜態網站裝載於 Blob 儲存體上

在本教學課程中，您將了解如何建置靜態網站並將其部署至 Azure 儲存體。 完成時，您將擁有一個可供使用者公開存取的靜態網站。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定靜態網站代管
> * 部署 Hello World 網站

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> 務必建立一般用途的 v2 標準儲存體帳戶。 靜態網站無法在任何其他類型的儲存體帳戶中使用。

本教學課程使用 [Visual Studio Code](https://code.visualstudio.com/download)，這是一個免費的程式設計人員工具，可建置靜態網站並將其部署至「Azure 儲存體」帳戶。

安裝 Visual Studio Code 之後，請安裝「Azure 儲存體」預覽版延伸模組。 此延伸模組會將「Azure 儲存體」管理功能與 Visual Studio Code 整合。 您將會使用此延伸模組將靜態網站部署至「Azure 儲存體」。 安裝延伸模組：

1. 啟動 Visual Studio Code。
2. 在工具列上，按一下 [延伸模組]  。 搜尋「Azure 儲存體」  ，然後從清單中選取 [Azure 儲存體]  延伸模組。 接著，按一下 [下載]  按鈕以安裝延伸模組。

    ![在 VS Code 中安裝 Azure 儲存體延伸模組](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)以開始進行操作。

## <a name="configure-static-website-hosting"></a>設定靜態網站代管

第一步是設定您的儲存體帳戶，以將靜態網站裝載於 Azure 入口網站中。 當您設定帳戶來進行靜態網站代管時，「Azure 儲存體」會自動建立名為 *$web* 的容器。 *$web* 容器將包含靜態網站的檔案。 

1. 在網頁瀏覽器中開啟 [Azure 入口網站](https://portal.azure.com/)。 
1. 找出您的儲存體帳戶，然後顯示帳戶概觀。
1. 選取 [靜態網站]  以顯示靜態網站的設定頁面。
1. 選取 [已啟用]  以啟用儲存體帳戶的靜態網站代管功能。
1. 在 [索引文件名稱]  欄位中，指定預設的索引頁面 *index.html*。 當使用者瀏覽至您靜態網站的根目錄時，就會顯示此預設索引頁面。  
1. 在 [文件路徑發生錯誤]  欄位中，指定預設的錯誤頁面 *404.html*。 當使用者嘗試瀏覽至您靜態網站中所沒有的網頁時，就會顯示此預設錯誤頁面。
1. 按一下 [檔案]  。 Azure 入口網站現在即會顯示您的靜態網站端點。 

    ![啟用儲存體帳戶的靜態網站代管功能](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>部署 Hello World 網站

接下來，使用 Visual Studio Code 來建立 Hello World 網頁，然後將其部署至裝載在您「Azure 儲存體」帳戶中的靜態網站。

1. 在您的本機檔案系統上建立一個名為 *mywebsite* 的空資料夾。 
1. 啟動 Visual Studio Code，然後從 [總管]  面板開啟您剛才建立的資料夾。

    ![在 Visual Studio Code 中開啟資料夾](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. 在 [mywebsite]  資料夾中建立預設索引檔案，然後將其命名為 *index.html*。

    ![在 Visual Studio Code 中建立預設索引檔案](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. 在編輯器中開啟 *index.html*，將下列文字貼到檔案中，然後儲存檔案：

    ```
    <h1>Hello World!</h1>
    ```

1. 建立預設錯誤檔案，然後將其命名為 *404.html*。
1. 在編輯器中開啟 *404.html*，然後將下列文字貼到檔案中，並加以儲存：

    ```
    <h1>404</h1>
    ```

1. 在 [總管]  面板中的 [mywebsite]  資料夾底下按一下滑鼠右鍵，然後選取 [部署至靜態網站]  以部署您的網站。 系統將提示您登入 Azure 以擷取訂用帳戶清單。

1. 選取包含已啟用靜態網站代管功能之儲存體帳戶的訂用帳戶。 接著，在出現提示時，選取該儲存體帳戶。

Visual Studio Code 現在會將您的檔案上傳到 Web 端點，然後顯示成功狀態列。 啟動網站以在 Azure 中檢視網站。

您已順利完成本教學課程並將靜態網站部署至 Azure。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何設定用於靜態網站裝載的 Azure 儲存體帳戶，以及如何建立靜態網站並將其部署至 Azure 端點。

接下來，請了解如何設定靜態網站的自訂網域。

> [!div class="nextstepaction"]
> [將自訂網域對應至 Azure Blob 儲存體端點](storage-custom-domain-name.md)
