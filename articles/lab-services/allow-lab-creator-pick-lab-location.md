---
title: 允許實驗室建立者在 Azure 實驗室服務中選擇位置
description: 本文說明實驗室帳戶管理員如何允許實驗室建立者挑選其實驗室的位置。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3b6c2d83414b1abc763755fbf15402b122c2186b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444194"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>允許實驗室建立者在 Azure 實驗室服務中挑選實驗室的位置
在 Azure 實驗室服務中，實驗室帳戶擁有者可以讓實驗室建立者 (教育者) 挑選他們所建立之實驗室的位置。 此位置可以不同于實驗室帳戶的位置。 位置是一組 Azure 區域。 例如，「美國地點」是一組區域，例如「美國東部」、「美國西部」等等。 

您以實驗室帳戶擁有者的身分，可以在建立實驗室帳戶時，以及在建立實驗室帳戶 (或現有的實驗室帳戶) 之後，選取 [ **允許實驗室建立者挑選實驗室位置** ] 選項。 

## <a name="at-the-time-of-lab-account-creation"></a>建立實驗室帳戶時
當您建立實驗室帳戶時，您會在第一個畫面上看到此選項， (**基本** ] 索引標籤) 。 

![在建立實驗室時啟用選項](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

如果您在 [ **Advanced** ] 索引標籤中選取實驗室帳戶的對等虛擬網路，則會停用此選項。  

![當對等互連虛擬網路啟用時](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>建立實驗室帳戶之後
建立實驗室帳戶之後，您可以遵循下列步驟來啟用或停用此選項： 

1. 在 [ **實驗室帳戶** ] 頁面上，選取左側功能表上的 [ **實驗室設定** ]。
2. 如果您想要允許實驗室建立者選取實驗室的位置，請選取 [ **允許實驗室建立者挑選實驗室位置** ] 選項。 如果已停用，則會自動在實驗室帳戶所在的相同位置中建立實驗室。 
    
    當您為 **對等虛擬網路** 欄位選取虛擬網路時，會停用此欄位。 這是因為實驗室帳戶中的實驗室必須位於與實驗室帳戶相同的區域中，才能存取對等虛擬網路中的資源。 
1. 在工具列上選取 [儲存]  。 

    ![實驗室設定](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>不允許虛擬網路和位置選取
在此案例中，您未啟用 [ **允許實驗室建立者挑選實驗室位置** ] 選項。 

![沒有實驗室位置](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

然後，實驗室建立者 (教師) 看不到挑選實驗室位置的選項。 每小時會看到其可用的每個大小選項各有價格。 當他們建立實驗室時，它會建立在與實驗室帳戶所在 Azure 區域相同位置的 Azure 區域中。 例如，如果實驗室帳戶位於 **美國西部**，則實驗室可能會在 **美國中南部** 建立，但不會在 **加拿大東部**中建立。 我們不保證我們在該區域中所選區域的任何相關資訊。 如果大小目前受到限制，實驗室建立者將會看到一個核取方塊，讓他們可以看到我們通常支援但目前無法使用的大小。 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>在虛擬網路中，不允許選取位置
在此案例中，已停用 [ **允許實驗室建立者挑選實驗室位置** ] 選項，因為您已選取實驗室帳戶的對等虛擬網路。 然後，實驗室建立者將會看到與上一個選項相同的畫面。 因為所有 Vm 都必須位於與虛擬網路相同的 Azure 區域中，所以會在虛擬網路所在的相同 Azure 區域中建立實驗室。 如果該特定區域受限於某個大小，則大小會顯示為無法使用。 

## <a name="location-selection-is-enabled"></a>已啟用位置選取
當您選取 [ **允許實驗室建立者挑選實驗室位置**]、[實驗室建立者] (教師) 查看建立實驗室時選取位置的選項。 

![挑選實驗室位置](./media/allow-lab-creator-pick-lab-location/location-selection.png)

實驗室建立者會看到大小所在的所有位置的價格範圍，並可選擇一個位置。 將在任何對應至該位置的 Azure 區域中建立實驗室。

如果某個位置受到限制，預設不會顯示在清單中。 展開下拉式清單，然後選取 [ **顯示此大小無法使用的位置**]。 

![顯示無法使用的位置](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Cost
稍早，定價是根據您為實驗室選擇的 VM 大小。 現在，價格是以作業系統 (作業系統) 、大小和位置的組合為基礎。 

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [將實驗室的網路與對等虛擬網路連線](how-to-connect-peer-virtual-network.md)
- [將共用映像庫連結至實驗室](how-to-attach-detach-shared-image-gallery.md)
- [將使用者新增為實驗室擁有者](how-to-add-user-lab-owner.md)
- [檢視實驗室的防火牆設定](how-to-configure-firewall-settings.md)
- [設定實驗室的其他設定](how-to-configure-lab-accounts.md)