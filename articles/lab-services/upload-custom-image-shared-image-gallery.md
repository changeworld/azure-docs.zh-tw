---
title: Azure 實驗室服務-將自訂映射上傳至共用映射庫
description: 說明如何將自訂映射上傳至共用映射庫。 大學 IT 部門會發現匯入映射特別有用。
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: cd701215eb375b7f9b867ba05082afc7ed348ff7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712389"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>將自訂映像上傳至共用映像庫

您可以使用共用映射庫來匯入自己的自訂映射，以在 Azure 實驗室服務中建立實驗室。 大學 IT 部門會發現匯入映射特別有説明，原因如下： 

* 您不需要使用實驗室的範本 VM 手動建立映射。
* 您可以上傳使用其他工具（例如 SCCM、端點管理員等）所建立的影像。

本文說明可採取的步驟，以帶入自訂映射，並在 Azure 實驗室服務中使用。 

> [!IMPORTANT]
> 當您將影像從實體實驗室環境移至 Az Labs 時，您需要將它們重新組織 appropriatly。 請勿直接從實體實驗室重複使用現有的映射。 <br/>如需詳細資訊，請參閱 [從實體實驗室移至 Azure 實驗室服務](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) 的 blog 文章。

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>從實體實驗室環境引入自訂映射

下列步驟說明如何匯入從實體實驗室環境開始的自訂映射。 然後，會從這個環境建立 VHD 並匯入 Azure 中的共用映射庫，以便在 Azure 實驗室服務中使用。

有許多選項可用來從實體實驗室環境建立 VHD。 下列步驟示範如何從 Windows Hyper-v VM 建立 VHD：

1. 從您已從映射建立的實體實驗室環境中的 Hyper-v VM 開始。
    1. VM 必須建立為第1代 VM。
    1. VM 必須使用固定磁片大小。 您也可以在此視窗中指定磁片的大小。 磁片大小不能大於 128 GB。<br/>    
    Azure 實驗室服務不支援磁片大小 > 128 GB 的映射。 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="連接虛擬硬碟":::   
    1. 如往常般將 VM 映射。
1. 連線[至 VM，並為 Azure 做好準備](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)。
    1. [設定適用於 Azure 的 Windows 設定](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#set-windows-configurations-for-azure)
    1. [檢查至少需要的 Windows 服務，以確保 VM 連線能力](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#check-the-windows-services)
    1. [更新遠端桌面登錄設定](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#update-remote-desktop-registry-settings)
    1. [設定 Windows 防火牆規則](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#configure-windows-firewall-rules)
    1. 安裝 Windows 更新
    1. [安裝 Azure VM 代理程式和其他設定（如下所示）](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#complete-the-recommended-configurations) 
    
    上述步驟會建立特製化映射。 如果建立一般化映射，您也必須執行 [SysPrep](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#determine-when-to-use-sysprep)。 <br/>
        如果您想要維護使用者目錄 (，其中可能包含映射中包含的軟體所需的檔案、使用者帳戶資訊等 ) ，您應該建立特製化映射。
1. 因為 **hyper-v** 預設會建立 **VHDX** 檔案，所以您需要將此檔案轉換成 VHD 檔案。
    1. 流覽至 [ **hyper-v 管理員**  ->  **動作**  ->  **編輯磁片**]。
    1. 在這裡，您可以選擇將磁片從 VHDX **轉換** 成 VHD
    1. 嘗試擴充磁片大小時，請務必不要超過 128 GB。        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="連接虛擬硬碟":::   
1. 將 VHD 上傳至 Azure 以建立受控磁片。
    1. 您可以從命令列使用儲存體總管或 AzCopy，如將 [VHD 上傳至 Azure 或將受控磁碟複製到另一個區域](https://docs.microsoft.com/azure/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell)中所述。        
    如果您的電腦進入睡眠或鎖定狀態，上傳程式可能會中斷並失敗。
    1. 此步驟的結果是您現在有一個可在 Azure 入口網站中看到的受控磁片。 
        您可以使用 Azure 入口網站的 [Size\Performance] 索引標籤來選擇您的磁片大小。 如先前所述，大小必須是 > 128 GB。
1. 取得受控磁片的快照集。
    您可以從 PowerShell、使用 Azure 入口網站，或從儲存體總管內執行這項操作，如 [使用入口網站或 PowerShell 建立快照](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)集所述。
1. 在共用映射庫中，建立映射定義和版本：
    1. [建立映射定義](https://docs.microsoft.com/azure/virtual-machines/windows/shared-images-portal#create-an-image-definition)。
    1. 您也必須在這裡指定是否要建立特製化/一般化映射。
1. 在 Azure 實驗室服務中建立實驗室，然後從共用映射庫中選取自訂映射。

    如果您在作業系統安裝于原始 Hyper-v VM 之後擴充磁片，您也必須在 Windows 中擴充 C 磁片磁碟機，以使用未配置的磁碟空間。 若要這樣做，請在建立實驗室之後登入範本 VM，然後遵循類似于 [擴充基本磁碟](https://docs.microsoft.com/windows-server/storage/disk-management/extend-a-basic-volume)區中所示的步驟。 您可以透過 UI 以及使用 PowerShell 來執行此動作的選項。

## <a name="next-steps"></a>後續步驟

* [共用映像資源庫概觀](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)
* [如何使用共用映射庫](how-to-use-shared-image-gallery.md)
