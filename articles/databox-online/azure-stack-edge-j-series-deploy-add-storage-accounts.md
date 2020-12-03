---
title: 使用 Azure Stack Edge Pro GPU 將資料傳輸至儲存體帳戶的教學課程 | Microsoft Docs
description: 了解如何在 Azure Stack Edge Pro GPU 裝置上新增和連線到本機和 Edge 儲存體帳戶。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: c24970b30e522d593a491194dbbabb0421ad7f4b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185524"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>教學課程：以 Azure Stack Edge Pro GPU 透過儲存體帳戶傳輸資料 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本教學課程說明如何新增及連線到 Azure Stack Edge Pro 裝置上的儲存體帳戶。 在新增儲存體帳戶之後，Azure Stack Edge Pro 即可將資料傳輸至 Azure。

此程序可能需要約 30 分鐘才能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增儲存體帳戶
> * 連接到儲存體帳戶

 
## <a name="prerequisites"></a>必要條件

將儲存體帳戶新增至 Azure Stack Edge Pro 之前，請確定：

- 您已依照[安裝 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md) 中的說明安裝實體裝置。

- 您已依照[啟動 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) 中的說明啟動實體裝置。


## <a name="add-an-edge-storage-account"></a>新增 Edge 儲存體帳戶

若要建立 Edge 儲存體帳戶，請執行下列程序：

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>連線到 Edge 儲存體帳戶

您現在可以透過 http 或 https 連線到 Edge 儲存體 REST API。

- Https 是安全且建議的方式。
- 透過受信任的網路連線時，會使用 Http。

## <a name="connect-via-http"></a>透過 HTTP 連線

透過 http 連線到 Edge 儲存體 REST API 需要下列步驟：

- 將一致的 Azure 服務 VIP 和 Blob 新增到遠端主機
- 驗證連線 

下列各節將說明這其中每個步驟。

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>將裝置 IP 位址和 Blob 服務端點新增到遠端用戶端

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>驗證連線

若要驗證連線，您通常需要在上一個步驟中收集的下列資訊 (所需資訊可能不同)：

- 儲存體帳戶名稱。
- 儲存體帳戶存取金鑰。
- Blob 服務端點。

您已經有儲存體帳戶名稱和 Blob 服務端點。 您可以使用 Azure PowerShell 用戶端透過 Azure Resource Manager 連線到裝置，以取得儲存體帳戶存取金鑰。

請遵循[透過 Azure Resource Manager 連線到裝置](azure-stack-edge-j-series-connect-resource-manager.md)中的步驟。 當您透過 Azure Resource Manager 登入本機裝置 API 後，請取得裝置上的儲存體帳戶清單。 執行下列 Cmdlet：

`Get-AzureRMStorageAccount`

從裝置上的儲存體帳戶清單中，找出需要取得存取金鑰的儲存體帳戶。 請注意儲存體帳戶和資源群組。

範例輸出如下所示：

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

執行下列 Cmdlet 以取得存取金鑰：

`Get-AzureRmStorageAccountAccessKey`

範例輸出如下所示：

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

複製並儲存此金鑰。 您將透過 Azure 儲存體總管，使用此金鑰來驗證連線。

若要確認是否已成功建立連線，請使用「儲存體總管」來連結至外部儲存體帳戶。 如果您沒有儲存體總管，請[下載儲存體總管](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409)。

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>透過 HTTPS 連線

透過 https 連線到 Azure Blob 儲存體 REST API 需要下列步驟：

- 取得您的 Blob 端點憑證
- 匯入用戶端或遠端主機上的憑證
- 將裝置 IP 和 Blob 服務端點新增到用戶端或遠端主機
- 設定並確認連線

下列各節將說明這其中每個步驟。

### <a name="get-certificate"></a>取得憑證

必須有裝置的 SSL 憑證，才能透過 HTTPS 存取 Blob 儲存體。 您也會將此憑證以 .pfx 檔案搭配連接的私密金鑰上傳至您的 Azure Stack Edge Pro 裝置。 如需有關如何建立 (僅適用於測試和開發用途) 並將這些憑證上傳至 Azure Stack Edge Pro 裝置的詳細資訊，請移至：

- [建立 Blob 端點憑證](azure-stack-edge-j-series-manage-certificates.md#create-certificates-optional)。
- [上傳 Blob 端點憑證](azure-stack-edge-j-series-manage-certificates.md#upload-certificates)。
- [在存取裝置的用戶端上匯入憑證](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)。

### <a name="import-certificate"></a>匯入憑證

如果使用 Azure 儲存體總管連線到裝置上的儲存體帳戶，您也必須將憑證以 PEM 格式匯入儲存體總管。 在 Windows 環境中，Base-64 編碼的 .cer 與 PEM 格式相同。

請採取下列步驟，在 Azure 儲存體總管上匯入憑證：

1. 請確定 Azure 儲存體總管的目標是 Azure Stack API。 移至 **編輯 > 目標 Azure Stack API**。 出現提示時，請重新啟動儲存體總管，變更才會生效。

2. 若要匯入 SSL 憑證，請移至 **編輯 > SSL 憑證 > 匯入憑證**。

  
   ![將憑證匯入儲存體總管](./media/azure-stack-edge-j-series-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. 瀏覽並提供簽署鏈結和 Blob 憑證。 簽署鏈結和 Blob 憑證都應該是 PEM 格式，這與 Windows 系統上的 Base64 編碼格式相同。 系統會通知您已成功匯入憑證。


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>新增裝置 IP 位址和 Blob 服務端點

請遵循相同的步驟，以[在透過 http  連線時新增裝置的 IP 位址和 Blob 服務端點](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client)。

### <a name="configure-and-verify-connection"></a>設定並驗證連線

請遵循相關步驟，以[透過 http 連線時設定並驗證所使用的連線](#verify-connection)。 唯一的差別是您應該使 [使用 http 選項]  保持未核取狀態。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解下列 Azure Stack Edge Pro 主題：

> [!div class="checklist"]
> * 新增儲存體帳戶
> * 連線至儲存體帳戶

若要了解如何使用 Azure Stack Edge Pro 來傳輸資料，請繼續進行下一個教學課程：

> [!div class="nextstepaction"]
> [使用 Azure Stack Edge Pro 轉換資料](./azure-stack-edge-j-series-deploy-configure-compute.md)


