---
title: 開始使用 Azure Data Lake Storage Gen1-PowerShell |Microsoft Docs
description: 使用 Azure PowerShell 建立 Azure Data Lake Storage Gen1 帳戶，並執行基本作業。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: ff12f8857a7977226f836ec0418f74fb29e02906
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075114"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>使用 Azure PowerShell 開始使用 Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [入口網站](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

瞭解如何使用 Azure PowerShell 建立 Azure Data Lake Storage Gen1 帳戶，並執行基本作業，例如建立資料夾、上傳和下載資料檔案、刪除您的帳戶等等。如需 Data Lake Storage Gen1 的詳細資訊，請參閱[Data Lake Storage Gen1 的總覽](data-lake-store-overview.md)。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更新版本**。 請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/)。

## <a name="authentication"></a>驗證

本文使用較簡單的驗證方法搭配 Data Lake Storage Gen1，系統會提示您輸入 Azure 帳號憑證。 Data Lake Storage Gen1 帳戶和檔案系統的存取層級則由已登入使用者的存取層級所控管。 不過，還有其他方法可向 Data Lake Storage Gen1 進行驗證，也就是使用者驗證或服務對服務驗證。 如需有關如何驗證的指示和詳細資訊，請參閱[使用者驗證](data-lake-store-end-user-authenticate-using-active-directory.md)或[服務對服務驗證](data-lake-store-authenticate-using-active-directory.md)。

## <a name="create-a-data-lake-storage-gen1-account"></a>建立 Data Lake Storage Gen1 帳戶

1. 從桌面開啟新的 Windows PowerShell 視窗。 輸入下列程式碼片段登入 Azure 帳戶、設定訂用帳戶，然後註冊 Data Lake Storage Gen1 提供者。 系統提示您登入時，請務必使用其中一個訂用帳戶管理員/擁有者身分登入：

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Data Lake Storage Gen1 帳戶與 Azure 資源群組建立關聯。 從建立資源群組著手。

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![建立 Azure 資源群組](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "建立 Azure 資源群組")

1. 建立 Data Lake Storage Gen1 帳戶。 您指定的名稱必須只包含小寫字母和數字。

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![建立 Data Lake Storage Gen1 帳戶](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "建立 Data Lake Storage Gen1 帳戶")

1. 確認已成功建立帳戶。

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    此 Cmdlet 的輸出應為 **True**。

## <a name="create-directory-structures"></a>建立目錄結構

您可以在您的 Data Lake Storage Gen1 帳戶下建立用於管理與儲存資料的目錄。

1. 指定根目錄。

    ```PowerShell
    $myrootdir = "/"
    ```

1. 在指定的根目錄下建立名為 **mynewdirectory** 的新目錄。

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. 確認已成功建立新目錄。

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    輸出應如下列螢幕擷取畫面所示：

    ![確認目錄](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "確認目錄")

## <a name="upload-data"></a>上傳資料

您可以在根層級直接將資料上傳至 Data Lake Storage Gen1，或上傳至您在帳戶內建立的目錄。 本節的程式碼範例說明如何將一些範例資料上傳至您在上一節中建立的目錄 (**mynewdirectory**)。

如果您正在尋找一些可上傳的範例資料，您可以從 **Azure Data Lake Git 存放庫** 取得 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)資料夾。 下載檔案並將它儲存在電腦的本機目錄上，例如 C:\sampledata\。

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>重新命名、下載和刪除資料

若要重新命名檔案，請使用下列命令：

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

若要下載檔案，請使用下列命令：

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

若要刪除檔案，請使用下列命令：

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

出現提示時，請輸入 **Y** 刪除項目。 如果您要刪除多個檔案，可以提供所有的路徑並以逗號分隔。

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>建立您的帳戶

使用下列命令刪除您的 Data Lake Storage Gen1 帳戶。

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

出現提示時，請輸入 **Y** 刪除帳戶。

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 搭配 Azure Data Lake Storage Gen1 的效能微調指導方針](data-lake-store-performance-tuning-powershell.md)
* [使用 Azure Data Lake Storage Gen1 處理巨量資料需求](data-lake-store-data-scenarios.md)
* [保護 Data Lake Storage Gen1 中的資料](data-lake-store-secure-data.md)
* [搭配 Data Lake Storage Gen1 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配 Data Lake Storage Gen1 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
