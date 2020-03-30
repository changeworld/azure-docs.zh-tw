---
title: 在 Azure 實驗室服務中連接或分離共用映射庫 |微軟文檔
description: 本文介紹如何將共用映射庫附加到 Azure 實驗室服務中的教室實驗室。
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
ms.openlocfilehash: 00dbef7b4453ffcb54020340bde51f55827759a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284313"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure 實驗室服務中連接或分離共用映射庫
教師/實驗室管理員可以將範本 VM 映射保存在 Azure[共用映射庫中](../../virtual-machines/windows/shared-image-galleries.md)，以供其他人重複使用。 作為第一步，實驗室管理員將現有的共用映射庫附加到實驗室帳戶。 附加共用圖像庫後，在實驗室帳戶中創建的實驗室可以將圖像保存到共用圖像庫。 其他教師可以從共用圖像庫中選擇此圖像，為其班級創建範本。 

將映射保存到共用映射庫時，Azure 實驗室服務會將保存的圖像複製到同一[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中可用的其他區域。 它可確保圖像可用於在同一地理位置的其他地區創建的實驗室。 將圖像保存到共用圖像庫會產生額外的成本，其中包括所有複製圖像的成本。 此成本與 Azure 實驗室服務使用成本分開。 有關共用圖像庫定價的詳細資訊，請參閱[共用圖像庫和計費]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)。

本文介紹如何將共用圖像庫附加到實驗室帳戶。 

> [!NOTE]
> 目前，Azure 實驗室服務僅支援基於共用映射庫中**的通用**VM 映射（非專用映射）創建範本 VM。 


## <a name="configure-at-the-time-of-lab-account-creation"></a>在創建實驗室帳戶時進行配置
創建實驗室帳戶時，可以將共用圖像庫附加到實驗室帳戶。 您可以從下拉清單中選擇現有的共用圖像庫，也可以創建新的共用圖像庫。 要創建共用圖像庫並將其附加到實驗室帳戶，請選擇"**創建新**"，輸入庫的名稱，然後輸入 **"確定**"。 

![在創建實驗室帳戶時配置共用映射庫](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>創建實驗室帳戶後配置
創建實驗室帳戶後，您可以執行以下任務：

- 創建並附加共用圖像庫
- 將共用圖像庫附加到實驗室帳戶
- 從實驗室帳戶分離共用圖像庫

## <a name="create-and-attach-a-shared-image-gallery"></a>創建並附加共用圖像庫
1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]****。 選取 **DEVOPS** 區段中的 [實驗室服務]****。 如果您選取 [實驗室服務]**** 旁邊的星號 (`*`)，它會新增到左功能表上的 [我的最愛]**** 區段。 從下一次開始，您可選取 [我的最愛]**** 下方的 [實驗室服務]****。

    ![[所有服務] -> [實驗室服務]](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 選擇您的實驗室帳戶以查看 **"實驗室帳戶"** 頁面。 
4. 選擇左側功能表上的 **"共用圖像庫**"，然後選擇"在工具列上**創建**"。  

    ![創建共用圖像庫按鈕](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. 在 **"創建共用圖像庫**"視窗中，輸入庫**的名稱**，然後輸入 **"確定**"。 

    ![創建共用圖像庫視窗](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure 實驗室服務將創建共用映射庫並將其附加到實驗室帳戶。 在此實驗室帳戶中創建的所有實驗室都有權訪問附加的共用映射庫。 

    ![附加的圖像庫](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    在底部窗格中，您可以在共用圖像庫中看到圖像。 在這個新庫中，沒有圖像。 將圖像上載到庫時，您會在此頁面上看到它們。     

    預設情況下，連接的共用圖像庫中的所有圖像都已啟用。 您可以通過在清單中選擇選定的**圖像並使用"啟用所選圖像**"或禁用所選圖像按鈕來啟用或**禁用選定的圖像**。

## <a name="attach-an-existing-shared-image-gallery"></a>附加現有的共用映射庫
以下過程演示如何將現有共用映射庫附加到實驗室帳戶。 

1. 在 **"實驗室帳戶"** 頁上，選擇左側功能表上的 **"共用圖像庫**"，然後選擇工具列上的 **"附加**"。 

    ![共用圖像庫 - 添加按鈕](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. 在 **"連接現有共用圖像庫"** 頁上，選擇共用圖像庫，然後選擇 **"確定**"。

    ![選擇現有庫](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 您將看到以下螢幕： 

    ![清單中的我的庫](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    在此示例中，共用圖像庫中尚未存在圖像。

    Azure 實驗室服務標識將作為參與者添加到附加到實驗室的共用映射庫。 它允許教師/IT 管理員將虛擬機器映射保存到共用映射庫。 在此實驗室帳戶中創建的所有實驗室都有權訪問附加的共用映射庫。 

    預設情況下，連接的共用圖像庫中的所有圖像都已啟用。 您可以通過在清單中選擇選定的**圖像並使用"啟用所選圖像**"或禁用所選圖像按鈕來啟用或**禁用選定的圖像**。 

## <a name="detach-a-shared-image-gallery"></a>分離共用圖像庫
實驗室只能將一個共用圖像庫附加到實驗室。 如果要附加另一個共用映射庫，請先分離當前映射庫，然後再附加新映射庫。 要從實驗室分離共用圖像庫，請在工具列上選擇 **"分離"，** 並確認分離操作。 

![從實驗室帳戶分離共用圖像庫](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>後續步驟
要瞭解如何將實驗室圖像保存到共用圖像庫或使用共用圖像庫中的圖像創建 VM，請參閱[如何使用共用圖像庫](how-to-use-shared-image-gallery.md)。

有關共用圖像庫的詳細資訊，請參閱[共用圖像庫](../../virtual-machines/windows/shared-image-galleries.md)。
