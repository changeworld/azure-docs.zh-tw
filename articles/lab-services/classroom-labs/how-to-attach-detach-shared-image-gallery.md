---
title: 在 Azure 實驗室服務中附加或卸離共用映射庫 |Microsoft Docs
description: 本文說明如何將共用映射資源庫連結至 Azure 實驗室服務中的教室實驗室。
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190512"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure 實驗室服務中附加或卸離共用映射資源庫
教師/實驗室系統管理員可以將範本 VM 映射儲存在 Azure[共用映射資源庫](../../virtual-machines/windows/shared-image-galleries.md)中，供其他人重複使用。 第一個步驟是，實驗室系統管理員將現有的共用映射資源庫附加至實驗室帳戶。 連結共用映射資源庫之後，在實驗室帳戶中建立的實驗室可以將影像儲存到共用映射資源庫。 其他老師可以從共用映射庫中選取此映射，以建立其類別的範本。 

當映射儲存到共用映射資源庫時，Azure 實驗室服務會將儲存的影像複寫到相同[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中可用的其他區域。 它可確保映射適用于在相同地理位置的其他區域中建立的實驗室。 將影像儲存到共用映射資源庫會產生額外的成本，包括所有複寫影像的成本。 這種成本與 Azure 實驗室服務的使用成本不同。 如需共用映射庫價格的詳細資訊，請參閱[共用映射資源庫–計費]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)。

本文說明如何將共用映射資源庫連結或卸離至實驗室帳戶。 

> [!NOTE]
> 目前，Azure 實驗室服務僅支援根據共用映射庫中的**一般化**VM 映射（非特製化映射）建立範本 vm。 


## <a name="configure-at-the-time-of-lab-account-creation"></a>在建立實驗室帳戶時設定
當您建立實驗室帳戶時，您可以將共用映射資源庫連結至實驗室帳戶。 您可以從下拉式清單中選取現有的共用映射資源庫，或建立一個新的。 若要建立共用映射資源庫並將其連結至實驗室帳戶，**請選取 [新建]** ，輸入資源庫的名稱，然後輸入 **[確定]** 。 

![在建立實驗室帳戶時設定共用映射資源庫](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>在建立實驗室帳戶後進行設定
建立實驗室帳戶之後，您可以執行下列工作：

- 建立和附加共用映射資源庫
- 將共用映射資源庫連結至實驗室帳戶
- 從實驗室帳戶卸離共用映射資源庫

## <a name="create-and-attach-a-shared-image-gallery"></a>建立和附加共用映射資源庫
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]。 選取 **DEVOPS** 區段中的 [實驗室服務]。 如果您選取 [實驗室服務]`*` **旁邊的星號 (** )，它會新增到左功能表上的 [我的最愛] 區段。 從下一次開始，您可選取 [我的最愛] 下方的 [實驗室服務]。

    ![[所有服務] -> [實驗室服務]](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 選取您的實驗室帳戶以查看 [**實驗室帳戶**] 頁面。 
4. 選取左側功能表上的 [**共用映射資源庫**]，然後選取工具列上的 [ **+ 建立**]。  

    ![建立共用映射資源庫按鈕](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. 在 [**建立共用映射資源庫**] 視窗中，輸入資源庫的**名稱**，然後輸入 **[確定]** 。 

    ![建立共用映射庫視窗](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure 實驗室服務會建立共用映射資源庫，並將其附加至實驗室帳戶。 在此實驗室帳戶中建立的所有實驗室都可以存取附加的共用映射資源庫。 

    ![附加映射庫](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    在下方窗格中，您會在共用映射資源庫中看到影像。 在這個新的圖庫中，沒有任何影像。 當您將影像上傳至資源庫時，您會在此頁面上看到它們。     

    預設會啟用 [附加的共用映射資源庫] 中的所有映射。 您可以在清單中選取影像，並使用 [**啟用選取的映射**] 或 [停用**選取的映射**] 按鈕，來啟用或停用選取的映射。

## <a name="attach-an-existing-shared-image-gallery"></a>附加現有的共用映射資源庫
下列程式說明如何將現有的共用映射資源庫連結至實驗室帳戶。 

1. 在 [**實驗室帳戶**] 頁面上，選取左側功能表上的 [**共用映射資源庫**]，然後選取工具列上的 [**附加**]。 

    ![共用映射資源庫-新增按鈕](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. 在 [**附加現有的共用映射資源庫**] 頁面上，選取您的共用映射資源庫，然後選取 **[確定]** 。

    ![選取現有的資源庫](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 您會看到下列畫面： 

    ![清單中的我的資源庫](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    在此範例中，共用映射庫中還沒有任何映射。

    Azure Lab Services 身分識別會以參與者的身分新增至附加至實驗室的共用映射資源庫。 它允許老師/IT 系統管理員將虛擬機器映射儲存到共用映射資源庫。 在此實驗室帳戶中建立的所有實驗室都可以存取附加的共用映射資源庫。 

    預設會啟用 [附加的共用映射資源庫] 中的所有映射。 您可以在清單中選取影像，並使用 [**啟用選取的映射**] 或 [停用**選取的映射**] 按鈕，來啟用或停用選取的映射。 

## <a name="detach-a-shared-image-gallery"></a>卸離共用映射資源庫
只有一個共用映射資源庫可以附加至實驗室。 如果您想要附加另一個共用映射資源庫，請先卸離目前的影像，然後再附加新的。 若要從您的實驗室卸離共用映射資源庫，請選取工具列上的 [卸**離**]，並確認卸離作業。 

![從實驗室帳戶卸離共用映射資源庫](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>後續步驟
若要瞭解如何將實驗室映射儲存到共用映射資源庫，或使用來自共用映射資源庫的映射來建立 VM，請參閱[如何使用共用映射資源庫](how-to-use-shared-image-gallery.md)。

如需共用映射資源庫的詳細資訊，請參閱[共用映射資源庫](../../virtual-machines/windows/shared-image-galleries.md)。
