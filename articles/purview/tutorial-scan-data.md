---
title: 教學課程：使用 Azure Purview (預覽) 掃描資料
description: 在本教學課程中，您會執行入門套件以設定資料資產，然後將資料來源中的資料掃描到您的 Azure Purview 目錄中。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: eafa2187308c0324b85596ce25e8310fd8506a97
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249597"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>教學課程：使用 Azure Purview (預覽) 掃描資料

> [!IMPORTANT]
> Azure Purview 目前為預覽狀態。 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含適用於 Azure 功能 (搶鮮版 (Beta)、預覽版，或尚未正式發行的版本) 的其他法律條款。

在這個「五部分的教學課程系列」中，您將大致了解如何使用 Azure Purview (預覽) 來管理資料資產中的資料控管。 您在教學課程的這個部分建立的資料資產，會用於系列中的其他教學課程。

在本教學課程系列的第 1 部分中，您將會：

> [!div class="checklist"]
>
> * 使用各種 Azure 資料資源建立資料資產。
> * 將資料掃描到目錄中。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Azure Purview 帳戶](create-catalog-portal.md)。
* 將部署資料資產的[入門套件](https://download.microsoft.com/download/9/7/9/979db3b1-0916-4997-a7fb-24e3d8f83174/PurviewStarterKitV4.zip)。

> [!NOTE]
> 此入門套件僅適用於 Windows。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-data-estate"></a>建立資料資產

在本節中，您會執行入門套件指令碼以建立模擬的資料資產。 資料資產是公司擁有之所有資料的組合。 此入門套件指令碼會執行下列動作：

* 建立 Azure Blob 儲存體帳戶，並將資料填入該帳戶。
* 建立 Azure Data Lake Storage Gen2 帳戶。
* 建立 Azure Data Factory 執行個體，並將執行個體關聯至 Azure Purview。
* 設定並觸發 Azure Blob 儲存體與 Azure Data Lake Storage Gen2 帳戶之間的複製活動管線。
* 將相關聯的譜系從 Azure Data Factory 推送至 Azure Purview。

### <a name="prepare-to-run-the-starter-kit"></a>準備執行入門套件

請依照下列步驟，在您的 Windows 機器上設定入門套件用戶端軟體：

1. [下載入門套件](https://download.microsoft.com/download/9/7/9/979db3b1-0916-4997-a7fb-24e3d8f83174/PurviewStarterKitV4.zip)，並將其內容解壓縮至您選擇的位置。


1. 在您的電腦上，於 Windows 工作列的搜尋方塊中輸入 **PowerShell**。 在搜尋清單中﹐以滑鼠右鍵按一下 [Windows PowerShell]，然後選取 [以系統管理員身分執行]。

1. 在 PowerShell 視窗中輸入下列命令，並將 `<path-to-starter-kit>` 取代為解壓縮入門套件檔案的資料夾路徑。

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. 輸入下列命令以安裝 Azure Cmdlet。

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. 如果您看到警告提示：*需要 NuGet 提供者才能繼續*，請輸入 **Y**，然後按 Enter 鍵。

1. 如果您看到警告提示：*不受信任的存放庫*，請輸入 **A**，然後按 Enter 鍵。

PowerShell 最多可能需要一分鐘才能將必要的模組安裝完成。

### <a name="collect-data-needed-to-run-the-scripts"></a>收集執行指令碼所需的資料

執行 PowerShell 指令碼以啟動目錄之前，請先取得下列引數的值以在指令碼中使用：

* TenantID
   1. 在 [Azure 入口網站](https://portal.azure.com) 中，選取 [Azure Active Directory]。
   1. 在左側瀏覽窗格的 [管理] 區段中，選取 [屬性]。 然後，選取 [租用戶識別碼] 的複製圖示，將值儲存至剪貼簿。 在文字編輯器中貼上值，以供後續使用。

* SubscriptionID
   1. 在 Azure 入口網站中，搜尋並選取您建立為必要條件的 Azure Purview 執行個體的名稱。
   1. 選取 [概觀] 區段，並儲存 GUID 作為 [訂用帳戶識別碼]。

   > [!NOTE]
   > 請確定您所使用的訂用帳戶與您建立 Azure Purview 帳戶時所使用的相同。 這是放在允許清單中的相同訂用帳戶。

* CatalogName：您在[建立 Azure Purview 帳戶](create-catalog-portal.md)中建立的 Azure Purview 帳戶名稱。

* CatalogResourceGroupName：您在其中建立 Azure Purview 帳戶的資源群組名稱。

### <a name="verify-permissions"></a>驗證權限

請依照下列步驟，將執行指令碼的使用者新增至建立為必要條件的 Azure Purview 帳戶。 使用者需同時具備 *Purview 資料編者* 和 *Purview 資料來源管理員* 角色。 

如果您自行建立 Azure Purview 帳戶，則會自動獲得存取權，而可以略過本節。

1. 移至 Azure 入口網站中的 [Purview 帳戶] 頁面，然後選取您要修改的 Azure Purview 帳戶。

1. 在帳戶的頁面上選取 [存取控制 (IAM)] 索引標籤，然後選取 [+ 新增]。

1. 選取 [新增角色指派]。

1. 針對 [角色]，輸入 **Purview 資料編者角色**。
 
1. 使用 [存取權指派對象] 的預設值。 預設值應為 [使用者、群組或服務主體]。

1. 在 [選取] 中，輸入執行指令碼的使用者名稱。

1. 選取 [儲存]。

1. 將 [角色] 設定為 [Purview 資料來源管理員角色]，並重複上述步驟。

如需詳細資訊，請參閱[目錄權限](catalog-permissions.md)。

### <a name="run-the-client-side-setup-scripts"></a>執行用戶端安裝指令碼

目錄設定完成後，請在 PowerShell 視窗中執行下列指令碼以建立資產，並將預留位置取代為您先前收集的值。

1. 使用下列命令瀏覽至入門套件目錄。 將 `path-to-starter-kit` 取代為解壓縮檔案的資料夾路徑。

   ```powershell
   cd <path-to-starter-kit>
   ```

1. 下列命令會設定本機電腦的執行原則。 在系統提示您變更執行原則時，請輸入 **A** 以指定 [全部皆是]。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. 使用下列命令連線至 Azure。 取代 `TenantID` 和 `SubscriptionID` 預留位置。

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   執行命令時可能會出現一個快顯視窗，讓您使用 Azure Active Directory 認證進行登入。


1. 使用下列命令執行入門套件。 取代 `CatalogName`、`TenantID`、`SubscriptionID`、`NewResourceGroupName` 和 `CatalogResourceGroupName` 預留位置。 針對 `NewResourceGroupName`，請使用將包含資料資產之資源群組的唯一名稱 (僅可使用小寫英數字元)。

   > [!IMPORTANT]
   > **newresourcegroupname** 僅使用數字和小寫字母，且不可超過 17 個字元。 **不允許使用大寫字母和特殊字元。** 此限制來自儲存體帳戶命名規則。

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

設定環境最多可能需要 10 分鐘的時間來。 在這段期間，可能會看到多個快顯視窗，但可加以忽略。 請勿關閉 **BlobDataCreator** 視窗；此視窗會在完成時自動關閉。

當您看到 `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b` 訊息時，請等候 **BlobDataCreator.exe** 的另一個執行個體開始並完成執行。

> [!IMPORTANT]
> 如果 [作用中工作數目] 停止減少，您可以結束 [Blob 建立者] 視窗，然後在 PowerShell 視窗上按 Enter 鍵

此程序完成後，就會以您提供的名稱建立資源群組。 Azure Data Factory、Azure Blob 儲存體和 Azure Data Lake Storage Gen2 帳戶都包含在此資源群組中。 資源群組會包含在您指定的訂用帳戶中。

## <a name="scan-data-into-the-catalog"></a>將資料掃描到目錄中

掃描是一個程序，其間，目錄會根據使用者指定的排程直接連線至資料來源。 目錄會透過掃描、譜系、入口網站和 API 反映公司的資料資產。 其目標包括檢查內容、擷取結構描述，以及嘗試了解語意。 在本節中，您會為先前使用入門套件產生的資料來源設定掃描。

您所執行的入門套件指令碼建立了兩個資料來源：Azure Blob 儲存體和 Azure Data Lake Storage Gen2。 您可以同時將這些資料來源掃描到目錄中。

### <a name="authenticate-to-your-storage-with-managed-identity"></a>使用受控識別向您的儲存體進行驗證

建立您的帳戶時，系統會自動建立與您的 Azure Purview 帳戶同名的受控識別。 您必須先提供儲存體帳戶的 Azure Purview 角色權限，才能掃描您的資料。

1. 瀏覽至入門套件所建立的資源群組，然後選取您的 Blob 儲存體帳戶。

1. 從左側導覽功能表中選取 [存取控制 (IAM)]。 然後選取 [+ 新增]。

1. 將 [角色] 設定為 [儲存體 Blob 資料讀者]，並針對 [選取] 輸入您的 Azure Purview 帳戶名稱。 然後，選取 [儲存]，將此角色指派提供給您的 Purview 帳戶。

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="新增角色指派":::

1. 針對 Azure Data Lake Storage Gen2 重複上述步驟。

### <a name="scan-your-data-sources"></a>掃描您的資料來源

1. 在 [Azure 入口網站](https://portal.azure.com) 中流覽至您的 Azure 範疇資源，然後選取 [ *開啟範疇 Studio*]。 您會自動進入 Purview Studio 的首頁。

1. 選取目錄網頁上的 [來源]，然後選取 [註冊]。 然後，選取 [Azure Blob 儲存體]，並選取 [繼續]。

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="註冊 Blob 儲存體資源":::

1. 在 [註冊來源] 頁面上，輸入 [名稱]。 選擇您先前使用入門套件建立的 Azure Blob 儲存體帳戶的 [儲存體帳戶名稱]。 帳戶的名稱為 `<YourResourceGroupName>adcblob`。 選取 [完成]。

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="此螢幕擷取畫面顯示用來註冊 Azure Blob 儲存體資料來源的設定。" border="true":::

1. 在 [資料來源] 對應檢視上，選取 [Azure Blob 儲存體] 圖格上的 [新增掃描]。

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="顯示如何從資料來源選取掃描設定的螢幕擷取畫面。" border="true":::

1. 在 [掃描] 頁面上輸入掃描名稱，從 [認證] 下拉式清單中選取您的受控識別，然後選取 [繼續]。

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="在 Azure Purview 中掃描 Blob 儲存體":::

1. 您可以將掃描範圍限定於個別的 Blob。 在本教學課程中，請保留所有資產的勾選狀態以掃描所有項目，然後選取 [繼續]。

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="界定儲存體的掃描範圍":::

1. 選取預設掃描規則集，然後選取 [繼續]。

1. 您可以設定週期性掃描的掃描觸發程序。 在本教學課程中請選取 [一次]，然後選取 [繼續]。

1. 選取 [儲存並執行] 以完成掃描。

1. 重複上述步驟以掃描您的 Azure Data Lake Storage Gen2 帳戶。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：
> [!div class="checklist"]
>
> * 執行入門套件指令碼以設定資料資產環境。
> * 將資料掃描到 Azure Purview 目錄中。

繼續進行下一個教學課程，以了解如何瀏覽首頁及搜尋資產。

> [!div class="nextstepaction"]
> [瀏覽首頁及搜尋資產](tutorial-asset-search.md)
