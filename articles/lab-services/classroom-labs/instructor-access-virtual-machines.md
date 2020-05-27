---
title: 教師如何存取 Azure 實驗室服務中的 VM
description: 本文說明教師可以如何從教師檢視中存取其學生 VM。 例如，助教可能是一個課程中的教師，但也可能是其他課程的學生。
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
ms.openlocfilehash: 449ff8aafd6dec1e9c0ff16dc407155949fd1313
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586162"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>以學生身分從教師檢視中存取虛擬機器
本文說明教師可以如何存取其作為學生參與的課程 VM。 

以下是此功能可提供協助的案例。 某位助教是一個課程的教師，但在其他課程中則是學生。 而助教想要從顯示所擁有實驗室的教師檢視中，查看並存取學生 VM。 

## <a name="access-vms-from-educator-view"></a>從教師檢視存取 VM

1. 登入 [Azure 實驗室服務網站](https://labs.azure.com)。 您會看到您擁有的實驗室。 這些實驗室可能是您自行建立的實驗室，或是管理員指派給您並讓您成為擁有者的實驗室。 如需詳細資訊，請參閱[如何將其他擁有者新增至現有的實驗室](how-to-add-user-lab-owner.md)
2. 若要存取您以學生身分參與的課程 VM，請選取右上角的電腦圖示。 確認您有看到可作為學生存取的 VM。 在下列範例中，使用者是 Python 實驗室的助教，但是是 Java 實驗室的學生。 因此，使用者會在下拉式清單中看到 Java 實驗室的 VM。 使用者可以啟動 VM 並與之連線。 
    
    ![存取學生 VM](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [連接到 VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [在 Mac 上使用 RDP 連線至 VM](connect-virtual-machine-mac-rdp.md)
- [使用 Linux 虛擬機器的遠端桌面](how-to-use-remote-desktop-linux-student.md)
