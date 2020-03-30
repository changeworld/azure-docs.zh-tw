---
title: 為 Azure 實驗室服務中的實驗室指定市場映射
description: 本文介紹如何指定實驗室建立者可用於在 Azure 實驗室服務中的實驗室帳戶中創建實驗室的應用商店映射。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444663"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>指定實驗室建立者可用的 Marketplace 映像
身為實驗室帳戶擁有者的您，可以指定實驗室建立者可用來在實驗室帳戶中建立實驗室的 Marketplace 映像。 

## <a name="select-images-available-for-labs"></a>選擇可用於實驗室的圖像
選取左側功能表上的 [Marketplace 映像]****。 根據預設，您會看到映像 (包括已啟用和停用) 的完整清單。 您可以通過從頂部的下拉清單中選擇/**"僅啟用"/** 禁用的"**Enabled only**選項"來篩選清單，以僅查看已啟用/禁用的圖像。 
    
![Marketplace 映像頁面](../media/tutorial-setup-lab-account/marketplace-images-page.png)

在清單中顯示的是滿足下列條件的 Marketplace 映像：
    
- 建立單一 VM。
- 使用 Azure Resource Manager 來佈建 VM
- 不需要購買額外的授權方案

## <a name="disable-images-for-a-lab"></a>禁用實驗室的圖像 
要禁用實驗室的單個圖像，請選擇 **...（省略）** 在最後一列中，並選擇 **"禁用圖像**"。 

![停用一個映像](../media/tutorial-setup-lab-account/disable-one-image.png) 

或者，選擇圖像名稱前的核取方塊，然後選擇工具列上的 **"禁用選定圖像**"。 

要同時禁用多個圖像，請在圖像名稱之前選擇核取方塊，然後選擇**工具列上的"禁用所選圖像**"。 

![停用多個映像](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>為實驗室啟用圖像
要啟用禁用的圖像，請選擇 **...（省略）** 在最後一列中，選擇 **"啟用圖像**"。 或者，選擇圖像名稱前的核取方塊，然後選擇工具列上的 **"啟用選定的圖像**"。 

要同時禁用多個圖像，請在圖像名稱之前選擇核取方塊，然後選擇工具列上的 **"啟用選定圖像**"。 

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
