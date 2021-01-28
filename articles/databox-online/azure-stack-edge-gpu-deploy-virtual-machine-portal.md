---
title: 如何透過 Azure 入口網站將 Vm 部署到您的 Azure Stack Edge Pro
description: 瞭解如何透過 Azure 入口網站在您的 Azure Stack Edge Pro 上建立和管理 Vm。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/02/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: a48abb34a201928185130fbe8fdf9592e77492d9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944955"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>透過 Azure 入口網站將 Vm 部署到您的 Azure Stack Edge Pro GPU 裝置

您可以使用 Azure 入口網站、範本、Azure PowerShell Cmdlet 和 Azure CLI/Python 腳本，在 Azure Stack Edge 裝置上建立及管理虛擬機器 (Vm) 。 本文說明如何使用 Azure 入口網站在 Azure Stack Edge 裝置上建立和管理 VM。 

本文適用于 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 裝置。 

> [!IMPORTANT] 
> 建議您為管理從雲端部署到您裝置上之 Vm 的使用者啟用多重要素驗證。
        
## <a name="vm-deployment-workflow"></a>VM 部署工作流程

部署工作流程的高階摘要如下所示：

1. 在您的 Azure Stack Edge 裝置上啟用用於計算的網路介面。 這會在指定的網路介面上建立虛擬交換器。
1. 從 Azure 入口網站啟用虛擬機器的雲端管理。
1. 使用儲存體總管將 VHD 上傳至 Azure 儲存體帳戶。 
1. 使用上傳的 VHD 將 VHD 下載到裝置上，並從 VHD 建立 VM 映射。 
1. 使用在先前步驟中建立的資源：
    1. 您所建立的 VM 映射。
    1. 與您已啟用計算的網路介面相關聯的 VSwitch。
    1. 與 VSwitch 相關聯的子網。

    並建立或指定下列內嵌資源：
    1. VM 名稱，選擇 VM 的支援 VM 大小和登入認證。 
    1. 建立新的資料磁片或連接現有的資料磁片。
    1. 設定 VM 的靜態或動態 IP。 如果提供靜態 IP，請從已啟用計算之網路介面的子網範圍中選擇可用的 IP。

    使用上面的資源來建立虛擬機器。


## <a name="prerequisites"></a>先決條件

在您開始透過 Azure 入口網站建立及管理裝置上的 Vm 之前，請確定：

1. 您已完成 Azure Stack Edge Pro 裝置上的網路設定，如 [步驟1：設定 Azure Stack Edge Pro 裝置](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)中所述。

    1. 您已啟用用於計算的網路介面。 此網路介面 IP 用來建立 VM 部署的虛擬交換器。 在裝置的本機 UI 中，移至 [ **計算**]。 選取您將用來建立虛擬交換器的網路介面。

        > [!IMPORTANT] 
        > 您只能設定一個連接埠進行計算。

    1. 在網路介面上啟用計算。 Azure Stack Edge Pro 會建立和管理對應至該網路介面的虛擬交換器。

1. 您可以存取 Windows 或 Linux VHD，以用來為您要建立的虛擬機器建立 VM 映射。

## <a name="deploy-a-vm"></a>部署 VM

請遵循下列步驟，在您的 Azure Stack Edge 裝置上建立虛擬機器。

### <a name="add-a-vm-image"></a>新增 VM 映射

1. 將 VHD 上傳至 Azure 儲存體帳戶。 遵循 [使用 Azure 儲存體總管上傳 VHD](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md)中的步驟。

