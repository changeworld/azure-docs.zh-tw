---
title: 在 Azure 實驗室服務中指定實驗室的 marketplace 映射
description: 本文說明如何指定實驗室建立者可用來在 Azure 實驗室服務中的實驗室帳戶中建立實驗室的 Marketplace 映射。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5a353394fec3dadf0f808a25ec66f6e9028820a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445605"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>指定實驗室建立者可用的 Marketplace 映像
身為實驗室帳戶擁有者的您，可以指定實驗室建立者可用來在實驗室帳戶中建立實驗室的 Marketplace 映像。 

## <a name="select-images-available-for-labs"></a>選取可供實驗室使用的映射
選取左側功能表上的 [Marketplace 映像]****。 根據預設，您會看到映像 (包括已啟用和停用) 的完整清單。 您可以從頂端的下拉式清單中**選取 [僅**限 / **已停用**] 選項，以篩選清單只查看已啟用/已停用的影像。 
    
![Marketplace 映像頁面](./media/tutorial-setup-lab-account/marketplace-images-page.png)

在清單中顯示的是滿足下列條件的 Marketplace 映像：
    
- 建立單一 VM。
- 使用 Azure Resource Manager 來佈建 VM
- 不需要購買額外的授權方案

## <a name="disable-images-for-a-lab"></a>停用實驗室的映射 
若要停用實驗室的單一影像，請選取最後一個資料行中的 [ **...] (省略號) ** ，然後選取 [ **停用影像**]。 

![停用一個映像](./media/tutorial-setup-lab-account/disable-one-image.png) 

或者，您也可以選取映射名稱前面的核取方塊，然後選取工具列上的 [ **停用選取的映射** ]。 

若要同時停用多個映射，請選取映射名稱前面的核取方塊，然後選取工具列上的 [ **停用選取的影像** ]。 

![停用多個映像](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>啟用實驗室的映射
若要啟用已停用的映射，請選取最後一個資料行中的 [ **...] (省略號) ** ，然後選取 [ **啟用影像**]。 或者，您也可以選取映射名稱前面的核取方塊，然後在工具列上選取 [ **啟用選取的映射** ]。 

若要同時停用多個映射，請選取影像名稱前面的核取方塊，然後選取工具列上的 [ **啟用選取的影像** ]。 

## <a name="enable-images-at-the-time-of-lab-creation"></a>在建立實驗室時啟用映射
您可以在建立實驗室時啟用更多映射： 

1. 使用**實驗室帳戶擁有**者認證登入[Azure 實驗室服務網站](https://labs.azure.com)
2. 選取預設虛擬機器映射或向下箭號。 
3. 選取 [ **啟用更多映射選項**]。 

    ![啟用更多映射選項](./media/specify-marketplace-images/enable-more-images-menu.png)
4. 依照上一節中的指示，啟用您所選取的映射。 
5. 您可能需要關閉新的 **實驗室** 視窗，然後重新開啟它，才能看到您在上一個步驟中選取的影像。 



## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
