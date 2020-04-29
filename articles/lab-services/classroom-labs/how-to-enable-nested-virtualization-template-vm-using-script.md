---
title: 在 Azure 實驗室服務（腳本）中的範本 VM 上啟用「嵌套虛擬化」 |Microsoft Docs
description: 瞭解如何在中建立具有多個 Vm 的範本 VM。  換句話說，請在 Azure 實驗室服務的範本 VM 上啟用「嵌套虛擬化」。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503032"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>使用腳本在 Azure 實驗室服務中的範本虛擬機器上啟用嵌套虛擬化

「嵌套虛擬化」可讓您在實驗室的範本虛擬機器內建立多個 VM 環境。 發佈範本會提供實驗室中的每位使用者，並在其中設定多個 Vm 的虛擬機器。  如需有關嵌套虛擬化和 Azure 實驗室服務的詳細資訊，請參閱[在 Azure 實驗室服務中的範本虛擬機器上啟用嵌套虛擬化](how-to-enable-nested-virtualization-template-vm.md)。

本文中的步驟著重于為 Windows Server 2016 或 Windows Server 2019 設定嵌套虛擬化。 您將使用腳本來設定具有 Hyper-v 的範本電腦。  下列步驟將引導您瞭解如何使用[實驗室服務 hyper-v 腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)。

>[!IMPORTANT]
>建立實驗室時，請為虛擬機器大小選取 [**大型（嵌套虛擬化）** ] 或 [**中（嵌套虛擬化）** ]。  否則，將無法使用嵌套虛擬化。  

## <a name="run-script"></a>執行指令碼

1. 如果您使用的是 Internet Explorer，您可能必須新增`https://github.com`至信任的網站清單。
    1. 開啟 Internet Explorer。
    1. 選取齒輪圖示，然後選擇 [**網際網路選項**]。  
    1. 當 [**網際網路選項**] 對話方塊出現時，選取 [**安全性**]，選取 [**信任的網站**]，按一下 [**網站**] 按鈕。
    1. 出現 [**信任的網站**] 對話方塊時`https://github.com` ，將新增至 [信任的網站] 清單，然後選取 [**關閉**]。

        ![信任的網站](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. 如下列步驟所述，下載 Git 存放庫檔案。
    1. 移至[https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)。
    1. 按一下 [**複製或下載**] 按鈕。
    1. 按一下 [**下載 ZIP**]。
    1. 壓縮檔 ZIP 檔案

    >[!TIP]
    >您也可以在[https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)複製 Git 存放庫。

1. 以**系統管理員**模式啟動**PowerShell** 。
1. 在 PowerShell 視窗中，流覽至含有已下載腳本的資料夾。 如果您是從存放庫檔案的最上層資料夾流覽，則腳本位於`azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`。
1. 您可能必須變更執行原則，才能成功執行腳本。 執行下列命令：

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. 執行指令碼：

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > 腳本可能需要重新開機電腦。 遵循腳本中的指示並重新執行腳本，直到輸出中出現**腳本完成**為止。
1. 別忘了重設執行原則。 執行下列命令：

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>結論

現在，您的範本電腦已準備好建立 Hyper-v 虛擬機器。 如需如何建立 Hyper-v 虛擬機器的指示，請參閱[在 hyper-v 中建立虛擬機器](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)。 此外，請參閱[Microsoft 評估中心](https://www.microsoft.com/evalcenter/)以查看可用的作業系統和軟體。  

## <a name="next-steps"></a>後續步驟

下一步是設定任何實驗室的常見步驟。

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)