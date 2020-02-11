---
title: 在中斷連線 Azure 實驗室服務時啟用 Vm 的關機 |Microsoft Docs
description: 瞭解如何在遠端桌面連線中斷連線時，啟用或停用 Vm 的自動關閉。
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
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117124"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>啟用中斷連線時自動關閉 Vm
本文說明如何在遠端桌面連線中斷連線之後，啟用或停用**Windows 10**實驗室 vm （範本或學生）的自動關閉。 您也可以指定 Vm 在自動關機之前，應等候多久的時間才能重新連線。

實驗室帳戶管理員可以針對您建立實驗室的實驗室帳戶，設定此設定。 如需詳細資訊，請參閱針對[實驗室帳戶在中斷連線時設定自動關閉 vm](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect)。 身為實驗室擁有者，您可以在建立實驗室時或在建立實驗室之後覆寫設定。 

## <a name="configure-when-creating-a-lab"></a>在建立實驗室時設定
在 [實驗室建立嚮導] 的 [步驟 3] 頁面上，您可以啟用或停用這項功能，同時指定 VM 應等候使用者重新連線多久，然後才會自動關閉。 

![在建立實驗室時設定](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>在建立實驗室之後設定
您可以在 [**設定**] 頁面上設定這項設定，如下圖所示： 

![在建立實驗室之後設定](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> 如果您在中斷 RDP 會話與 VM 的連線之前，先關閉 VM 上的 Windows 作業系統（OS），autoshutdown 功能將無法正常運作。  

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [教室實驗室的儀表板](use-dashboard.md)