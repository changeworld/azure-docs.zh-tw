---
title: 管理 Azure 實驗室服務中的教室實驗室範本 | Microsoft Docs
description: 了解如何在 Azure 實驗室服務中建立和管理教室實驗室範本。
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
ms.openlocfilehash: 08fbe9565356dc1b7db952fdd265770fef600ca8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989037"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>在 Azure 實驗室服務中建立和管理教室範本
實驗室中的範本是所有使用者的虛擬機器據以建立的基底虛擬機器映像。 設定範本虛擬機器以便能確實設定您要提供給實驗室使用者的虛擬機器。 您可以提供要給實驗室使用者查看的範本名稱與描述。 然後，發佈範本以便讓範本 VM 的執行個體可供實驗室使用者使用。 當您發佈範本時，Azure 實驗室服務會使用範本在實驗室中建立 VM。 在此程序中建立的 VM 數目與實驗室中允許的使用者數量上限相同 (此上限可在實驗室的使用原則中設定)。 所有虛擬機器都有與範本相同的設定。

本文說明如何在 Azure 實驗室服務的教室實驗室中，建立和管理範本虛擬機器。 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>在建立教室實驗室時發佈範本
若要瞭解如何在建立教室實驗室時發佈範本，請參閱[建立教室實驗室](how-to-manage-classroom-labs.md#create-a-classroom-lab)
 
## <a name="set-or-update-template-title-and-description"></a>設定或更新範本標題和描述
使用下列步驟，第一次設定標題和描述，且稍後加以更新。 

1. 在 [**範本**] 頁面上，輸入實驗室的新**標題**。  
2. 輸入範本的新**描述**。 當您將焦點移出文字方塊時，它會自動儲存。 

    ![範本名稱和描述](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>更新範本 VM
使用下列步驟來更新範本 VM。  

1. 等待範本 VM 啟動，然後選取工具列上的 **[連線至範本**] 以連線至範本 vm，並遵循指示進行。 如果是 Windows 電腦，您會看到下載 RDP 檔案的選項。 
1. 一旦您連接到範本並進行變更後，它將不再具有與最後發佈給使用者的虛擬機器相同的設定。 在您再次發佈之前，範本變更不會反映在使用者的現有虛擬機器上。

    ![連線至範本 VM](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. 安裝學生使用實驗室所需的任何軟體 (例如，Visual Studio、Azure 儲存體總管等)。 
1. 中斷範本 VM 的連線 (關閉您的遠端桌面工作階段)。 
1. 選取 [**停止範本**]，以**停止**範本 VM。 
1. 遵循下一節中的步驟，**發佈**已更新的範本 VM。 

## <a name="publish-the-template-vm"></a>發佈範本 VM  
如果您未在建立實驗室時發行範本，也可以之後再發佈。 在發佈之前，請先連線至範本 VM，然後為其更新軟體。 當您發佈範本時，Azure 實驗室服務會使用範本在實驗室中建立 VM。 在此程式中建立的 Vm 數目是您第一次發佈時所指定的 Vm 數目，或您在 [虛擬機器集區] 頁面上指定的數量。 所有虛擬機器都有與範本相同的設定。 

1. 在 [**範本**] 頁面上，選取工具列上的 [**發佈**]。 
1. 在 [發佈範本] 訊息方塊上檢閱訊息，然後選取 [發佈]。 此程序可能需要一些時間，視正在建立的 VM 數目而定。

    ![發佈按鈕](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > 範本發行後就無法取消發行。 但可以重新發佈範本。 
1. 您可以在 [範本] 頁面上查看發佈程式的狀態。 等候範本狀態變更為 [已發佈]。 

    ![發佈狀態](../media/how-to-create-manage-template/publish-status.png)
1. 切換到 [虛擬機器] 頁面，確認您看到虛擬機器處於 [未指派] 狀態。 這些虛擬機器尚未指派給任何學生。 請等候 VM 建立。 它們應處於 [已停止] 狀態。 您可以在此頁面上啟動學生 VM、連線到 VM、停止 VM，以及刪除 VM。 您可以在此頁面啟動 VM，或是讓學生啟動 VM。 

    ![已停止狀態的虛擬機器](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
