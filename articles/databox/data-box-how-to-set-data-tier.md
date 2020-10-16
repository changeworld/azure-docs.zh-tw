---
title: 透過 Azure 資料箱/Azure Data Box Heavy 將資料傳送至經常性存取、非經常性存取、封存 blob 層
description: 說明如何使用 Azure 資料箱或 Azure Data Box Heavy，將資料傳送至適當的區塊 blob 儲存層，例如經常性存取、非經常性存取或封存
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 80c4d8a70454c007ac45f588e59c03ef45f10933
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125059"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>使用 Azure 資料箱或 Azure Data Box Heavy 將資料傳送至適當的 Azure 儲存體 blob 層

Azure 資料箱會寄給您適當的存放裝置，以將大量資料移動至 Azure。 您會將資料移到該裝置內並寄回。 來自資料箱的資料會上傳到與儲存體帳戶相關聯的預設層。 然後您可以將資料移動到其他儲存層。

本文描述如何將藉由資料箱上傳的資料，移動到常性存取、非經常性存取或封存 Blob 層。 本文同時適用于 Azure 資料箱和 Azure Data Box Heavy。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>為您的資料選擇正確的儲存層

Azure 儲存體可讓三個不同層以最符合成本效益的方式來儲存資料：經常性存取、非經常性存取或封存。 經常性存取儲存層已最佳化，可用於儲存經常存取的資料。 經常性儲存層的儲存成本高於非經常性儲存層和封存儲存層，但存取成本最低。

非經常性存取儲存層適合必須儲存最少 30 天且不常存取的資料。 非經常性存取層的儲存成本比經常性存取儲存層的低，但其資料存取費用比經常性存取層的高。

Azure 封存層已離線並提供最低的儲存成本，但存取成本最高。 此層適合要在封存儲存體中保留最少 180 天的資料。 如需每個這些層的計價模式詳細資訊，請移至[儲存層的比較](../storage/blobs/storage-blob-storage-tiers.md)。

資料箱或 Data Box Heavy 中的資料會上傳到與儲存體帳戶相關聯的儲存層。 當您建立儲存體帳戶時，您可以指定存取層是經常性存取的或非經常性存取的。 視您工作負載的存取模式和成本而定，您可以將這此資料從預設層移至另一個儲存層。

您只能將您在 Blob 儲存體或一般用途 v2 (GPv2) 帳戶中的物件儲存體資料分層。 一般用途 v1 (GPv1) 帳戶不支援階層處理。 若要為您的資料選擇正確的儲存層，請參閱 Azure Blob 儲存體中的詳細考慮 [： Premium、](../storage/blobs/storage-blob-storage-tiers.md)經常性存取、非經常性存取和封存儲存層。

## <a name="set-a-default-blob-tier"></a>設定預設 Blob 層

在 Azure 入口網站中建立儲存體帳戶時會指定預設 Blob 層。 一旦儲存體類型選取為 GPv2 或 Blob 儲存體，就能指定存取層屬性。 預設會選取「經常性存取層」。

如果您在訂購資料箱或 Data Box Heavy 時嘗試建立新的帳戶，則無法指定層級。 建立帳戶之後，您可以在入口網站中修改帳戶，以設定預設存取層。

或者，您使用指定的存取層屬性先建立儲存體帳戶。 建立資料箱或 Data Box Heavy 訂單時，請選取現有的儲存體帳戶。 如需如何在儲存體帳戶建立期間設定預設 Blob 層的詳細資訊，請移至[在 Azure 入口網站中建立儲存體帳戶](../storage/common/storage-account-create.md?tabs=portal)。

## <a name="move-data-to-a-non-default-tier"></a>將資料移至非預設層

將資料箱裝置的資料上傳至預設層之後，您可能會想要將資料移至非預設層。 有兩種方式可以將此資料移動到非預設層。

- **Azure Blob 儲存體生命週期管理** - 您可以使用以原則為基礎的方法，以自動將資料分層，或在其生命週期結束時過期。 如需詳細資訊，請移至[管理 Azure Blob 儲存體生命週期](../storage/blobs/storage-lifecycle-management-concepts.md)。
- **指令碼** - 您可以使用撰寫指令碼的方法，透過 Azure PowerShell 來啟用 Blob 等級的階層處理。 您可以呼叫 `SetBlobTier` 作業來設定 Blob 上的階層。

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>使用 Azure PowerShell 來設定 Blob 層

下列步驟描述您可以如何使用 Azure PowerShell 指令碼，將 Blob 階層設定成封存。

1. 開啟已提高權限的 [Windows PowerShell] 工作階段。 請確定您執行的是 PowerShell 5.0 或更新版本。 輸入：

   `$PSVersionTable.PSVersion`     

2. 登入 Azure PowerShell。 

   `Login-AzAccount`  

3. 定義儲存體帳戶、存取金鑰、容器和儲存體內容的變數。

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. 取得容器中的 Blob。

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. 將容器中的所有 Blob 的階層設定成封存。

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    範例輸出如下所示：

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > 如果您想要資料在内嵌時封存，請將預設帳戶層設定成經常性存取。 如果預設層是非經常性存取，則立即將資料移動到封存會有提早 30 天刪除的懲罰。

## <a name="next-steps"></a>下一步

-  了解如何[使用生命週期規則解決常見的資料階層處理案例](../storage/blobs/storage-lifecycle-management-concepts.md#examples)