---
title: 在 Azure 實驗室服務中配置實驗室帳戶 |微軟文檔
description: 本文介紹如何在 Azure 實驗室服務中創建實驗室帳戶、查看所有實驗室帳戶或刪除實驗室帳戶。
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
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284300"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>在 Azure 實驗室服務中配置實驗室帳戶 
在 Azure 實驗室服務中，實驗室帳戶是託管實驗室類型的容器，如課堂實驗室。 系統管理員會使用 Azure 實驗室服務來設定實驗室帳戶，並向可以在帳戶中建立實驗室的實驗室擁有者提供存取權。 

本文演示如何執行以下任務： 

- 在實驗室中為 VM 指定位址範圍
- 在斷開連接時配置 VM 的自動關機

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>在實驗室中為 VM 指定位址範圍
以下步驟具有為實驗室中的 VM 指定位址範圍的步驟。 如果更新以前指定的範圍，則修改後的位址範圍僅適用于進行更改後創建的 VM。 

以下是指定位址範圍時應牢記的一些限制。 

- 首碼必須小於或等於 23。 
- 如果將虛擬網路與實驗室帳戶對等，則提供的位址範圍無法與來自對等虛擬網路的位址範圍重疊。

1. 在 **"實驗室帳戶"** 頁上，選擇左側功能表上的 **"實驗室"設置**。
2. 對於"**位址範圍**"欄位，指定將在實驗室中創建的 VM 的位址範圍。 位址範圍應位於無類域間路由 （CIDR） 標記法中（例如：10.20.0.0/23）。 實驗室中的虛擬機器將會建立在此位址範圍內。
3. 在工具列上選取 [儲存]****。 

    ![配置位址範圍](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>斷開時自動關閉 VM
在遠端桌面連線斷開連接後，您可以啟用或禁用 Windows 實驗室 VM（範本或學生）的自動關閉。 您還可以指定 VM 應等待使用者重新連接的時間，然後再自動關閉。

![實驗室帳戶的自動關機設置](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

此設置適用于實驗室帳戶中創建的所有實驗室。 實驗室擁有者可以在實驗室級別覆蓋此設置。 對實驗室帳戶中的此設置的更改將僅影響進行更改後創建的實驗室。

要瞭解實驗室擁有者如何在實驗室級別配置此設置，請參閱[本文](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [允許實驗室建立者挑選實驗室位置](allow-lab-creator-pick-lab-location.md)
- [將實驗室網路與對等虛擬網路連接](how-to-connect-peer-virtual-network.md)
- [將共用圖像庫附加到實驗室](how-to-attach-detach-shared-image-gallery.md)
- [將使用者添加為實驗室擁有者](how-to-add-user-lab-owner.md)
- [查看實驗室的防火牆設置](how-to-configure-firewall-settings.md)
