---
title: 準備 Azure 入口網站以部署資料箱閘道的教學課程 | Microsoft Docs
description: 部署 Azure 資料箱閘道的第一個教學課程說明如何準備 Azure 入口網站。
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 553d6f716bbb6e98aa64ef07cb80d2d6cba370b6
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561481"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>教學課程：準備部署 Azure Data Box Gateway

我們提供了一系列的部署教學課程來說明如何完整部署 Azure 資料箱閘道，這是此系列的第一個教學課程。 本教學課程說明如何準備 Azure 入口網站以部署資料箱閘道資源。

您需要有系統管理員權限，才能完成安裝和設定程序。 入口網站準備工作不到 10 分鐘就能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 建立新的資源
> * 下載虛擬裝置映像
> * 取得啟用金鑰

## <a name="get-started"></a>開始使用

若要部署資料箱閘道，請依指定的順序參閱下列教學課程。

| **#** | **在此步驟中** | **使用這些文件** |
| --- | --- | --- | 
| 1. |**[為 Azure 入口網站進行資料箱閘道的準備工作](data-box-gateway-deploy-prep.md)** |在佈建資料箱閘道虛擬裝置之前，請先建立並設定資料箱閘道資源。 |
| 2. |**[在 Hyper-V 中佈建資料箱閘道](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[在 VMware 中佈建資料箱閘道](data-box-gateway-deploy-provision-vmware.md)**|針對 Hyper-V，請在使用 Windows Server 2016 或 Windows Server 2012 R2 執行 Hyper-V 的主機系統上，佈建並連線至資料箱閘道虛擬裝置。 <br><br><br> 針對 VMware，請在執行 VMware ESXi 6.0、6.5 或 6.7 的主機系統上佈建並連線至資料箱閘道虛擬裝置。<br></br> |
| 3. |**[連線、設定、啟動資料箱閘道](data-box-gateway-deploy-connect-setup-activate.md)** |連線至本機 Web UI、完成裝置設定，並啟動裝置。 接下來，您可以佈建 SMB 共用。  |
| 4. |**[使用資料箱閘道傳輸資料](data-box-gateway-deploy-add-shares.md)** |新增共用，並透過 SMB 或 NFS 連線至共用。 |

現在您可以開始設定 Azure 入口網站。

## <a name="prerequisites"></a>Prerequisites

我們在此提供資料箱閘道資源、資料箱閘道裝置和資料中心網路的設定必要條件。

### <a name="for-the-data-box-gateway-resource"></a>針對資料箱閘道資源

在您開始前，請確定：

* 已針對 Azure Stack Edge 資源啟用您的 Microsoft Azure 訂用帳戶。 確定您所使用的是受支援的訂用帳戶，例如 [Microsoft Enterprise 合約 (EA)](https://azure.microsoft.com/overview/sales-number/)、[雲端方案提供者 (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp) 或 [Microsoft Azure 贊助](https://azure.microsoft.com/offers/ms-azr-0036p/)。
* 您在 Azure Stack Edge / 資料箱閘道、IoT 中樞及 Azure 儲存體資源的資源群組層級上，具有擁有者或參與者存取權限。
    - 若要建立任何 Azure Stack Edge/ 資料箱閘道資源，您應該要有以資源群組層級作為範圍的參與者權限 (或更高權限)。 您也需要確定已註冊 `Microsoft.DataBoxEdge` 提供者。 如需有關如何註冊的資訊，請移至[註冊資源提供者](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers)。
    - 同樣的，若要建立儲存體帳戶資源，您需要以資源群組層級作為範圍的參與者存取權限 (或更高權限)。 根據預設，Azure 儲存體是已註冊的資源提供者。
- 您擁有 Microsoft Graph API 的系統管理員或使用者存取權。 如需詳細資訊，請參閱 [Microsoft Graph 權限參考](https://docs.microsoft.com/graph/permissions-reference)。
- 您擁有的 Microsoft Azure 儲存體帳戶具有存取認證。

### <a name="for-the-data-box-gateway-device"></a>針對資料箱閘道裝置

在您部署虛擬裝置之前，請確定：

- 您可以存取在 Windows Server 2012 R2 或更新版本或 VMware (ESXi 6.0、6.5 或 6.7) 上執行 Hyper-V，並且可用來佈建裝置的主機系統。
- 此主機系統能夠將下列資源專門用來佈建您的資料箱虛擬裝置：
  
  - 至少 4 個虛擬處理器。
  - 至少 8 GB 的 RAM。
  - 一個網路介面。
  - 250 GB 的 OS 磁碟。
  - 供系統資料使用的 2 TB 虛擬磁碟。

### <a name="for-the-datacenter-network"></a>對於資料中心的網路

在您開始前，請確定：

- 資料中心的網路是根據資料箱閘道裝置的網路需求所設定的。 如需詳細資訊，請參閱 [Data Box Gateway 系統需求](data-box-gateway-system-requirements.md)。

- 如需 Data Box Gateway 的正常運作條件，您應具有：

    - 最少 10 Mbps 的下載頻寬來確定裝置可維持在更新狀態。
    - 最少 20 Mbps 的專用上傳與下載頻寬，以傳輸檔案。

## <a name="create-a-new-resource"></a>建立新的資源

如果您目前已有可用來管理虛擬裝置的資料箱閘道資源，請略過此步驟，並移至[取得啟用金鑰](#get-the-activation-key)。

若要建立 Data Box Gateway 資源，請在 Azure 入口網站中執行下列步驟。

1. 使用您的 Microsoft Azure 認證登入：

    - 位於以下 RUL 的 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
    - 或者，位於以下 RUL 的 Azure Government 入口網站：[https://portal.azure.us](https://portal.azure.us)。 如需詳細資訊，請移至[使用入口網站連線到 Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal)。
2. 在左窗格中，選取 [+ 建立資源]  。 搜尋 **Azure Stack Edge/資料箱閘道**。 選取 Azure Stack Edge / 資料箱閘道。 選取 [建立]  。
3. 挑選您要用於 Data Box Gateway 裝置的訂用帳戶。 選取您要部署 Data Box Gateway 資源的區域。 如需 Azure Stack Edge 適用區域的完整清單，請參閱[依區域提供的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 請選擇與您要部署裝置的地理區域最接近的位置。 在 [Data Box Gateway]  選項中，選取 [建立]  。

    ![搜尋資料箱閘道服務](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. 在 [基本概念]  索引標籤上，輸入或選取下列 [專案詳細資料]  。
    
    |設定  |值  |
    |---------|---------|
    |訂用帳戶    |這會根據您稍早的選取項目自動填入。 訂用帳戶會連結到您的帳單帳戶。 |
    |資源群組  |選取現有的群組或建立新的群組。<br>深入了解 [Azure 資源群組](../azure-resource-manager/management/overview.md)。     |

5. 輸入或選取下列 [執行個體詳細資料]  。

    |設定  |值  |
    |---------|---------|
    |名稱   | 可識別資源的易記名稱。<br>此名稱介於 2 到 50 個字元之間，並且可包含字母、數字和連字號。<br> 名稱必須以字母或數字為開頭或結尾。        |   
    |區域     |如需 Azure Stack Edge 適用區域的完整清單，請參閱[依區域提供的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 針對 Azure Government，列在 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)中的所有政府區域都適用。 <br> 請選擇與您要部署裝置的地理區域最接近的位置。|
    
    ![建立資料箱閘道資源](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. 選取 [檢閱 + 建立]  。
 
7. 在 [檢閱 + 建立]  索引標籤上，檢閱 [定價詳細資料]  、[使用規定]  ，以及您的資源詳細資料。 選取 [建立]  。

    ![檢閱 Data Box Gateway 資源詳細資料](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

建立資源需要幾分鐘的時間。 順利建立及部署資源之後，您就會接獲通知。 選取 [前往資源]  。

![檢閱 Data Box Gateway 資源詳細資料](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>下載虛擬裝置映像

資料箱閘道資源建立後，請下載適當的虛擬裝置映像，以在主機系統上佈建虛擬裝置。 虛擬裝置映像為作業系統所特有。

> [!IMPORTANT]
> 在資料箱閘道上執行的軟體只能用於資料箱閘道資源。

在 [Azure 入口網站](https://portal.azure.com/)中遵循下列步驟，以下載虛擬裝置映像。

1. 在您所建立的資源中，選取 [概觀]  。 如果您有現有的 Azure Data Box Gateway 資源，請選取該資源並移至 [概觀]  。 選取 [裝置設定]  。

    ![新增資料箱閘道資源](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. 在 [下載映像]  圖格上，選取用來佈建 VM 的主機伺服器上對應至作業系統的虛擬裝置映像。 映像檔大約為 5.6 GB。
   
   * [VHDX (適用於 Windows Server 2012 R2 及更新版本上的 Hyper-V)](https://aka.ms/dbe-vhdx-2012)。
   * [VMDK (適用於 VMWare ESXi 6.0、6.5 或 6.7)](https://aka.ms/dbe-vmdk)。

    ![下載 Data Box Gateway 虛擬裝置映像](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. 下載檔案並將檔案解壓縮至本機磁碟機，記下解壓縮檔案的所在位置。


## <a name="get-the-activation-key"></a>取得啟用金鑰

在 Data Box Gateway 資源啟動並執行後，您必須取得啟用金鑰。 此金鑰可用來啟動資料箱閘道裝置，並將其與資源連線。 您現在可以在 Azure 入口網站中取得此金鑰。

1. 選取您所建立的資源，然後選取 [概觀]  。 在 [裝置設定]  中，移至 [設定並啟用]  圖格。

    ![設定並啟用圖格](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. 選取 [產生金鑰]  以建立啟用金鑰。 選取 [複製] 圖示以複製金鑰，並儲存金鑰以供日後使用。

    ![取得啟用金鑰](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - 啟用金鑰在產生之後 3 天就會到期。
> - 如果金鑰已過期，請產生新的金鑰。 舊金鑰沒有效用。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解資料箱閘道的相關主題，像是：

> [!div class="checklist"]
> * 建立新的資源
> * 下載虛擬裝置映像
> * 取得啟用金鑰

請繼續進行下一個教學課程，以了解如何為資料箱閘道佈建虛擬機器。 請根據您的主機作業系統，來參閱下列其中一個詳細指示：

> [!div class="nextstepaction"]
> [在 Hyper-V 中佈建資料箱閘道](./data-box-gateway-deploy-provision-hyperv.md)

OR

> [!div class="nextstepaction"]
> [在 VMware 中佈建資料箱閘道](./data-box-gateway-deploy-provision-vmware.md)


