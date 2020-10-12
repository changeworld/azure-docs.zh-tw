---
title: Windows 開機管理程式錯誤-0xC0000428 狀態不正確映射雜湊
titlesuffix: Azure Virtual Machines
description: 本文提供的步驟可解決使用預覽映射的問題，以及試用期已過期的問題，這會導致 Azure 虛擬機器 (VM) 無法開機。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: f607ebb64b27c45ec696d7fcd431a0ba2342697f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89447767"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Windows 開機管理程式錯誤-0xC0000428 狀態不正確映射雜湊

本文提供的步驟可解決使用預覽映射的問題，以及試用期已過期的問題，這會導致 Azure 虛擬機器 (VM) 無法開機。

## <a name="symptom"></a>徵狀

當您使用 [開機診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示 Windows 開機管理程式，並顯示下列訊息：

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![[圖 1] 顯示 Windows 開機管理程式的狀態為「Ox0000428」，而「Windows 無法驗證此檔案的數位簽章」資訊。](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

或訊息：

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![[圖 2] 顯示 [Windows 開機管理程式] 的狀態為「Ox0000428」，以及「無法驗證此檔案的數位簽章」的資訊。](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>原因

用來建立 VM 的映射是預覽映射，其到期日不是 RTM (發行至製造) 映射。 

預覽映射具有指定的生命週期，而當您傳遞到期日時，即會顯示您所看到的螢幕擷取畫面，這表示影像的試用已結束。

### <a name="example-of-preview-images"></a>預覽影像的範例

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

您無法延長預覽映像的到期日。 預覽過期後，VM 將無法再開機。

- 根據產品而定，試用期限可能會有所不同。 例如，Windows preview 映射有180天的試用期。

- 在 Azure 中，所有預覽版本的 Windows 映像都會在其描述中包含備註，這些映射不適合用于生產環境，而且僅適用于指定的試用期或「預覽版本」。

## <a name="solution"></a>解決方法

如果您的映射是預覽影像，則無法延長所用映射的到期日，您將需要使用非預覽映射來 [部署新的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) 。 下列步驟可協助您識別您是否已使用預覽映射，以及提供資源來協助您將資料從此 VM 傳送至新的 VM。 如果您已將影像識別為預覽影像，則映射無法復原，因為它現在已過期。

根據您的喜好設定，您可以使用 Azure PowerShell 或 Azure CLI 來查詢您的影像，以判斷它是否為預覽影像。 您可以使用這些命令來確認映射是預覽映射。

### <a name="query-using-azure-powershell"></a>使用 Azure PowerShell 查詢

1. 開啟 Windows PowerShell 應用程式。
1. 執行下列命令：

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- 在先前的命令中， `<LOCATION>` 將、、 `<PUBLISHER NAME>` `<OFFER NAME>` 和取代 `<YEAR WHEN THIS IMAGE WAS RELEASED>` 為所述的資訊。 也請移除 "<" 和 ">" 符號。

  請參閱下列範例：

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>使用 Azure CLI 進行查詢

1. 如果您還沒有這麼做，就必須 [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。
1. 下載之後，請使用命令提示字元或 PowerShell 來輸入 `az login` 命令，然後使用您的帳號憑證登入。
1. 登入後，請輸入下列命令：

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- 在先前的命令中， `<LOCATION>` 將、、 `<PUBLISHER NAME>` `<OFFER NAME>` 和取代 `<YEAR WHEN THIS IMAGE WAS RELEASED>` 為所述的資訊。 也請移除 "<" 和 ">" 符號。

  請參閱下列範例：

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```
