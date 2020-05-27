---
title: Azure 實驗室服務的防火牆設定
description: 了解如何判斷實驗室中虛擬機器的公用 IP 位址和連接埠號碼範圍，以便將此資訊新增至防火牆規則。
author: emaher
ms.author: enewman
ms.date: 05/15/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: dcb8c0d5304f052dbe7f14386425ce67ae3b79a6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589528"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure 實驗室服務的防火牆設定

每個組織或學校都會以最符合其需要的方式設定自己的網路。  有時，這會包括設定防火牆規則來封鎖對自己網路外機器的遠端桌面通訊協定 (rdp) 或安全殼層 (ssh) 連線。  因為 Azure 實驗室服務會在公用雲端中執行，所以可能需要另外進行一些設定，才能讓學生在從校園網路連線時存取其 VM。

每個實驗室都會使用單一的公用 IP 位址和多個連接埠。  所有 VM (範本 VM 和學生 VM) 都會使用此公用 IP 位址。  在實驗室的生命週期內，此公用 IP 位址不會變更。  不過，每個 VM 都會有不同的連接埠號碼。  連接埠號碼的範圍是 49152 到 65535。  公用 IP 位址和連接埠號碼的組合可用來將講師和學生連線到正確 VM。  本文將討論如何尋找實驗室所使用的特定公用 IP 位址。  該資訊可用來更新輸入和輸出防火牆規則，讓學生可以存取其 VM。

>[!IMPORTANT]
>每個實驗室都會有不同的公用 IP 位址。

## <a name="find-public-ip-for-a-lab"></a>尋找實驗室的公用 IP

每個實驗室的公用 IP 位址會列在實驗室服務實驗室帳戶的 [所有實驗室] 頁面中。  如需如何尋找 [所有實驗室] 頁面的指示，請參閱[在實驗室帳戶中檢視實驗室](manage-labs.md#view-labs-in-a-lab-account)。  

> [!div class="mx-imgBorder"]
> ![所有實驗室頁面](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>如果您實驗室的範本機器尚未發佈，您就不會看到公用 IP 位址。

## <a name="conclusion"></a>結論

現在我們知道了實驗室的公用 IP 位址。  您可以針對公用 IP 位址和連接埠範圍 49152 - 65535，為組織的防火牆建立輸入和輸出規則。  規則更新之後，學生就可以存取其 VM，而不會遭到網路防火牆封鎖存取。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [允許實驗室建立者挑選實驗室位置](allow-lab-creator-pick-lab-location.md)
- [將實驗室的網路與對等虛擬網路連線](how-to-connect-peer-virtual-network.md)
- [將共用映像庫連結至實驗室](how-to-attach-detach-shared-image-gallery.md)
- [將使用者新增為實驗室擁有者](how-to-add-user-lab-owner.md)
- [檢視實驗室的防火牆設定](how-to-configure-firewall-settings.md)
- [設定實驗室的其他設定](how-to-configure-lab-accounts.md)
