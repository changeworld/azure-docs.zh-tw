---
title: 教學課程：註冊 Azure 資料目錄中的資料資產
description: 本教學課程說明如何在 Azure 資料目錄中註冊資料資產。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: 66bacdf27b2ef9cf624b645cb6e4c123d9c3fa2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88135873"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>教學課程：註冊 Azure 資料目錄中的資料資產

在本教學課程中，您可使用註冊工具，向目錄註冊資料庫範例中的資料資產。 註冊的過程會從資料來源及其包含的資產中，擷取重要的結構化中繼資料 (例如名稱、類型和位置)，並將該中繼資料複製到目錄。 資料來源及資料資產會留在原地，但目錄會利用中繼資料，讓您更輕鬆探索和了解資料來源及其資料。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 註冊資料資產 
> * 搜尋資料資產
> * 註解資料資產
> * 連線到資料資產
> * 管理資料資產
> * 刪除資料資產

## <a name="prerequisites"></a>必要條件

若要開始著手，您必須完成[快速入門](register-data-assets-tutorial.md)。

* [Microsoft Azure](https://azure.microsoft.com/) 訂用帳戶。
* 您需要有自己的 [Azure Active Directory 租用戶](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

若要設定資料目錄，您必須是 Azure 訂用帳戶的擁有者或共同擁有者。

## <a name="register-data-assets"></a>註冊資料資產

### <a name="register-a-data-source"></a>註冊資料來源

您會註冊 Azure SQL Database [資料庫範例](../azure-sql/database/single-database-create-quickstart.md)中的資料資產 (資料表)，但如果您偏好使用熟悉且與您的角色相關的資料，也可以使用任何支援的資料來源。 如需支援的資料來源清單，請參閱 [支援的資料來源](data-catalog-dsr.md)。

我們在本教學課程使用的資料庫名稱為 *RLSTest*。

您現在可以使用 Azure 資料目錄，註冊資料庫範例中的資料資產。

1. 移至 [Azure 資料目錄首頁](http://azuredatacatalog.com)，然後選取 [發佈資料]。

   ![Azure 資料目錄--發佈資料按鈕](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. 選取 [啟動應用程式] 以在電腦上下載、安裝及執行註冊工具。

   ![Azure 資料目錄--啟動按鈕](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. 在 [歡迎] 頁面上，選取 [登入] 並輸入您的認證。

    ![Azure 資料目錄--歡迎使用頁面](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. 在 [Microsoft Azure 資料目錄] 頁面上，選取 [SQL Server] 和 [下一步]。

    ![Azure 資料目錄--資料來源](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. 輸入 Azure SQL Database 中資料庫範例的 SQL Server 連線屬性，然後選取 [連線]。

   ![Azure 資料目錄--SQL Server 連線設定](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. 為您的資料資產註冊中繼資料。 在此範例中，您將註冊範例命名空間中的 **Product** 物件：

   1. 在 [伺服器階層] 樹狀目錄中，展開資料庫範例，然後選取 [SalesLT]。

   2. 使用 CTRL 鍵並選取 [Product]、[ProductCategory]、[ProductDescription] 和 [ProductModel]。

   3. 選取**移動選取的箭頭** ( **>** )。 此動作會將所有選取的物件移至 [準備註冊的物件]  清單。

      ![Azure 資料目錄教學課程--瀏覽並選取物件](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

   4. 選取 [包含預覽]  以包含資料的快照預覽。 快照中會包含最多 20 筆來自各個資料表的記錄，並且會複製到目錄。

   5. 選取 [包含資料設定檔]  以包含資料設定檔的物件統計資料快照 (例如︰資料行的最小值、最大值和平均值以及資料列數目)。

   6. 在 [新增標記] 欄位中輸入 **sales, product, azure sql**。 此動作會新增這些資料資產的搜尋標籤。 標記可協助使用者尋找已註冊的資料來源，非常有用。

   7. 指定此資料之 **專家** 的名稱 (選擇性)。

      ![Azure 資料目錄教學課程--要註冊的物件](media/register-data-assets-tutorial/data-catalog-objects-register.png)

   8. 選取 [註冊]。 Azure 資料目錄會註冊您選取的物件。 本練習中會註冊從您的資料庫範例中選取的物件。 註冊工具會從資料資產擷取中繼資料，並將該資料複製到 Azure 資料目錄服務。 資料會保留在它目前的位置。 資料仍然在原始系統的系統管理員及原則的控制之下。

      ![Azure 資料目錄--已註冊的物件](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. 若要查看您註冊的資料來源物件，請選取 [檢視入口網站]。 在 Azure 資料目錄入口網站中，確認您在方格檢視中看到全部四個資料表和資料庫 (確認搜尋列是空的)。

       ![Azure 資料目錄入口網站中的物件](media/register-data-assets-tutorial/data-catalog-view-portal.png)

在本練習中，您已註冊 Azure SQL Database 資料庫範例中的物件，讓整個組織裡的使用者可以輕鬆加以探索。

在下一個練習中，您將學習如何探索已註冊的資料資產。

## <a name="discover-data-assets"></a>探索資料資產

在 Azure 資料目錄探索資料會使用兩種主要機制：搜尋和篩選。

搜尋的設計兼具直覺與強大的功能。 根據預設，搜尋字詞會比對目錄中的所有內容，包括使用者提供的註解。

篩選的設計則與搜尋互補。 您可以選取特定特性，例如專家、資料來源類型、物件類型和標記，來檢視相符的資料資產並將搜尋結果限制為相符的資產。

透過結合使用搜尋和篩選，您可以快速瀏覽已向 Azure 資料目錄註冊的資料來源。

在本練習中，您會使用 Azure 資料目錄入口網站，探索您在上一個練習中註冊的資料資產。 如需搜尋語法的詳細資料，請參閱 [資料目錄搜尋語法參考](/rest/api/datacatalog/#search-syntax-reference) 。

以下是幾個探索目錄中資料資產的範例。  

### <a name="discover-data-assets-with-basic-search"></a>利用基本搜尋探索資料資產

基本搜尋可協助您使用一或多個搜尋字詞搜尋目錄。 結果包含了任何與一或多個指定字詞的內容相符的資產。

1. 選取 Azure 資料目錄入口網站中的 [首頁]。 如果您已關閉網頁瀏覽器，請移至 [Azure 資料目錄首頁](https://www.azuredatacatalog.com)。

2. 在搜尋方塊中輸入 `product` ，然後按 **ENTER**鍵。

   ![Azure 資料目錄--基本文字搜尋](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. 確認您在結果中看到全部四個資料表和資料庫。 您可以選取工具列上的按鈕，在**資料格檢視**與**清單檢視**之間切換，如下圖所示。 請注意，[醒目提示] 選項為 [開啟] 狀態，因此搜尋結果中會醒目提示搜尋關鍵字。 您也可以指定搜尋結果的 [每頁顯示的結果]  數目。

   ![Azure 資料目錄--基本文字搜尋結果](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

   [搜尋] 面板位於左邊，而 [屬性] 面板位於右邊。 在 [搜尋]  面板上，您可以變更搜尋條件和篩選結果。 [屬性]  面板會顯示資料格或清單中所選物件的屬性。

4. 選取搜尋結果中的 [Product]。 選取 [預覽]、[資料行]、[資料設定檔] 和 [文件] 索引標籤，或選取箭號以展開底部窗格。  

   ![Azure 資料目錄--底部窗格](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

   在 [預覽] 索引標籤上，您會看到 **Product** 資料表資料的預覽。

5. 選取 [資料行] 索引標籤，可尋找有關資料資產資料行的詳細資料 (例如**名稱**和**資料類型**)。

6. 選取 [資料設定檔]  索引標籤，可查看資料資產中資料的分析 (例如︰資料列數目、資料大小或資料行中的最小值)。

### <a name="discover-data-assets-with-property-scoping"></a>利用屬性範圍探索資料資產

屬性範圍可協助您探索搜尋字詞符合指定屬性的資料資產。

1. 清除 [篩選]中 [物件類型] 底下的 [資料表] 篩選。  

2. 在搜尋方塊中輸入 `tags:product` ，然後按 **ENTER**鍵。 請參閱 [資料目錄搜尋語法參考](/rest/api/datacatalog/#search-syntax-reference) ，以取得用來搜尋資料目錄的所有屬性。

3. 確認您在結果中看到資料表和資料庫。  

   ![資料目錄--屬性範圍搜尋結果](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>儲存搜尋

1. 在 [目前搜尋] 區段的 [搜尋] 窗格中，輸入搜尋的名稱並選取 [儲存]。

   ![Azure 資料目錄--儲存搜尋](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. 確認已儲存的搜尋顯示在 [已儲存的搜尋] 底下。

3. 選取您可以對已儲存的搜尋採取的動作 ([重新命名]、[刪除]、[設定為預設值] 搜尋)。

### <a name="grouping-with-parentheses"></a>使用括號分組

使用括號進行分組，您即可將一部分的查詢分組以達到邏輯隔離 (尤其是搭配布林運算子)。

1. 在搜尋方塊中輸入 `name:product AND (tags:product AND objectType:table)` ，然後按 **ENTER**鍵。

2. 確認您只在搜尋結果中看到 **Product** 資料表。

   ![Azure 資料目錄--群組搜尋](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>比較運算子

使用比較運算子，您可以針對具有數值和日期資料類型的屬性使用比較而非相等。

1. 在搜尋方塊中，輸入 `lastRegisteredTime:>"06/09/2016"`。

2. 清除 [物件類型] 底下的 [資料表] 篩選。

3. 按 ENTER 鍵。

4. 確認您在搜尋結果中看到已註冊的 **Product**、**ProductCategory** 和 **ProductDescription** 資料表以及 SQL 資料庫。

   ![Azure 資料目錄--比較搜尋結果](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

如需關於探索資料資產的詳細資訊，請參閱[如何探索資料資產](data-catalog-how-to-discover.md)。 如需搜尋語法的詳細資訊，請參閱[資料目錄搜尋語法參考](/rest/api/datacatalog/#search-syntax-reference)。

## <a name="annotate-data-assets"></a>註解資料資產

在本練習中，您將使用 Azure 資料目錄入口網站，標註目錄中現有的資料資產 (新增描述、標記或專家等資訊)。 註釋可補充在註冊期間擷取自資料來源的結構化中繼資料。 註釋可讓您更輕鬆地探索及了解資料資產。

在此練習中，您會註解單一資料資產 (ProductPhoto)。 您會對 ProductPhoto 資料資產新增易記名稱和描述。  

1. 移至 [Azure 資料目錄首頁](https://www.azuredatacatalog.com)，並使用 `tags:product` 進行搜尋，以尋找您已註冊的資料資產。

2. 選取搜尋結果中的 [ProductModel]。  

3. 在 [易記名稱] 中輸入 **Product images**，並在 [描述] 中輸入 **Product photos for marketing materials**。

    ![Azure 資料目錄--產品圖片描述](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    [描述]  可幫助其他人探索並了解為何及如何使用選取的資料資產。 您也可以新增更多的標記，並檢視資料行。 您可以使用已新增至目錄的描述性中繼資料，來搜尋和篩選資料來源。

您也可以在此頁面上執行下列步驟︰

* 新增資料資產的專家。 選取 [專家] 區域中的 [新增]。

* 新增資料集層級的標記。 選取 [標記] 區域中的 [新增]。 標記可以是使用者標記或詞彙標記。 標準版的資料目錄包含有助於目錄管理員定義中央商務分類的商務詞彙。 目錄使用者接著可以為資料資產加上詞彙註解。 如需詳細資訊，請參閱 [如何設定控管標籤的商務詞彙](data-catalog-how-to-business-glossary.md)

* 新增資料行層級的標記。 針對您想要標註的資料行，選取 [標記] 下的 [新增]。

* 新增資料行層級的描述。 輸入資料行的 [描述]  。 您也可以檢視擷取自資料來源的描述中繼資料。

* 新增 [要求存取]  資訊，以對使用者顯示如何要求資料資產的存取權。
  
* 選擇 [文件]  索引標籤並提供資料資產的文件。 透過 Azure 資料目錄文件，您可以使用資料目錄做為內容儲存機制，以建立完整的資料資產敘述。
  
您也可以對多個資料資產新增一個註解。 例如，您可以選取您已註冊的所有資料資產，並指定這些資產的專家。

![Azure 資料目錄--註解多個資料資產](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure 資料目錄支援支援群眾外包 (crowd-sourcing) 的註解作法。 Azure 資料目錄使用者可以新增標記 (使用者或詞彙)、描述和其他中繼資料。 如此一來，使用者可新增對於資料資產及其用途的觀點，並與其他使用者共用觀點。

如需關於註解資料資產的詳細資訊，請參閱 [如何註解資料資產](data-catalog-how-to-annotate.md) 。

## <a name="connect-to-data-assets"></a>連線到資料資產

在本練習中，您會使用連線資訊，在整合式用戶端工具 (Excel) 和非整合式工具 (SQL Server Management Studio) 中開啟資料資產。

> [!NOTE]
> 請務必記得，Azure 資料目錄不會讓您存取實際的資料來源，它只是讓您更容易探索和了解資料來源。 當您連接到資料來源時，所選擇的用戶端應用程式會使用您的 Windows 認證，或在必要時提示您提供認證。 如果先前未授權您存取資料來源，您必須先獲得授權才能連線。

### <a name="connect-to-a-data-asset-from-excel"></a>從 Excel 連線到資料資產

1. 選取搜尋結果中的 **Product** 。 選取工具列上的 [開啟於]，然後選取 [Excel]。

   ![Azure 資料目錄--連線到資料資產](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. 選取下載快顯視窗中的 [開啟]。 這種經驗會視瀏覽器而有所不同。

3. 在 [Microsoft Excel 安全性注意事項] 視窗中，選取 [啟用]。

   ![Azure 資料目錄--Excel 安全性快顯視窗](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. 保留 [匯入資料] 對話方塊中的預設值，然後選取 [確定]。

   ![Azure 資料目錄--Excel 匯入資料](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. 在 Excel 中檢視資料來源。

   ![Azure 資料目錄--Excel 中的產品資料表](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

在本練習中，您連接到使用 Azure 資料目錄找到的資料資產。 在 Azure 資料目錄入口網站中，您可以使用整合到 [開啟於]  功能表中的用戶端應用程式來直接連接。 您也可以使用資產中繼資料中包含的連接位置資訊，與您所選的任何應用程式連接。 例如︰您可以使用 SQL Server Management Studio 連線到 Azure SQL Database，以存取本教學課程中註冊的資料資產中的資料。

1. 開啟 [SQL Server Management Studio]。

2. 在 [連線到伺服器] 對話方塊中，輸入 Azure 資料目錄入口網站 [屬性] 窗格中的伺服器名稱。

3. 使用適當的驗證和認證來存取資料資產。 如果您沒有存取權，使用 [要求存取]  欄位中的資訊來取得它。

   ![Azure 資料目錄--要求存取](media/register-data-assets-tutorial/data-catalog-request-access.png)

選取 [檢視連接字串] 來檢視 ADF.NET、ODBC 和 OLEDB 連接字串，並將這些字串複製到剪貼簿以在應用程式中使用。

## <a name="manage-data-assets"></a>管理資料資產

在此步驟中，您會了解如何設定資料資產的安全性。 資料目錄並不會讓使用者存取資料本身。 資料來源的擁有者會控制資料存取權。

您可以使用資料目錄探索資料來源，以及檢視與目錄中註冊的來源相關的中繼資料。 不過，有時候只有特定使用者或特定群組的成員才能看到資料來源。 若是這樣的情況，您可以使用資料目錄來取得已註冊資料資產的擁有權，以及控制您擁有之資產的可見性。

> [!NOTE]
> 本練習所述的管理功能只在標準版 Azure 資料目錄中提供，免費版本沒有提供。
> 在 Azure 資料目錄中，您可以取得資料資產的擁有權、新增資料資產的共同擁有者，以及設定資料資產的可見性。

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>取得資料資產的擁有權及限制可見性

1. 移至 [Azure 資料目錄首頁](https://www.azuredatacatalog.com)。 在 [搜尋] 文字方塊中輸入 `tags:cycles`，然後按 **ENTER** 鍵。

2. 選取結果清單中的項目，然後選取工具列上的 [取得擁有權]。

3. 在 [屬性] 面板的 [管理] 區段中，選取 [取得擁有權]。

    ![Azure 資料目錄--取得擁有權](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. 若要限制可見性，請選擇 [可見性] 區段的 [擁有者與這些使用者]，然後選取 [新增]。 在文字方塊中輸入使用者的電子郵件地址，然後按 **ENTER** 鍵。

    ![Azure 資料目錄--限制存取](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>移除資料資產

在本練習中，您會使用 Azure 資料目錄入口網站，從已註冊的資料資產中移除預覽資料，並從目錄中刪除資料資產。

在 Azure 資料目錄中，您可以刪除個別資產或多個資產。

1. 移至 [Azure 資料目錄首頁](https://www.azuredatacatalog.com)。

2. 在 [搜尋] 文字方塊中輸入 `tags:cycles`，然後選取 **ENTER** 鍵。

3. 選取結果清單中的項目，然後選取工具列上的 [刪除]，如下圖所示：

   ![Azure 資料目錄--刪除資料格項目](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

   如果您使用清單檢視，此核取方塊位於項目的左邊，如下圖所示：

   ![Azure 資料目錄--刪除清單項目](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

   您也可以選取多個資料資產來加以刪除，如下圖所示︰

   ![Azure 資料目錄--刪除多個資料資產](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> 目錄的預設行為是允許任何使用者註冊任何資料來源，並允許任何使用者刪除任何已註冊的資料資產。 標準版 Azure 資料目錄中包含的管理功能提供其他選項，可用來取得資產的所有權、限制誰可以探索資產，以及限制誰可以刪除資產。

## <a name="clean-up-resources"></a>清除資源

請遵循[移除資料資產](#remove-data-assets)的步驟，來清除您在遵循本教學課程時可能使用的任何資產。

## <a name="summary"></a>摘要

在本教學課程中，您已瀏覽 Azure 資料目錄的基本功能，包括註冊、註解、探索和管理企業資料資產。 既然您已經完成本教學課程，現在可以開始使用。 您可以立即開始註冊您和小組所依賴的資料來源，並邀請同事使用目錄。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [支援的資料來源](data-catalog-dsr.md)
