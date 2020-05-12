---
title: 使用 Azure 實驗室服務中的共用映射資源庫 |Microsoft Docs
description: 瞭解如何設定實驗室帳戶以使用共用映射資源庫，讓使用者可以與其他人員共用映射，而其他使用者可以使用該映射在實驗室中建立範本 VM。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: 294a270107033590e340db49b85e8a67acbbb701
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116861"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>使用 Azure 實驗室服務中的共用映射資源庫
本文說明老師/lab 管理員如何將範本虛擬機器映射儲存到[共用映射資源庫](../../virtual-machines/windows/shared-image-galleries.md)，讓其他人可以使用它來建立實驗室。 

## <a name="scenarios"></a>案例
以下是這項功能支援的幾個案例： 

- 實驗室帳戶管理員會將共用映射資源庫附加至實驗室帳戶，並將影像上傳至實驗室內容外部的共用映射資源庫。 然後，實驗室建立者可以使用共用映射資源庫中的該映射來建立實驗室。 
- 實驗室帳戶管理員會將共用映射資源庫附加至實驗室帳戶。 實驗室建立者（講師）會將其實驗室的自訂映射儲存到共用映射資源庫。 然後，其他實驗室建立者可以從共用映射庫中選取此映射，以建立其實驗室的範本。 

    當映射儲存到共用映射資源庫時，Azure 實驗室服務會將儲存的影像複寫到相同[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中可用的其他區域。 它可確保映射適用于在相同地理位置的其他區域中建立的實驗室。 將影像儲存到共用映射資源庫會產生額外的成本，包括所有複寫影像的成本。 這種成本與 Azure 實驗室服務的使用成本不同。 如需共用映射庫價格的詳細資訊，請參閱[共用映射資源庫–計費]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)。

    > [!NOTE]
    > Azure 實驗室服務支援根據共用映射庫中的**一般化**和**特製**化映射來建立範本 vm。 


## <a name="prerequisites"></a>先決條件
- 使用[Azure PowerShell](../../virtual-machines/windows/shared-images.md)或[Azure CLI](../../virtual-machines/linux/shared-images.md)建立共用映射資源庫。
- 您已將共用映射資源庫連接至實驗室帳戶。 如需逐步指示，請參閱[如何附加或卸離共用映射資源庫](how-to-attach-detach-shared-image-gallery.md)。


## <a name="save-an-image-to-the-shared-image-gallery"></a>將影像儲存到共用映射資源庫
連結共用映射庫之後，實驗室帳戶管理員或老師可以將影像儲存到共用映射資源庫，讓其他老師可以重複使用該映射。 

1. 在實驗室的 [**範本**] 頁面上，選取工具列上的 [**匯出至共用映射資源庫**]。

    ![[儲存影像] 按鈕](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. 在 [**匯出至共用映射資源庫**] 對話方塊中，輸入**影像的名稱**，然後選取 [**匯出**]。 

    ![[匯出至共用映射資源庫] 對話方塊](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. 您可以在 [**範本**] 頁面上查看這項作業的進度。 此作業可能需要一些時間。 

    ![匯出進行中](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 當匯出作業成功時，您會看到下列訊息：

    ![匯出已完成](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    > [!NOTE]
    > 將映射儲存到共用映射資源庫之後，您可以在建立另一個實驗室時，從資源庫使用該映射。 
    > 
    > 您也可以將影像上傳至實驗室內容外的共用映射資源庫。 如需詳細資訊，請參閱[共用映射資源庫總覽](../../virtual-machines/windows/shared-images.md)。 

    

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用共用映射庫中的映射
老師/教授可以在新的實驗室建立期間，為範本選擇可在共用映射資源庫中使用的自訂映射。

![從資源庫使用虛擬機器映射](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>後續步驟
如需共用映射資源庫的詳細資訊，請參閱[共用映射資源庫](../../virtual-machines/windows/shared-image-galleries.md)。
