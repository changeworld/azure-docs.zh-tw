---
title: 在 Azure 實驗室服務（腳本）中的範本 VM 上啟用「嵌套虛擬化」 |Microsoft Docs
description: 了解如何建立內有多個 VM 的範本 VM。  換句話說，就是在 Azure 實驗室服務的範本 VM 上啟用巢狀虛擬化。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a45257dffffc98c3f650c5b1753158edd9bfbede
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445741"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>使用腳本在 Azure 實驗室服務中的範本虛擬機器上啟用嵌套虛擬化

巢狀虛擬化可讓您在實驗室的範本虛擬機器內建立有多個 VM 的環境。 發佈範本會向實驗室中的每位使用者提供在其內部設定了多個 VM 的虛擬機器。  如需有關嵌套虛擬化和 Azure 實驗室服務的詳細資訊，請參閱[在 Azure 實驗室服務中的範本虛擬機器上啟用嵌套虛擬化](how-to-enable-nested-virtualization-template-vm.md)。

本文中的步驟著重于為 Windows Server 2016、Windows Server 2019 或 Windows 10 設定嵌套虛擬化。 您將使用腳本來設定具有 Hyper-v 的範本電腦。  下列步驟將引導您瞭解如何使用[實驗室服務 hyper-v 腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)。

>[!IMPORTANT]
>在建立實驗室時，請選取 [大型 (巢狀虛擬化)] 或 [中型 (巢狀虛擬化)] 作為虛擬機器大小。  否則，將無法使用巢狀虛擬化。  

## <a name="run-script"></a>執行指令碼

1. 如果您使用的是 Internet Explorer，您可能必須新增 `https://github.com` 至信任的網站清單。
    1. 開啟 Internet Explorer。
    1. 選取齒輪圖示，然後選擇 [**網際網路選項**]。  
    1. 當 [**網際網路選項**] 對話方塊出現時，選取 [**安全性**]，選取 [**信任的網站**]，按一下 [**網站**] 按鈕。
    1. 出現 [**信任的網站**] 對話方塊時，將新增 `https://github.com` 至 [信任的網站] 清單，然後選取 [**關閉**]。

        ![信任的網站](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. 如下列步驟所述，下載 Git 存放庫檔案。
    1. 移至 [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) 。
    1. 按一下 [**複製或下載**] 按鈕。
    1. 按一下 [**下載 ZIP**]。
    1. 壓縮檔 ZIP 檔案

    >[!TIP]
    >您也可以在複製 Git 存放庫 [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) 。

1. 以**系統管理員**模式啟動**PowerShell** 。
1. 在 PowerShell 視窗中，流覽至含有已下載腳本的資料夾。 如果您是從存放庫檔案的最上層資料夾流覽，則腳本位於 `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` 。
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