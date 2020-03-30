---
title: Azure 實驗室服務的防火牆設定
description: 瞭解如何確定實驗室中虛擬機器的公共 IP 位址和埠號範圍，以便將資訊添加到防火牆規則中。
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443445"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure 實驗室服務的防火牆設定

每個組織或學校都將以最適合其需求的方式建立自己的網路。  有時，這包括設置防火牆規則，以阻止遠端桌面協定 （rdp） 或安全外殼 （ssh） 連接到其網路外部的電腦。  由於 Azure 實驗室服務在公共雲中運行，因此可能需要一些額外的配置，以允許學生在從園區網路連接時訪問其 VM。

每個實驗室使用單個公共 IP 位址和多個埠。  所有 VM（範本 VM 和學生 VM）都將使用此公共 IP 位址。  公共 IP 位址在實驗室生命週期內不會更改。  但是，每個 VM 將具有不同的埠號。  埠號範圍為 49152 到 65535。  公共 IP 位址和埠號的組合用於將教師和學生連接到正確的 VM。  本文將介紹如何查找實驗室使用的特定公共 IP 位址。  這些資訊可用於更新入站和出站防火牆規則，以便學生可以訪問其 VM。

>[!IMPORTANT]
>每個實驗室將具有不同的公共 IP 位址。

## <a name="find-public-ip-for-a-lab"></a>查找實驗室的公共 IP

每個實驗室的公共 IP 位址列在實驗室服務實驗室帳戶的 **"所有實驗室**"頁面。  有關如何查找 **"所有實驗室"** 頁面的說明，請參閱[如何管理實驗室帳戶中的實驗室](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)。  

> [!div class="mx-imgBorder"]
> ![所有實驗室頁面](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>如果實驗室的範本電腦尚未發佈，則看不到公共 IP 位址。

## <a name="conclusion"></a>結論

現在我們知道實驗室的公共 IP 位址。  可以為組織的公共 ip 位址和埠範圍 49152-65535 創建入站和出站規則。  更新規則後，學生無需網路防火牆阻止訪問即可訪問其 VM。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [允許實驗室建立者挑選實驗室位置](allow-lab-creator-pick-lab-location.md)
- [將實驗室網路與對等虛擬網路連接](how-to-connect-peer-virtual-network.md)
- [將共用圖像庫附加到實驗室](how-to-attach-detach-shared-image-gallery.md)
- [將使用者添加為實驗室擁有者](how-to-add-user-lab-owner.md)
- [查看實驗室的防火牆設置](how-to-configure-firewall-settings.md)
- [為實驗室配置其他設置](how-to-configure-lab-accounts.md)
