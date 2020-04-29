---
title: 在 Azure 實驗室服務中存取 Vm 的講師
description: 本文說明講師如何從講師的觀點來存取其學生 Vm。 例如，「教學小幫手」可以是一個類別的講師，另一個則是其他類別的學生。
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641936"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>從講師 view 以學生身分存取虛擬機器
本文說明講師可以如何存取其作為學生出席之類別的 Vm。 

以下是這項功能可提供協助的案例。 「教學小幫手」是一個類別的講師，另一個則是其他類別中的學生。 而且，講師想要從顯示他們所擁有之實驗室的講師觀點，觀看並存取學生 Vm。 

## <a name="access-vms-from-instructor-view"></a>從講師 view 存取 Vm

1. 登入[Azure 實驗室服務網站](https://labs.azure.com)。 您會看到您擁有的實驗室。 這些實驗室可能是您自行建立的實驗室，或是系統管理員指派給您做為擁有者的實驗室。 如需詳細資訊，請參閱[如何將其他擁有者新增至現有的實驗室](how-to-add-user-lab-owner.md)
2. 若要存取您以學生身分參加之類別的 Vm，請選取右上角的 [電腦] 圖示。 確認您看到可作為學生存取的 Vm。 在下列範例中，使用者是 Python 實驗室的「教學助理」，但是 JAVA 實驗室的學生。 因此，使用者會在下拉式清單中從 JAVA 實驗室看到 VM。 使用者可以啟動 VM 並與其連線。 
    
    ![存取學生 Vm](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [連接到 VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [在 Mac 上使用 RDP 連線至 VM](connect-virtual-machine-mac-rdp.md)
- [使用 Linux 虛擬機器的遠端桌面](how-to-use-remote-desktop-linux-student.md)
