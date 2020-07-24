---
title: 使用 NFS 3.0 通訊協定（預覽）在 Linux 上掛接 Azure Blob 儲存體 |Microsoft Docs
description: 瞭解如何使用 NFS 3.0 通訊協定，從以 Linux 為基礎的 Azure 虛擬機器（VM）或在內部部署環境中執行的 Linux 系統掛接 Blob 儲存體中的容器。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 8f4ef046221ae50d2b05525d6cea2d268282551c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097610"
---
# <a name="mount-blob-storage-on-linux-using-the-network-file-system-nfs-30-protocol-preview"></a>使用網路檔案系統（NFS）3.0 通訊協定（預覽）在 Linux 上掛接 Blob 儲存體

您可以使用 NFS 3.0 通訊協定，從以 Linux 為基礎的 Azure 虛擬機器（VM）或在內部部署環境中執行的 Linux 系統，掛接 Blob 儲存體中的容器。 本文提供逐步指引。 若要深入瞭解 Blob 儲存體中的 NFS 3.0 通訊協定支援，請參閱[Azure blob 儲存體（預覽）中的網路檔案系統（NFS）3.0 通訊協定支援](network-file-system-protocol-support.md)。

> [!NOTE]
> Azure Blob 儲存體中的 NFS 3.0 通訊協定支援處於公開預覽狀態，並可在下欄區域使用：美國東部、美國中部和加拿大中部。

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>步驟1：向您的訂用帳戶註冊 NFS 3.0 通訊協定功能

1. 開啟 PowerShell 命令視窗。 

2. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

   ```powershell
   Connect-AzAccount
   ```

3. 如果您的身分識別與多個訂用帳戶相關聯，請設定您的有效訂用帳戶。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   使用訂閱識別碼取代 `<subscription-id>` 預留位置值。

4. `AllowNFSV3`使用下列命令註冊功能。

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. 也請 `PremiumHns` 使用下列命令來註冊此功能。

   ```powershell
   Register-AzProviderFeature -FeatureName PremiumHns -ProviderNamespace Microsoft.Storage  
   ```

6. 使用下列命令來註冊資源提供者。
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>步驟2：確認已註冊此功能 

註冊核准最多可能需要一小時的時間。 若要確認註冊是否已完成，請使用下列命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName PremiumHns  
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>步驟3：建立 Azure 虛擬網路（VNet）

您的儲存體帳戶必須包含在 VNet 中。 VNet 可讓用戶端安全地連接到您的儲存體帳戶。 若要深入瞭解 VNet，以及如何建立它，請參閱[虛擬網路檔](https://docs.microsoft.com/azure/virtual-network/)。

> [!NOTE]
> 相同 VNet 中的用戶端可以在您的帳戶中掛接容器。 您也可以從內部部署網路中執行的用戶端掛接容器，但必須先將內部部署網路連線至 VNet。 請參閱[支援的網路連接](network-file-system-protocol-support.md#supported-network-connections)。

## <a name="step-4-configure-network-security"></a>步驟4：設定網路安全性

保護帳戶中資料的唯一方法是使用 VNet 和其他網路安全性設定。 在已啟用 NFS 3.0 通訊協定支援的帳戶中，還不支援用來保護資料的任何其他工具，包括帳戶金鑰授權、Azure Active Directory （AD）安全性和存取控制清單（Acl）。 

若要保護您帳戶中的資料，請參閱下列建議：[適用于 Blob 儲存體的網路安全性建議](security-recommendations.md#networking)。

## <a name="step-5-create-and-configure-a-storage-account"></a>步驟5：建立和設定儲存體帳戶

若要使用 NFS 3.0 掛接容器，您必須在向訂用帳戶註冊功能**之後**，建立儲存體帳戶。 您無法在註冊功能之前啟用已存在的帳戶。 

在此功能的預覽版本中，只有[BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md)帳戶支援 NFS 3.0 通訊協定。

當您設定帳戶時，請選擇下列值：

|設定 | 值|
|----|---|
|Location|下列其中一個區域：美國東部、美國中部和加拿大中部 |
|效能|Premium|
|帳戶類型|BlockBlobStorage|
|複寫|本地備援儲存體 (LRS)|
|連線方法|公用端點（選取的網路）或私人端點|
|需要安全傳輸|已停用|
|階層式命名空間|啟用|
|NFS V3|啟用|

您可以接受所有其他設定的預設值。 

## <a name="step-6-create-a-container"></a>步驟 6：建立容器

使用下列任何一種工具或 Sdk，在您的儲存體帳戶中建立容器：

|工具|SDK|
|---|---|
|[Azure 儲存體總管](data-lake-storage-explorer.md#create-a-container)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-blobs.md#create-a-container)|[Java](data-lake-storage-directory-file-acl-java.md#create-a-container)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](https://docs.microsoft.com/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>步驟7：掛接容器

1. 在 Linux 系統上，建立目錄。

   ```
   mkdir -p /mnt/test
   ```

2. 使用下列命令來掛接容器。

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - 將 `<storage-account-name>` 出現在此命令中的預留位置取代為您的儲存體帳戶名稱。  

   - `<container-name>`以您的容器名稱取代預留位置。

## <a name="resolve-common-issues"></a>解決常見的問題

|問題/錯誤 | 解決方案|
|---|---|
|`Access denied by server while mounting`|確定您的用戶端是在支援的子網內執行。 請參閱[支援的網路位置](network-file-system-protocol-support.md#supported-network-connections)。|
|`No such file or directory`| 確認您要掛接的容器是在您確認該功能已註冊之後所建立。 請參閱[步驟2：確認已註冊此功能](#step-2-verify-that-the-feature-is-registered)。此外，請務必在終端機中直接輸入 mount 命令和它的參數。 如果您將此命令的任何部分複製並貼到另一個應用程式的終端機中，則貼上資訊中的隱藏字元可能會導致此錯誤出現。|
|使用非 NFS 3.0 工具所上傳的檔案，不會顯示在目錄中。 | 請取消掛接容器，然後重新裝載容器。 |

## <a name="see-also"></a>請參閱

[Azure Blob 儲存體中的網路檔案系統（NFS）3.0 通訊協定支援（預覽）](network-file-system-protocol-support.md)







