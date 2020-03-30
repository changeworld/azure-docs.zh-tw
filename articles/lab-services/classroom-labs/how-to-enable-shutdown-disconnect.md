---
title: 在斷開連接 Azure 實驗室服務時啟用 VM 的關閉 |微軟文檔
description: 瞭解如何在遠端桌面連線斷開連接時啟用或禁用 VM 的自動關閉。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117124"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>在斷開連接時自動關閉 VM
本文介紹如何在遠端桌面連線斷開連接後啟用或禁用**Windows 10**實驗室 VM（範本或學生）的自動關閉。 您還可以指定 VM 應等待使用者重新連接的時間，然後再自動關閉。

實驗室帳戶管理員可以為創建實驗室的實驗室帳戶配置此設置。 有關詳細資訊，請參閱[為實驗室帳戶在斷開連接時配置 VM 的自動關閉](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect)。 作為實驗室擁有者，您可以在創建實驗室時或在創建實驗室後覆蓋該設置。 

## <a name="configure-when-creating-a-lab"></a>創建實驗室時配置
在實驗室創建嚮導的步驟 3 頁上，您可以啟用或禁用此功能，並指定 VM 應等待使用者重新連接的時間，然後再自動關閉。 

![在創建實驗室時進行配置](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>創建實驗室後配置
您可以在 **"設置"** 頁上配置此設置，如下圖所示： 

![創建實驗室後配置](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> 如果在將 RDP 會話斷開為 VM 之前關閉 VM 上的 Windows 作業系統 （OS），則自動關機功能將無法正常工作。  

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [教室實驗室儀表板](use-dashboard.md)