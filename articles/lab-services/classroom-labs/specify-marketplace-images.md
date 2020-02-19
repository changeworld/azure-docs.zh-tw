---
title: 為 Azure 實驗室服務中的實驗室指定 marketplace 映射
description: 本文說明如何指定實驗室建立者可用來在 Azure 實驗室服務的實驗室帳戶中建立實驗室的 Marketplace 映射。
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: ad56041f853d030e3a286610fe4872bffecaee12
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444663"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>指定實驗室建立者可用的 Marketplace 映像
身為實驗室帳戶擁有者的您，可以指定實驗室建立者可用來在實驗室帳戶中建立實驗室的 Marketplace 映像。 

## <a name="select-images-available-for-labs"></a>選取可供實驗室使用的映射
選取左側功能表上的 [Marketplace 映像]。 根據預設，您會看到映像 (包括已啟用和停用) 的完整清單。 您可以從頂端的下拉式清單中選取 [僅限已啟用]/[僅限已停用] 選項來篩選清單，而僅檢視已啟用/已停用的映像。 
    
![Marketplace 映像頁面](../media/tutorial-setup-lab-account/marketplace-images-page.png)

在清單中顯示的是滿足下列條件的 Marketplace 映像：
    
- 建立單一 VM。
- 使用 Azure Resource Manager 來佈建 VM
- 不需要購買額外的授權方案

## <a name="disable-images-for-a-lab"></a>停用實驗室的映射 
若要停用實驗室的單一映射，請選取 [ **...]（省略號）** 在最後一個資料行中，選取 [**停用映射**]。 

![停用一個映像](../media/tutorial-setup-lab-account/disable-one-image.png) 

或者，您可以選取映射名稱前面的核取方塊，然後選取工具列上的 [**停用選取的映射**]。 

若要同時停用多個映射，請選取映射名稱前面的核取方塊，然後選取工具列上的 [**停用選取的映射**]。 

![停用多個映像](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>啟用實驗室的映射
若要啟用已停用的映射，請選取 [ **...]（省略號）** 在最後一個資料行中，選取 [**啟用映射**]。 或者，您可以選取映射名稱前面的核取方塊，然後選取工具列上的 [**啟用選取的映射**]。 

若要同時停用多個映射，請選取映射名稱前面的核取方塊，然後選取工具列上的 [**啟用選取的映射**]。 

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
