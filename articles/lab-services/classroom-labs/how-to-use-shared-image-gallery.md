---
title: 在 Azure Lab Services 中使用共用映像庫 | Microsoft Docs
description: 了解如何將實驗室帳戶設定為使用共用映像庫，讓使用者可以與其他人員共用映像，而其他使用者可以使用該映像在實驗室中建立範本 VM。
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 8253b784a427ceb3842b6b4d585c52a2c30e0c4d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592418"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure Lab Services 中使用共用映像庫
本文說明教師/實驗室管理員如何將範本虛擬機器映像儲存至[共用映像庫](../../virtual-machines/windows/shared-image-galleries.md)，以便其他人將其用於建立實驗室。 

## <a name="scenarios"></a>案例
以下是這項功能所支援的幾個案例： 

- 實驗室帳戶管理員會將共用映像庫連結至實驗室帳戶，並將映像上傳至實驗室內容外部的共用映像庫。 然後，實驗室建立者可以使用共用映像庫中的該映像來建立實驗室。 
- 實驗室帳戶管理員會將共用映像庫連結至實驗室帳戶。 實驗室建立者 (講師) 會將其實驗室的自訂映像儲存到共用映像庫。 然後，其他實驗室建立者可以從共用映像庫中選取此映像，為其實驗室建立範本。 

    當映像儲存到共用映像庫時，Azure Lab Services 會將儲存的映像複寫到相同[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中的其他可用區域。 其可確保映像適用於相同地理位置的其他區域中建立的實驗室。 將映像儲存到共用映像庫會產生額外成本，包括所有複寫映像的成本。 此成本與 Azure Lab Services 成本分開。 如需共用映像庫定價的詳細資訊，請參閱[共用映像庫 – 計費]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)。

    > [!NOTE]
    > Azure Lab Services 支援建立以共用映像庫中**一般化**和**特殊化**映像為基礎的範本 VM。 


## <a name="prerequisites"></a>Prerequisites
- 使用 [Azure PowerShell](../../virtual-machines/windows/shared-images.md) 或 [Azure CLI](../../virtual-machines/linux/shared-images.md) 建立共用映像庫。
- 您已將共用映像庫連結至實驗室帳戶。 如需逐步指示，請參閱[如何連結共用映像庫或將其中斷連結](how-to-attach-detach-shared-image-gallery.md)。


## <a name="save-an-image-to-the-shared-image-gallery"></a>將映像儲存至共用映像庫
連結共用映像庫之後，實驗室帳戶管理員或教師可以將映像儲存到共用映像庫，以便其他教師重複使用該映像。 

1. 在實驗室的 [範本] 頁面上，選取工具列上的 [匯出至共用映像庫]。

    ![儲存映像按鈕](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. 在 [匯出至共用映像庫] 對話方塊中，輸入**映像的名稱**，然後選取 [匯出]。 

    ![[匯出至共用映像庫] 對話方塊](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. 您可以在 [範本] 頁面上查看此作業的進度。 此作業可能需要一些時間。 

    ![匯出進行中](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 當匯出作業成功時，您會看到下列訊息：

    ![匯出已完成](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    > [!NOTE]
    > 將映像儲存到共用映像庫之後，您可以在建立另一個實驗室時，使用映像庫中的該映像。 
    > 
    > 您也可以將映像上傳至實驗室內容外部的共用映像庫。 如需詳細資訊，請參閱[共用映像庫概觀](../../virtual-machines/windows/shared-images.md)。 

    

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用共用映像庫中的映像
教師可以在建立新實驗室期間，為範本挑選共用映像庫中可用的自訂映像。

![使用共用映像庫中的虛擬機器映像](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>後續步驟
如需共用映像庫的詳細資訊，請參閱[共用映像庫](../../virtual-machines/windows/shared-image-galleries.md)。
