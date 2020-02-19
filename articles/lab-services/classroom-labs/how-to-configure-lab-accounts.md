---
title: 在 Azure 實驗室服務中設定實驗室帳戶 |Microsoft Docs
description: 本文說明如何在 Azure 實驗室服務中建立實驗室帳戶、查看所有實驗室帳戶，或刪除實驗室帳戶。
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
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443411"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>在 Azure 實驗室服務中設定實驗室帳戶 
在 Azure 實驗室服務中，實驗室帳戶是適用于受控實驗室類型的容器，例如教室實驗室。 系統管理員會使用 Azure 實驗室服務來設定實驗室帳戶，並向可以在帳戶中建立實驗室的實驗室擁有者提供存取權。 

本文說明如何執行下列工作： 

- 為實驗室中的 Vm 指定位址範圍
- 在中斷連線時設定自動關閉 Vm

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>為實驗室中的 Vm 指定位址範圍
下列程式中的步驟可為實驗室中的 Vm 指定位址範圍。 如果您更新您先前指定的範圍，修改過的位址範圍僅適用于在進行變更之後所建立的 Vm。 

當您指定應記住的位址範圍時，以下是一些限制。 

- 前置詞必須小於或等於23。 
- 如果虛擬網路是對等互連至實驗室帳戶，則提供的位址範圍不能與對等互連虛擬網路中的位址範圍重迭。

1. 在 [**實驗室帳戶**] 頁面上，選取左側功能表上的 [**實驗室設定**]。
2. 針對 [**位址範圍**] 欄位，指定要在實驗室中建立之 vm 的位址範圍。 位址範圍應該是無類別網域間路由（CIDR）標記法（例如： 10.20.0.0/23）。 實驗室中的虛擬機器將會建立在此位址範圍內。
3. 在工具列上選取 [儲存]。 

    ![設定位址範圍](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>在中斷連線時自動關閉 Vm
您可以在遠端桌面連線中斷連線之後，啟用或停用 Windows 實驗室 Vm （範本或學生）的自動關閉。 您也可以指定 Vm 在自動關機之前，應等候多久的時間才能重新連線。

![實驗室帳戶的自動關閉設定](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

此設定適用于在實驗室帳戶中建立的所有實驗室。 實驗室擁有者可以在實驗室層級覆寫此設定。 在實驗室帳戶對此設定進行的變更，只會影響在進行變更之後所建立的實驗室。

若要瞭解實驗室擁有者如何在實驗室層級進行這項設定，請參閱[這篇文章](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [允許實驗室建立者選擇實驗室位置](allow-lab-creator-pick-lab-location.md)
- [將您的實驗室網路與對等虛擬網路連線](how-to-connect-peer-virtual-network.md)
- [將共用映射資源庫連結至實驗室](how-to-attach-detach-shared-image-gallery.md)
- [將使用者新增為實驗室擁有者](how-to-add-user-lab-owner.md)
- [查看實驗室的防火牆設定](how-to-configure-firewall-settings.md)
