---
title: 在 Azure 實驗室服務（腳本）中的範本 VM 上啟用嵌套虛擬化 |微軟文檔
description: 瞭解如何創建包含多個 VM 的範本 VM。  換句話說，在 Azure 實驗室服務中的範本 VM 上啟用嵌套虛擬化。
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503032"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>使用腳本在 Azure 實驗室服務中的範本虛擬機器上啟用嵌套虛擬化

嵌套虛擬化使您能夠在實驗室的範本虛擬機器內創建多 VM 環境。 發佈範本將為實驗室中的每個使用者提供一個虛擬機器，其中設置多個 VM。  有關嵌套虛擬化和 Azure 實驗室服務的詳細資訊，請參閱在[Azure 實驗室服務中的範本虛擬機器上啟用嵌套虛擬化](how-to-enable-nested-virtualization-template-vm.md)。

本文中的步驟側重于為 Windows Server 2016 或 Windows Server 2019 設置嵌套虛擬化。 您將使用腳本設置具有 Hyper-V 的範本電腦。  以下步驟將指導您瞭解如何使用[實驗室服務 Hyper-V 腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)。

>[!IMPORTANT]
>創建實驗室時，為虛擬機器大小選擇 **"大型（嵌套虛擬化）"** 或 **"中型"（嵌套虛擬化）。**  嵌套虛擬化將不起作用。  

## <a name="run-script"></a>執行指令碼

1. 如果您使用的是 Internet 資源管理器，您可能需要添加到`https://github.com`受信任的網站清單中。
    1. 開啟 Internet Explorer。
    1. 選擇齒輪圖示，然後選擇**互聯網選項**。  
    1. 此時顯示 **"互聯網選項"** 對話方塊，選擇 **"安全**"，選擇 **"受信任的網站**"，按一下 **"網站"** 按鈕。
    1. 當"**受信任的網站"** 對話方塊出現時，`https://github.com`將添加到受信任的網站清單，然後選擇"**關閉**"。

        ![信任的網站](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. 下載以下步驟中概述的 Git 存儲庫檔。
    1. 轉到[https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)。
    1. 按一下 **"克隆"或"下載"** 按鈕。
    1. 按一下**下載 ZIP**。
    1. 壓縮檔 ZIP 檔案

    >[!TIP]
    >您還可以在 中[https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)克隆 Git 存儲庫。

1. 在**管理員**模式下啟動**電源外殼**。
1. 在 PowerShell 視窗中，使用下載的腳本導航到資料夾。 如果要從存儲庫檔的頂部資料夾導航，則腳本位於`azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`。
1. 您可能需要更改執行策略才能成功運行腳本。 執行以下命令：

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. 執行指令碼：

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > 腳本可能需要重新開機電腦。 按照腳本的說明重新運行腳本，直到在輸出中看到**腳本完成**為止。
1. 不要忘記重置執行策略。 執行以下命令：

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>結論

現在，您的範本電腦已準備好創建超 V 虛擬機器。 有關如何創建超 V 虛擬機器的說明，請參閱[在 Hyper-V 中創建](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)虛擬機器。 此外，請參閱[Microsoft 評估中心](https://www.microsoft.com/evalcenter/)查看可用的作業系統和軟體。  

## <a name="next-steps"></a>後續步驟

後續步驟是設置任何實驗室的常見步驟。

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設置配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設置計畫](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [電子郵件註冊連結給學生](how-to-configure-student-usage.md#send-invitations-to-users)