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
ms.date: 05/19/2020
ms.author: spelluru
ms.openlocfilehash: 57005bc18d1eda42043236086ebbac69e54cc505
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655775"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>在 Azure 實驗室服務中建立和管理教室範本
實驗室中的範本是所有使用者的虛擬機器據以建立的基底虛擬機器映像。 設定範本虛擬機器以便能確實設定您要提供給實驗室使用者的虛擬機器。 您可以提供要給實驗室使用者查看的範本名稱與描述。 然後，發佈範本以便讓範本 VM 的執行個體可供實驗室使用者使用。 當您發佈範本時，Azure 實驗室服務會使用範本在實驗室中建立 VM。 在此程序中建立的 VM 數目與實驗室中允許的使用者數量上限相同 (此上限可在實驗室的使用原則中設定)。 所有虛擬機器都有與範本相同的設定。

本文說明如何在 Azure 實驗室服務的教室實驗室中，建立和管理範本虛擬機器。 

> [!NOTE]
> 範本 VM 在建立實驗室時即已建立，但不會啟動。 您可啟動範本 VM、連線到範本 VM，然後安裝實驗室的任何必要軟體，然後發佈範本 VM。 當發佈範本 VM 時，如果尚未套用自動關機，則系統會自動關機。 
> 
> 範本 VM 會在執行時產生**成本**，因此確保當您不需要範本 VM 執行時，其已關閉。 


## <a name="set-or-update-template-title-and-description"></a>設定或更新範本標題和描述
使用下列步驟，第一次設定標題和描述，且稍後加以更新。 

1. 在 [範本] 頁面上，輸入實驗室的新**標題**。  
2. 輸入範本的新**描述**。 當您將焦點移出文字方塊時，其便會自動儲存。 

    ![範本名稱和描述](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>更新範本 VM
使用下列步驟來更新範本 VM。  

1. 等待範本 VM 啟動，然後在工具列上選取 [連線至範本]，以連線至範本 VM，並遵循指示進行。 如果是 Windows 機器，您會看到下載 RDP 檔案的選項。 
1. 當您連線到範本並進行變更後，其設定即不會再與上次向使用者發佈的虛擬機器設定相同。 在您再次發佈之前，範本變更不會反映在使用者的現有虛擬機器上。

    ![連線至範本 VM](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. 安裝學生使用實驗室所需的任何軟體 (例如，Visual Studio、Azure 儲存體總管等)。 
1. 中斷範本 VM 的連線 (關閉您的遠端桌面工作階段)。 
1. 選取 [停止範本] 以**停止**範本 VM。 
1. 遵循下一節的步驟來**發佈**已更新的範本 VM。 

## <a name="publish-the-template-vm"></a>發佈範本 VM  
在此步驟中，請發佈範本 VM。 當您發佈範本 VM 時，Azure 實驗室服務會使用範本在實驗室中建立 VM。 所有虛擬機器都有與範本相同的設定。


1. 在 [範本] 頁面上，選取工具列上的 [發佈]。 

    ![[發佈範本] 按鈕](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 在您發佈時，即無法取消發佈。 
2. 在 [發佈範本] 頁面上，輸入您要在實驗室中建立的虛擬機器數目，然後選取 [發佈]。 

    ![發佈範本 - VM 的數目](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. 您會在頁面上看到範本的**發佈狀態**。 此程序最多可能需要一小時。 

    ![發佈範本 - 進度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 等候發佈完成，然後藉由選取左側功能表上的 [虛擬機器] 或選取 [虛擬機器] 圖格，以切換到 [虛擬機器集區] 頁面。 確認您看到處於 [未指派] 狀態的虛擬機器。 這些虛擬機器尚未指派給任何學生。 它們應處於 [已停止] 狀態。 您可以在此頁面上啟動學生 VM、連線到 VM、停止 VM，以及刪除 VM。 您可以在此頁面啟動 VM，或是讓學生啟動 VM。 

    ![已停止狀態的虛擬機器](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