1. 在 Azure 入口網站中，移至 Azure Stack Edge 裝置的 Azure Stack Edge 資源。 前往 **Edge 計算 > 虛擬機器**。

    ![新增 VM 映射1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. 選取 [ **虛擬機器** ] 以移至 [ **總覽** ] 頁面。 **啟用** 虛擬機器雲端管理。
    ![新增 VM 映射2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. 第一個步驟是新增 VM 映射。 您已在先前的步驟中將 VHD 上傳至儲存體帳戶。 您將使用此 VHD 來建立 VM 映射。

    選取 [ **新增映射** ] 以從儲存體帳戶下載 VHD，並將其新增至裝置。 下載程式需要幾分鐘的時間，視 VHD 的大小和可供下載的網際網路頻寬而定。 

    ![新增 VM 映射3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. 在 [ **新增映射** ] 分頁中，輸入下列參數。 選取 [新增]。


    |參數  |描述  |
    |---------|---------|
    |從儲存體 blob 下載    |流覽至您上傳 VHD 之儲存體帳戶中儲存體 blob 的位置。         |
    |下載至    | 自動設定為您要在其中部署虛擬機器的目前裝置。        |
    |將影像另存為      | 您要從上傳至儲存體帳戶的 VHD 建立的 VM 映射名稱。        |
    |OS 類型     |從 Windows 或 Linux 選擇您將用來建立 VM 映射的 VHD 作業系統。         |
   

    ![新增 VM 映射4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. VHD 會下載並建立 VM 映射。 映射建立需要幾分鐘的時間才能完成。 您會看到成功完成 VM 映射的通知。

    ![新增 VM 映射5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. 成功建立 VM 映射之後，它會新增至 [ **映射** ] 分頁中的映射清單。
    ![新增 VM 映射6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    **部署** 分頁會更新以指出部署的狀態。

    ![新增 VM 映射7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    新加入的影像也會顯示在 [ **總覽** ] 頁面中。
    ![新增 VM 映射8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>新增 VM

建立 VM 映射之後，請遵循下列步驟來建立 VM。

1. 在 [ **總覽** ] 頁面中，選取 [ **新增虛擬機器**]。

    ![新增 VM 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. 在 [ **基本** ] 索引標籤中輸入下列參數。


    |參數 |描述  |
    |---------|---------|
    |虛擬機器名稱     |         |
    |映像     | 從裝置上可用的 VM 映射進行選取。        |
    |大小     | 從支援的 [VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md)中選擇。        |
    |使用者名稱     | 使用預設的使用者名稱 *>azureuser*。        |
    |驗證類型    | 從 SSH 公開金鑰或使用者定義密碼進行選擇。       |
    |密碼     | 輸入密碼以登入虛擬機器。 密碼長度必須至少為12個字元，且符合定義的 [複雜度需求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。        |
    |確認密碼    | 再次輸入密碼。        |


    ![新增 VM 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    完成時，選取 [下一步:  磁碟]。

1. 在 [ **磁片** ] 索引標籤中，您會將磁片連接到您的 VM。 
    
    1. 您可以選擇 **建立並連接新的磁片，** 或 **連接現有的磁片**。

        ![新增 VM 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. 選取 [ **建立並附加新的磁片**]。 在 [ **建立新的磁片** ] 分頁中，提供磁片的名稱和 GiB 中的大小。

        ![新增 VM 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  重複上述步驟來新增更多磁片。 磁片建立之後，就會顯示在 [ **磁片** ] 索引標籤中。

        ![新增 VM 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        選取 [下一步：**網路]**。

1. 在 [ **網路** 功能] 索引標籤中，您將設定 VM 的網路連線能力。

    
    |參數  |描述 |
    |---------|---------|
    |虛擬網路    | 從下拉式清單中，選取當您在網路介面上啟用 [計算] 時，在 Azure Stack Edge 裝置上建立的虛擬交換器。    |
    |子網路     | 此欄位會自動填入與您啟用計算的網路介面相關聯的子網。         |
    |IP 位址     | 為您的 VM 提供靜態或動態 IP。 靜態 IP 應為指定子網範圍內可用的可用 IP。        |

    ![新增 VM 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    完成時，選取 [下一步:檢閱 + 建立]。

1. 在 [ **審核 + 建立** ] 索引標籤中，檢查 VM 的規格，然後選取 [ **建立**]。

    ![新增 VM 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. VM 建立會啟動，最多可能需要20分鐘的時間。 您可以移至 [ **部署** ] 來監視 VM 建立。

    ![新增 VM 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. 成功建立 VM 之後，[ **總覽** ] 頁面會更新以顯示新的 vm。

    ![新增 VM 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. 選取新建立的 VM 以移至 **虛擬機器**。

    ![新增 VM 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    選取 VM 以查看詳細資料。 

    ![新增 VM 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>連線至 VM

根據您建立的是 Windows 或 Linux VM，連接的步驟可能會不同。 您無法透過 Azure 入口網站連接到您裝置上部署的 Vm。 您必須採取下列步驟來連線到您的 Linux 或 Windows VM。

### <a name="connect-to-linux-vm"></a>連接至 Linux VM

遵循下列步驟以連線至 Linux VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>連接到 Windows VM

遵循下列步驟以連線至 Windows VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>後續步驟

若要瞭解如何管理您的 Azure Stack Edge Pro 裝置，請參閱[使用本機 WEB UI 來管理 Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)。
