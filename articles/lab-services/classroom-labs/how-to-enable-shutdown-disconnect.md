---
title: 在已中斷連線的 Azure 實驗室服務上啟用 VM 的關機 | Microsoft Docs
description: 了解如何在遠端桌面連線已中斷時，啟用或停用 VM 的自動關機。
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: b0c7f5daa6bcd9ab5cb8f4d7b1a513a15cd1c708
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591704"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>在中斷連線時啟用 VM 的自動關機
本文說明如何在遠端桌面連線中斷之後，啟用或停用 **Windows 10** 實驗室 VM (範本或學生) 的自動關機。 您也可以指定 VM 在自動關機之前，應該等候使用者重新連線的時間長度。

實驗室帳戶管理員可以針對您用來建立實驗室的實驗室帳戶進行此設定。 如需詳細資訊，請參閱[針對實驗室帳戶設定已中斷連線時的 VM 自動關機](how-to-configure-lab-accounts.md)。 身為實驗室擁有者，您可以在建立實驗室時或在實驗室建立好之後覆寫設定。 

## <a name="configure-when-creating-a-lab"></a>在建立實驗室時進行設定
在實驗室建立精靈的步驟 3 頁面上，您可以啟用或停用這項功能，並且也可以指定 VM 應等候多久讓使用者重新連線，過後才自動關機。 

![在建立實驗室時進行設定](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>在實驗室建立好之後進行設定
您可以在 [設定] 頁面上進行這項設定，如下圖所示： 

![在實驗室建立好之後進行設定](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> 如果您在中斷 RDP 工作階段與 VM 的連線之前，就先關閉 VM 上的 Windows 作業系統 (OS)，自動關機功能將無法正常運作。  

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [教室實驗室的儀表板](use-dashboard.md)