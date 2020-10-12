---
title: 針對 Azure 實驗室服務中的教室實驗室 Vm 重設密碼 |Microsoft Docs
description: 瞭解如何在 Azure 實驗室服務的教室實驗室中，為虛擬機器 (Vm) 重設密碼。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d63d14da8859c12fa797a04fa9a3c3230219adab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445333"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>為教室實驗室中的虛擬機器設定或重設密碼 (學生) 
本文會說明學生如何設定/重設其 Vm 的密碼。 

## <a name="enable-resetting-of-passwords"></a>啟用密碼重設
建立實驗室時，實驗室擁有者可以啟用或停 **用所有虛擬機器的相同密碼**。 如果啟用此選項，學生將無法重設密碼。 實驗室中的所有 Vm 都會具有講師所設定的相同密碼。 

如果停用此選項，使用者將必須在第一次嘗試連線到 VM 時設定密碼。 學生也可以稍後再重設密碼，如本文最後一節所示。 

## <a name="reset-password-for-the-first-time"></a>第一次重設密碼
如果已停**用 [針對所有虛擬機器使用相同的密碼**] 選項，當使用者 (學生) 選取 [我的**虛擬機器**] 頁面上 [實驗室 **]** 磚上的 [連線] 按鈕時，使用者會看到下列對話方塊來設定 VM 的密碼： 

![重設學生的密碼](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>稍後重設密碼
學生也可以按一下 [實驗] 磚上 (**垂直三個點**) ，然後選取 [ **重設密碼**]，來設定密碼。 

![稍後重設密碼](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>後續步驟
若要瞭解實驗室擁有者可以設定的其他學生使用方式選項，請參閱下列文章： [設定學生的使用](how-to-configure-student-usage.md)方式。
