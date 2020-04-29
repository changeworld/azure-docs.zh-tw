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
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78190444"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>使用 Azure 實驗室服務中的共用映射資源庫
本文說明老師/lab 管理員如何儲存範本虛擬機器映射，供其他人重複使用。 這些映射會儲存在 Azure[共用映射資源庫](../../virtual-machines/windows/shared-image-galleries.md)中。 第一個步驟是，實驗室系統管理員將現有的共用映射資源庫附加至實驗室帳戶。 連結共用映射資源庫之後，在實驗室帳戶中建立的實驗室可以將影像儲存到共用映射資源庫。 其他老師可以從共用映射庫中選取此映射，以建立其類別的範本。 

> [!NOTE]
> 目前，Azure 實驗室服務僅支援根據共用映射庫中的**一般化**VM 映射（非特製化映射）建立範本 vm。 

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

您也可以將影像上傳至實驗室內容外的共用映射資源庫。 如需詳細資訊，請參閱[共用映射資源庫總覽](../../virtual-machines/windows/shared-images.md)。 

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用共用映射庫中的映射
老師/教授可以在新的實驗室建立期間，為範本選擇可在共用映射資源庫中使用的自訂映射。

![從資源庫使用虛擬機器映射](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>後續步驟
如需共用映射資源庫的詳細資訊，請參閱[共用映射資源庫](../../virtual-machines/windows/shared-image-galleries.md)。
