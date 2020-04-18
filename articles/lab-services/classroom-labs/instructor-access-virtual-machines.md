---
title: 存取 Azure 實驗室服務中的 VM 的講師
description: 本文演示如何教師從講師視圖中訪問其學生 VM。 例如,助教可以是一個班級的講師,也可以是其他班級的學生。
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
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641936"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>從講師檢視中以學生身份訪問虛擬機器
本文介紹教師如何訪問他們的 VM 參加他們作為學生參加的課程。 

下面是一個此功能將提供説明的方案。 助教是一個班級的教師,但其他班的學生。 而且,教師希望從顯示自己擁有的實驗室的講師視圖中查看和訪問學生 VM。 

## <a name="access-vms-from-instructor-view"></a>從講師檢視存取 VM

1. 登入 Azure[實驗室服務網站](https://labs.azure.com)。 你看到你擁有的實驗室 這些實驗室可能是您自己創建的實驗室,也可以是管理員指派為您分配的實驗室的擁有者。 有關詳細資訊,請參閱[如何向現有實驗室添加其他擁有者](how-to-add-user-lab-owner.md)
2. 要訪問您作為學生參加的班級的 VM,請選擇右上角的計算機圖示。 確認您看到可作為學生訪問的 VM。 在下面的示例中,使用者是 Python 實驗室的助教,但也是 JAVA 實驗室的學生。 因此,使用者在下拉清單中看到來自 Java 實驗室的 VM。 用戶可以啟動 VM 並連接到它。 
    
    ![存取學生 VM](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [連接到 VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [在 Mac 上使用 RDP 連線至 VM](connect-virtual-machine-mac-rdp.md)
- [將遠端桌面用於 Linux 虛擬機器](how-to-use-remote-desktop-linux-student.md)
