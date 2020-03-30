---
title: 在 Azure 實驗室服務中使用共用映射庫 |微軟文檔
description: 瞭解如何將實驗室帳戶配置為使用共用圖像庫，以便使用者可以與其他使用者共用映射，而另一個使用者可以使用該映射在實驗室中創建範本 VM。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190444"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure 實驗室服務中使用共用映射庫
本文介紹教師/實驗室管理員如何保存範本虛擬機器映射以供他人重用。 這些映射保存在 Azure[共用映射庫中](../../virtual-machines/windows/shared-image-galleries.md)。 作為第一步，實驗室管理員將現有的共用映射庫附加到實驗室帳戶。 附加共用圖像庫後，在實驗室帳戶中創建的實驗室可以將圖像保存到共用圖像庫。 其他教師可以從共用圖像庫中選擇此圖像，為其班級創建範本。 

> [!NOTE]
> 目前，Azure 實驗室服務僅支援基於共用映射庫中**的通用**VM 映射（非專用映射）創建範本 VM。 

## <a name="prerequisites"></a>Prerequisites
- 使用[Azure PowerShell](../../virtual-machines/windows/shared-images.md)或[Azure CLI](../../virtual-machines/linux/shared-images.md)創建共用映射庫。
- 您已將共用圖像庫附加到實驗室帳戶。 有關分步說明，請參閱[如何附加或分離共用圖像庫](how-to-attach-detach-shared-image-gallery.md)。


## <a name="save-an-image-to-the-shared-image-gallery"></a>將圖像保存到共用圖像庫
附加共用圖像庫後，實驗室帳戶管理員或教師可以將圖像保存到共用圖像庫，以便其他教師可以重複使用該圖像。 

1. 在實驗室的 **"範本"** 頁上，選擇工具列上的 **"匯出到共用圖像庫**"。

    ![保存圖像按鈕](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. 在 **"匯出到共用圖像庫**"對話方塊中，輸入**圖像的名稱**，然後選擇 **"匯出**"。 

    ![匯出到共用圖像庫對話方塊](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. 您可以在 **"範本"** 頁上查看此操作的進度。 此操作可能需要一些時間。 

    ![正在出口](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 匯出操作成功後，您將看到以下消息：

    ![出口已完成](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

您還可以將圖像上載到實驗室上下文外的共用圖像庫。 有關詳細資訊，請參閱[共用圖像庫概述](../../virtual-machines/windows/shared-images.md)。 

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用共用圖像庫中的圖像
教師/教授可以在新實驗室創建期間為範本選擇共用圖像庫中可用的自訂圖像。

![使用庫中的虛擬機器映射](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>後續步驟
有關共用圖像庫的詳細資訊，請參閱[共用圖像庫](../../virtual-machines/windows/shared-image-galleries.md)。
