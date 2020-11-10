---
title: Microsoft Azure 資料箱磁碟的快速入門 | Microsoft Docs
description: 使用此快速入門，在 Azure 入口網站中快速部署您的 Azure 資料箱磁碟
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 23615daf4a07e02b01bbd5a9cdf57ec9a81a2b76
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347384"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站部署 Azure 資料箱磁碟

本快速入門說明如何使用 Azure 入口網站來部署 Azure 資料箱磁碟。 這些步驟包含如何快速建立訂單、接收磁碟、打開包裝、連線及將資料複製到磁碟，以便上傳至 Azure。

如需部署和追蹤的詳細逐步指示，請移至[教學課程：訂購 Azure 資料箱磁碟](data-box-disk-deploy-ordered.md)。 

如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F&preserve-view=true)。

::: zone-end

::: zone target="chromeless"

本指南會逐步引導您進行在 Azure 入口網站中使用 Azure 資料箱磁碟的步驟。 本指南可協助回答下列問題。

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Prerequisites

開始之前：

- 請確定您已啟用 Azure 資料箱服務的訂用帳戶。 若要啟用這項服務的訂用帳戶，請[註冊服務](https://aka.ms/azuredataboxfromdiskdocs)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs)。

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **檢閱必要條件** ：檢查磁碟和纜線、作業系統和其他軟體的數目。
> - **連線並解除鎖定** ：將裝置連線並將磁碟解除鎖定以複製資料。
> - **將資料複製到磁碟並進行驗證** ：將資料複製到磁碟中的預先建立資料夾。
> - **返還磁碟** ：將磁碟返還給 Azure 資料中心，以將資料上傳至儲存體帳戶。
> - **確認 Azure 中的資料** ：先確認資料已上傳至儲存體帳戶，再將其從來源資料伺服器中刪除。

::: zone-end


::: zone target="docs"

## <a name="order"></a>單

### <a name="portal"></a>[入口網站](#tab/azure-portal)

這個步驟需要大約 5 分鐘。

1. 在 Azure 入口網站中建立新的 Azure 資料箱資源。 
2. 選取針對此服務啟用的訂用帳戶，然後選擇 [匯入] 作為轉移類型。 提供資料所在的 [來源國家/地區]，以及資料傳輸的 [Azure 目的地區域]。
3. 選取 [資料箱磁碟]。 解決方案的容量上限為 35 TB，您可以針對較大的資料大小建立多個磁碟訂單。  
4. 輸入訂單詳細資料和出貨資訊。 如果您的區域可使用服務，請提供通知電子郵件地址、檢閱摘要，然後建立訂單。

一旦建立訂單後，磁碟就準備出貨。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用這些 Azure CLI 命令建立資料箱磁碟作業。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. 執行 [az group create](/cli/azure/group#az_group_create) 命令建立資源群組，或使用現有的資源群組：

   ```azurecli
   az group create --name databox-rg --location westus
   ```

1. 使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令建立儲存體帳戶，或使用現有的儲存體帳戶：

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. 執行 [az databox job create](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_create) 命令，使用 SKU DataBoxDisk 建立資料箱作業：

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxdisk-job \
       --location westus --sku DataBoxDisk --contact-name "Jim Gan" --phone=4085555555 \
       –-city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA \
       --storage-account databoxtestsa --expected-data-size 1
   ```

1. 執行 [az databox job update](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_update) 更新工作，如下列範例所示，您可以變更連絡人名稱和電子郵件：

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   執行 [az databox job show](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_show) 命令取得作業的相關資訊：

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   使用 [az databox job list]( /cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_list) 命令查看資源群組的所有資料箱作業：

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   執行 [az databox job cancel](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_cancel) 命令取消作業：

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   執行 [az databox job delete](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_delete) 命令刪除作業：

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. 使用 [az databox job list-credentials]( /cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_list_credentials) 命令列出資料箱作業的認證：

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

一旦建立訂單後，裝置就準備出貨。

---

## <a name="unpack"></a>打開包裝

這個步驟需要大約 5 分鐘。

資料箱磁碟會以 UPS 快遞包裹寄送。 開啟包裹，並檢查包裹中包含：

- 1 到 5 個發泡包裝的 USB 磁碟。
- 每個磁碟都有連接纜線。
- 退貨用的出貨標籤。

## <a name="connect-and-unlock"></a>連線並解除鎖定

這個步驟需要大約 5 分鐘。

1. 使用內含的纜線將磁碟連接到執行支援版本的 Windows/Linux 電腦。 如需有關支援作業系統版本的詳細資訊，請移至 [Azure 資料箱磁碟系統需求](data-box-disk-system-requirements.md)。 
2. 若要將磁碟解除鎖定：

    1. 在 Azure 入口網站中，前往 [一般] > [裝置詳細資料] 並取得通行金鑰。
    2. 在用來將資料複製到磁碟上的電腦上，下載及擷取作業系統特定的資料箱碟解除鎖定工具。 
    3. 執行資料箱磁碟解除鎖定工具，並提供通行金鑰。 若要執行磁碟重新插入，請再次執行解除鎖定工具，並提供通行金鑰。 **請勿使用 BitLocker 對話方塊或 BitLocker 金鑰來解除鎖定磁碟。** 如需如何將磁碟解除鎖定的詳細資訊，請移至[解除鎖定 Windows 用戶端上的磁碟](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)或[解除鎖定 Linux 用戶端上的磁碟](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)。
    4. 指派給磁碟的磁碟機代號會由工具顯示。 請記下磁碟機代號。 這會用於後續的步驟。

## <a name="copy-data-and-validate"></a>複製資料並驗證

完成這項作業的時間取決於您的資料大小。

1. 磁碟機包含 PageBlob、BlockBlob、AzureFile、ManagedDisk 和 DataBoxDiskImport 資料夾。 拖放需要以區塊 Blob 形式匯入的資料，並複製到 BlockBlob 資料夾。 同樣地，將 VHD/VHDX 等資料拖放到 PageBlob 資料夾，並將適當的資料拖放到 AzureFile。 將您想要以受控磁碟的形式上傳的 VHD 複製到 ManagedDisk 下的資料夾。

    系統會在 Azure 儲存體帳戶中，為 BlockBlob 和 PageBlob 資料夾下的每個子資料夾建立容器。 針對 AzureFile 下的子資料夾，會建立一個檔案共用。

    BlockBlob 和 PageBlob 資料夾下的所有檔案，都會複製到 Azure 儲存體帳戶下的預設容器 `$root`。 將檔案複製到 AzureFile 內的資料夾。 直接複製到 AzureFile 資料夾的任何檔案都會失敗，並以區塊 Blob 的形式上傳。

    > [!NOTE]
    > - 所有容器、Blob 和檔案都應符合 [Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 如果未遵循這些規則，則將資料上傳至 Azure 會失敗。
    > - 確定檔案不超過約 4.75 TiB (區塊 Blob)、8 TiB (分頁 Blob) 和 1 TiB (Azure 檔案儲存體)。

2. **(選擇性但建議執行)** 複製完成之後，強烈建議您至少應執行 DataBoxDiskImport 資料夾中提供的 `DataBoxDiskValidation.cmd`，並選取選項 1 以驗證檔案。 我們也建議，如果時間允許，您也應使用選項 2 產生總和檢查碼以進行驗證 (可能會花一些時間，視資料大小而定)。 執行這些步驟可盡量降低資料上傳至 Azure 時發生失敗的機率。
3. 安全地移除磁碟機。

## <a name="ship-to-azure"></a>寄送到 Azure

此步驟約需要 5-7 分鐘來完成。

1. 將所有磁碟一起放在原始套件中。 使用所包含的出貨標籤。 如果標籤受損或遺失，請從入口網站下載。 移至 [概觀]，然後從命令列中按 [下載出貨標籤]。
2. 將已密封的包裹交給運送公司。  

資料箱磁碟服務會傳送電子郵件通知，並且在 Azure 入口網站上更新訂單狀態。

## <a name="verify-your-data"></a>驗證您的資料

完成這項作業的時間取決於您的資料大小。

1. 當資料箱磁碟連線到 Azure 資料中心網路時，上傳至 Azure 的資料會自動啟動。
2. Azure 資料箱服務會通知您，已透過 Azure 入口網站完成資料複製。
    
    1. 檢查所有失敗的錯誤記錄，並採取適當的動作。
    2. 請先確認您的資料位於儲存體帳戶中，再從來源予以刪除。

## <a name="clean-up-resources"></a>清除資源

完成此步驟需要 2-3 分鐘。

若要清除，您可以取消資料箱訂單，然後再加以刪除。

- 訂單處理之前，您可以取消 Azure 入口網站中的資料箱訂單。 一旦處理訂單後，就無法取消訂單。 訂單會進行直到達到已完成的階段為止。

    若要取消訂單，請前往 [概觀]，然後從命令列按一下 [取消]。  

- 一旦 Azure 入口網站中的狀態顯示為 [已完成] 或是 [已取消] 後，您就可以刪除訂單。

    若要刪除訂單，請前往 [概觀]，然後從命令列按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 Azure 資料箱磁碟，以協助將您的資料匯入 Azure。 若要深入了解 Azure 資料箱磁碟管理，請進入下列教學課程：

> [!div class="nextstepaction"]
> [使用 Azure 入口網站來管理資料箱磁碟](data-box-portal-ui-admin.md)

::: zone-end
