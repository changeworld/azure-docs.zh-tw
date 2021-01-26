---
title: 在 Azure Lab Services 中使用共用映像庫 | Microsoft Docs
description: 了解如何將實驗室帳戶設定為使用共用映像庫，讓使用者可以與其他人員共用映像，而其他使用者可以使用該映像在實驗室中建立範本 VM。
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 984cb4c47099928ffab327895a728cbe8a8f9604
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791385"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure Lab Services 中使用共用映像庫
本文說明教師/實驗室管理員如何將範本虛擬機器映像儲存至[共用映像庫](../virtual-machines/shared-image-galleries.md)，以便其他人將其用於建立實驗室。 

> [!IMPORTANT]
> 使用共用映射庫時，Azure 實驗室服務僅支援小於 128 GB 作業系統磁碟空間的映射。 在實驗室建立期間，虛擬機器映射清單中不會顯示具有超過 128 GB 磁碟空間或多個磁片的映射。

## <a name="scenarios"></a>案例
以下是這項功能所支援的幾個案例： 

- 實驗室帳戶管理員會將共用映像庫連結至實驗室帳戶，並將映像上傳至實驗室內容外部的共用映像庫。 然後，實驗室建立者可以使用共用映像庫中的該映像來建立實驗室。 
- 實驗室帳戶管理員會將共用映像庫連結至實驗室帳戶。 實驗室建立者 (講師) 會將其實驗室的自訂映像儲存到共用映像庫。 然後，其他實驗室建立者可以從共用映像庫中選取此映像，為其實驗室建立範本。 

    當映像儲存到共用映像庫時，Azure Lab Services 會將儲存的映像複寫到相同[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中的其他可用區域。 其可確保映像適用於相同地理位置的其他區域中建立的實驗室。 將映像儲存到共用映像庫會產生額外成本，包括所有複寫映像的成本。 此成本與 Azure Lab Services 成本分開。 如需共用映像庫定價的詳細資訊，請參閱[共用映像庫 – 計費]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)。
    
## <a name="prerequisites"></a>Prerequisites
- 使用 [Azure PowerShell](../virtual-machines/shared-images-powershell.md) 或 [Azure CLI](../virtual-machines/shared-images-cli.md) 建立共用映像庫。
- 您已將共用映像庫連結至實驗室帳戶。 如需逐步指示，請參閱[如何連結共用映像庫或將其中斷連結](how-to-attach-detach-shared-image-gallery.md)。


## <a name="save-an-image-to-the-shared-image-gallery"></a>將映像儲存至共用映像庫
連結共用映像庫之後，實驗室帳戶管理員或教師可以將映像儲存到共用映像庫，以便其他教師重複使用該映像。 

1. 在實驗室的 [範本] 頁面上，選取工具列上的 [匯出至共用映像庫]。

    ![儲存映像按鈕](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. 在 [匯出至共用映像庫] 對話方塊中，輸入 **映像的名稱**，然後選取 [匯出]。 

    ![[匯出至共用映像庫] 對話方塊](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. 您可以在 [範本] 頁面上查看此作業的進度。 此作業可能需要一些時間。 

    ![匯出進行中](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 當匯出作業成功時，您會看到下列訊息：

    ![匯出已完成](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    將映像儲存到共用映像庫之後，您可以在建立另一個實驗室時，使用映像庫中的該映像。 您也可以將映像上傳至實驗室內容外部的共用映像庫。 如需詳細資訊，請參閱[共用映像庫概觀](../virtual-machines/shared-images-powershell.md)。 

    > [!IMPORTANT]
    > 當您將 Azure 實驗室服務中的 [實驗室範本映射儲存](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) 至共用映射庫時，影像會以 **特製化映射** 的形式上傳至資源庫。 [特製化映射](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) 會保留電腦特定的資訊和使用者設定檔。 您仍然可以直接將一般化映射上傳至 Azure 實驗室服務以外的資源庫。    

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用共用映像庫中的映像
教師可以在建立新實驗室期間，為範本挑選共用映像庫中可用的自訂映像。

![使用共用映像庫中的虛擬機器映像](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> 您可以根據 Azure 實驗室服務中的 **一般化** 和 **特製** 化映射來建立範本 VM。 


## <a name="next-steps"></a>後續步驟
如需共用映像庫的詳細資訊，請參閱[共用映像庫](../virtual-machines/shared-image-galleries.md)。