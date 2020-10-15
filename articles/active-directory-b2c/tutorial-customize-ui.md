---
title: 教學課程：自訂使用者介面
titleSuffix: Azure AD B2C
description: 請遵循本教學課程以了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中自訂應用程式的使用者介面 (UI)。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9ca6d91be95bfb1a47f85b20f3775a57518ffcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922127"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中自訂使用者介面體驗

針對較常見的使用者體驗，例如註冊、登入和設定檔編輯等，您可以使用 Azure Active Directory B2C (Azure AD B2C) 中的[使用者流程](user-flow-overview.md)。 本教學課程中的資訊可協助您了解如何使用自己的 HTML 和 CSS 檔案，為這些體驗[自訂使用者介面 (UI)](customize-ui-overview.md)。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 建立 UI 自訂檔案
> * 更新使用者流程以使用檔案
> * 測試自訂的 UI

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

[建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。

## <a name="create-customization-files"></a>建立自訂檔案

您將建立 Azure 儲存體帳戶和容器，然後將基本的 HTML 和 CSS 檔案放入容器中。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

雖然您可以用很多方式來儲存檔案，但在此教學課程中，您會將這些檔案儲存在 [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 請確定您使用的目錄中有您的 Azure 訂用帳戶。 在上方功能表中選取 [目錄+訂用帳戶篩選]，然後選擇包含您訂用帳戶的目錄。 此目錄不同於包含您 Azure B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [儲存體帳戶]。
4. 選取 [新增]。
5. 在 [資源群組] 下，選取 [新建]，然後輸入新資源群組的名稱並按一下 [確認]。
6. 輸入儲存體帳戶的名稱。 您選擇的名稱在 Azure 中必須是唯一的、必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。
7. 選取儲存體帳戶的位置，或接受預設位置。
8. 接受所有其他預設值，然後選取 [檢閱 + 建立]，並按一下 [建立]。
9. 建立儲存體帳戶之後，請選取 [移至資源]。

### <a name="create-a-container"></a>建立容器

1. 在儲存體帳戶的 [概觀] 頁面中，選取 [Blob]。
2. 選取 [容器]，輸入容器的名稱，然後選擇 [Blob (僅限 Blob 的匿名讀取存取)\]，並按一下 [確定]。

### <a name="enable-cors"></a>啟用 CORS

 瀏覽器中的 Azure AD B2C 程式碼會使用最新且標準的方法，從使用者流程中的指定 URL 載入自訂內容。 跨來源資源共用 (CORS) 可允許從其他網域要求網頁上受限制的資源。

1. 在功能表中，選取 [CORS]。
2. 針對 [允許的來源]，輸入 `https://your-tenant-name.b2clogin.com`。 將 `your-tenant-name` 取代為您的 Azure AD B2C 租用戶名稱。 例如： `https://fabrikam.b2clogin.com` 。 輸入您的租用戶名稱時，必須全部使用小寫字母。
3. 對於**允許的方法**，請選取 `GET`,`PUT` 和 `OPTIONS`。
4. 針對 [允許的標頭]，輸入星號 (*)。
5. 針對 [公開的標頭]，輸入星號 (*)。
6. 針對 [最大壽命]，輸入 200。

    ![Azure 入口網站中 Azure Blob 儲存體的 CORS 設定頁面](./media/tutorial-customize-ui/enable-cors.png)

5. 按一下 [檔案] 。

### <a name="create-the-customization-files"></a>建立自訂檔案

若要自訂註冊體驗的 UI，您可以從建立簡單的 HTML 和 CSS 檔案開始。 您可以透過任何方式設定 HTML，但其中必須有識別碼為 `api` 的 **div** 元素。 例如： `<div id="api"></div>` 。 當頁面顯示時，Azure AD B2C 會將元素插入 `api` 容器。

1. 在本機資料夾中建立下列檔案，並確定您已將 `your-storage-account` 變更為儲存體帳戶名稱，以及將 `your-container` 變更為您建立的容器名稱。 例如： `https://store1.blob.core.windows.net/b2c/style.css` 。

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    您可以透過任何方式設計頁面，但您建立的任何 HTML 自訂檔案都必須有 **api** div 元素。

3. 將檔案儲存為 custom-ui.html。
4. 建立下列簡單的 CSS，以將所有項目 (包括 Azure AD B2C 插入的項目) 放到註冊或登入頁面的中間。

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. 將檔案儲存為 style.css。

### <a name="upload-the-customization-files"></a>上傳自訂檔案

在本教學課程中，您會將建立的檔案儲存在儲存體帳戶中，讓 Azure AD B2C 可以存取這些檔案。

1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [儲存體帳戶]。
2. 選取您建立的儲存體帳戶、選取 [Blob]，然後選取您建立的容器。
3. 選取 [上傳]，瀏覽至 [custom-ui.html] 檔案並加以選取，然後再按一下 [上傳]。

    ![在入口網站的上傳 Blob 頁面中反白顯示的上傳按鈕和檔案](./media/tutorial-customize-ui/upload-blob.png)

4. 複製已上傳檔案的 URL，以便稍後在教學課程中使用。
5. 對 style.css 檔案重複步驟 3 和 4。

## <a name="update-the-user-flow"></a>更新使用者流程

1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
2. 選取 [使用者流程]，然後選取 *B2C_1_signupsignin1* 使用者流程。
3. 選取 [頁面配置]，然後針對 [統一註冊或登入頁面] 底下的 [使用自訂頁面內容] 按一下 [是]。
4. 在 [自訂頁面 URI] 中，輸入您先前記下的 custom-ui.html 檔案 URI。
5. 在頁面上方選取 [儲存]。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]，然後選取 B2C_1_signupsignin1 使用者流程。
2. 按一下頁面頂端的 [執行使用者流程]。
3. 按一下 [執行使用者流程]  按鈕。

    ![執行使用者流程頁面以註冊或登入使用者流程](./media/tutorial-customize-ui/run-user-flow.png)

    您應該會看到類似下列範例的頁面，而頁面上的置中元素會以您所建立的 CSS 檔案為基礎：

    ![網頁瀏覽器顯示具有自訂 UI 元素的註冊或登入頁面](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立 UI 自訂檔案
> * 更新使用者流程以使用檔案
> * 測試自訂的 UI

> [!div class="nextstepaction"]
> [設定 Azure Active Directory B2C 中的 UI](customize-ui-overview.md)
