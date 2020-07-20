---
title: 在 Azure 實驗室服務中重設教室實驗室 Vm 的密碼 |Microsoft Docs
description: 瞭解如何在 Azure 實驗室服務的教室實驗室中重設虛擬機器（Vm）的密碼。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d63d14da8859c12fa797a04fa9a3c3230219adab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445333"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>設定或重設課堂實驗室（學生）中虛擬機器的密碼
本文說明學生可以如何為其 Vm 設定/重設密碼。 

## <a name="enable-resetting-of-passwords"></a>啟用密碼重設
在建立實驗室時，實驗室擁有者可以啟用或停**用所有虛擬機器的相同密碼**。 如果啟用此選項，學生就無法重設密碼。 實驗室中的所有 Vm 都會有講師所設定的相同密碼。 

如果停用此選項，則使用者第一次嘗試連線到 VM 時，必須設定密碼。 學生也可以稍後再重設密碼，如本文最後一節所示。 

## <a name="reset-password-for-the-first-time"></a>第一次重設密碼
如果已停**用 [所有虛擬機器使用相同的密碼**] 選項，當使用者（學生）選取 [我的**虛擬機器**] 頁面上 [實驗室] 磚上的 [連線 **]** 按鈕時，使用者會看到下列對話方塊來設定 VM 的密碼： 

![重設學生的密碼](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>稍後重設密碼
學生也可以按一下 [實驗室] 磚上的溢位功能表（**垂直三個點**），然後選取 [**重設密碼**]，來設定密碼。 

![稍後重設密碼](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>後續步驟
若要瞭解實驗室擁有者可以設定的其他學生使用方式選項，請參閱下列文章：[設定學生使用](how-to-configure-student-usage.md)方式。
