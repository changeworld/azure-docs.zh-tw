---
title: 允許實驗室建立者在 Azure 實驗室服務中選擇位置
description: 本文介紹實驗室帳戶管理員如何允許實驗室建立者為其實驗室選擇位置。
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
ms.openlocfilehash: 52d5628698d1f945a7f672595ee7ce4739b6d13c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444361"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>允許實驗室建立者在 Azure 實驗室服務中選擇實驗室的位置
在 Azure 實驗室服務中，實驗室帳戶擁有者可以允許實驗室建立者（教育者）為其創建的實驗室選擇位置。 此位置可能不同于實驗室帳戶的位置。 位置是一組 Azure 區域。 例如，美國位置是一組區域，如美國東部、美國西部等。 

作為實驗室帳戶擁有者，您可以選擇"**允許實驗室建立者在**創建實驗室帳戶時以及創建實驗室帳戶（或現有實驗室帳戶）後選擇實驗室位置選項。 

## <a name="at-the-time-of-lab-account-creation"></a>在創建實驗室帳戶時
創建實驗室帳戶時，在第一個螢幕上看到此選項（**基本選項卡**）。 

![在實驗室創建時啟用該選項](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

如果在 **"高級"** 選項卡中為實驗室帳戶選擇對等虛擬網路，則禁用此選項。  

![啟用對等虛擬網路時](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>創建實驗室帳戶後
創建實驗室帳戶後，可以通過以下步驟啟用或禁用此選項： 

1. 在 **"實驗室帳戶"** 頁上，選擇左側功能表上的 **"實驗室"設置**。
2. 如果要允許實驗室建立者為實驗室選擇位置，請選擇"**允許實驗室建立者選擇實驗室位置**"選項。 如果禁用，則實驗室將自動創建在實驗室帳戶存在的同一位置。 
    
    當您為**對等虛擬網路**欄位選擇虛擬網路時，此欄位將禁用。 這是因為實驗室帳戶中的實驗室必須與實驗室帳戶位於同一區域，以便它們訪問對等虛擬網路中的資源。 
1. 在工具列上選取 [儲存]****。 

    ![實驗室設置](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>不允許虛擬網路和位置選擇
在這種情況下，您尚未啟用 **"允許實驗室建立者選擇實驗室位置**"選項。 

![無實驗室位置](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

然後，實驗室建立者（教育工作者）看不到選擇實驗室位置的選項。 他們將看到每個尺寸選項每小時的價格。 創建實驗室時，將在 Azure 區域中創建該實驗室，該區域位於其實驗室帳戶位於的 Azure 區域的同一位置。 例如，如果實驗室帳戶**位於美國西部**，則實驗室可能**在美國中南部**創建，但不會**在加拿大東部**創建。 我們不保證我們選擇的區域，除了它的位置。 如果大小當前受到限制，則實驗室建立者將看到一個核取方塊，其中他們可以看到我們通常支援但當前不可用的大小。 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>不允許在虛擬網路和位置選擇中
在這種情況下，**禁用"允許實驗室建立者選擇實驗室位置**"選項，因為您為實驗室帳戶選擇了對等虛擬網路。 然後，實驗室建立者將看到與前一個選項相同的螢幕。 由於所有 VM 都必須與虛擬網路位於同一 Azure 區域中，因此將在虛擬網路位於的同一 Azure 區域中創建實驗室。 如果該特定區域的大小受到限制，則大小將顯示為不可用。 

## <a name="location-selection-is-enabled"></a>啟用位置選擇
當您選擇 **"允許實驗室建立者選擇實驗室位置"** 時，實驗室建立者（教育者）將看到一個選項，用於在創建實驗室時選擇位置。 

![選擇實驗室位置](../media/allow-lab-creator-pick-lab-location/location-selection.png)

實驗室建立者查看大小所在的所有位置的價格範圍，並可以選擇位置。 該實驗室將在映射到該位置的任何 Azure 區域中創建。

如果位置受約束，預設情況下不會顯示在清單中。 展開下拉清單，然後選擇 **"顯示此大小的不可用位置**"。 

![顯示不可用的位置](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>成本
以前，定價基於您為實驗室選擇的 VM 大小。 現在，價格基於作業系統 （OS）、大小和位置的組合。 

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [將實驗室網路與對等虛擬網路連接](how-to-connect-peer-virtual-network.md)
- [將共用圖像庫附加到實驗室](how-to-attach-detach-shared-image-gallery.md)
- [將使用者添加為實驗室擁有者](how-to-add-user-lab-owner.md)
- [查看實驗室的防火牆設置](how-to-configure-firewall-settings.md)
- [為實驗室配置其他設置](how-to-configure-lab-accounts.md)