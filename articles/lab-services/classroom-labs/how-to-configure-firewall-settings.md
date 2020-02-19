---
title: Azure 實驗室服務的防火牆設定
description: 瞭解如何判斷實驗室中虛擬機器的公用 IP 位址和埠號碼範圍，讓資訊可以新增至防火牆規則。
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443445"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure 實驗室服務的防火牆設定

每個組織或學校都會以最符合其需求的方式設定自己的網路。  有時，包括設定防火牆規則，以封鎖遠端桌面通訊協定（rdp）或安全殼層（ssh）連線到自己網路外的電腦。  因為 Azure 實驗室服務是在公用雲端中執行，所以可能需要一些額外的設定，才能讓學生在從校園網路連線時存取其 VM。

每個實驗室都使用單一公用 IP 位址和多個埠。  所有 Vm （範本 VM 和學生 Vm）都會使用此公用 IP 位址。  在實驗室的生命週期中，公用 IP 位址不會變更。  不過，每個 VM 都會有不同的埠號碼。  埠號碼的範圍從49152到65535。  公用 IP 位址和埠號碼的組合是用來將講師和學生連線到正確的 VM。  本文將討論如何尋找實驗室所使用的特定公用 IP 位址。  該資訊可以用來更新輸入和輸出防火牆規則，讓學生可以存取其 Vm。

>[!IMPORTANT]
>每個實驗室都有不同的公用 IP 位址。

## <a name="find-public-ip-for-a-lab"></a>尋找實驗室的公用 IP

每個實驗室的公用 IP 位址會列在實驗室服務實驗室帳戶的 [**所有實驗室**] 頁面中。  如需如何尋找 [**所有實驗室**] 頁面的指示，請參閱[如何在實驗室帳戶中管理實驗室](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)。  

> [!div class="mx-imgBorder"]
> ![所有實驗室頁面](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>如果您實驗室的範本電腦尚未發佈，您將不會看到公用 IP 位址。

## <a name="conclusion"></a>結論

現在我們知道實驗室的公用 IP 位址。  您可以為組織的防火牆建立公用 ip 位址和埠範圍49152-65535 的輸入和輸出規則。  更新規則之後，學生就可以存取其 Vm，而不會有網路防火牆封鎖存取。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [允許實驗室建立者選擇實驗室位置](allow-lab-creator-pick-lab-location.md)
- [將您的實驗室網路與對等虛擬網路連線](how-to-connect-peer-virtual-network.md)
- [將共用映射資源庫連結至實驗室](how-to-attach-detach-shared-image-gallery.md)
- [將使用者新增為實驗室擁有者](how-to-add-user-lab-owner.md)
- [查看實驗室的防火牆設定](how-to-configure-firewall-settings.md)
- [設定實驗室的其他設定](how-to-configure-lab-accounts.md)
