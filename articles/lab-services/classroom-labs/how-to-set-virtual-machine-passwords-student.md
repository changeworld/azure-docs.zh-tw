---
title: 在 Azure 實驗室服務中重置教室實驗室 VM 的密碼 |微軟文檔
description: 瞭解如何在 Azure 實驗室服務的教室實驗室中重置虛擬機器 （VM） 的密碼。
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73583665"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>設置或重置教室實驗室（學生）中的虛擬機器密碼
本文介紹學生如何為其 VM 設置/重置密碼。 

## <a name="enable-resetting-of-passwords"></a>啟用重置密碼
在創建實驗室時，實驗室擁有者可以為所有虛擬機器啟用或禁用**使用相同的密碼**。 如果啟用此選項，學生無法重置密碼。 實驗室中的所有 VM 都將具有講師設置的相同密碼。 

如果禁用此選項，使用者在首次嘗試連接到 VM 時必須設置密碼。 學生還可以稍後隨時重置密碼，如本文最後一節所示。 

## <a name="reset-password-for-the-first-time"></a>首次重置密碼
如果禁用了 **"對所有虛擬機器使用相同的密碼"** 選項，當使用者（學生）選擇 **"我的虛擬機器**"頁上實驗室磁貼上的 **"連接**"按鈕時，使用者將看到以下對話方塊來設置 VM 的密碼： 

![重置學生密碼](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>稍後重置密碼
學生還可以通過按一下實驗室磁貼上的溢出功能表（**垂直三個點**）並選擇 **"重置密碼**"來設置密碼。 

![稍後重置密碼](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>後續步驟
要瞭解實驗室擁有者可以配置的其他學生使用選項，請參閱以下文章：[配置學生使用方式](how-to-configure-student-usage.md)。
