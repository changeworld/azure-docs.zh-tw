---
title: 準備 Azure 入口網站、資料中心環境以部署 Azure Stack Edge Pro GPU 的教學課程 | Microsoft Docs
description: 第一個有關部署 Azure Stack Edge Pro GPU 的教學課程牽涉到如何準備 Azure 入口網站。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: cf7719487d4f03b8d9524234e1a58cf792a4843b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90899779"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-with-gpu"></a>教學課程：準備部署 Azure Stack Edge Pro 搭配 GPU 

這是完整部署 Azure Stack Edge Pro 搭配 GPU 所需之一系列部署教學課程中的第一個教學課程。 本教學課程說明如何準備 Azure 入口網站以部署 Azure Stack Edge 資源。

您需要有系統管理員權限，才能完成安裝和設定程序。 入口網站準備工作不到 10 分鐘就能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立新的資源
> * 取得啟用金鑰

### <a name="get-started"></a>開始使用

針對 Azure Stack Edge Pro 部署，您必須先準備環境。 準備好環境後，請遵循必要的步驟，並視需要執行完整部署裝置的選用步驟和程序。 逐步部署指示會指出應該執行各個必要和選用步驟的時機。

| 步驟 | 描述 |
| --- | --- |
| **準備** |這些步驟是針對將要進行的部署所必須完成的準備工作。 |
| **[部署設定檢查清單](#deployment-configuration-checklist)** |使用此檢查清單，將部署之前和部署期間的資訊加以收集並記錄。 |
| **[部署必要條件](#prerequisites)** |這些會驗證環境是否準備就緒以供部署。 |
|  | |
|**部署教學課程** |需要這些教學課程，才能在生產環境中部署您的 Azure Stack Edge Pro 裝置。 |
|**[1.準備 Azure Stack Edge Pro 的 Azure 入口網站](azure-stack-edge-gpu-deploy-prep.md)** |在安裝 Azure Stack Edge 實體裝置之前，請先建立並設定您的 Azure Stack Edge 資源。 |
|**[2.安裝 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md)**|打開包裝，然後接上 Azure Stack Edge Pro 實體裝置的機架和纜線。  |
|**[3.連線至 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md)** |安裝裝置之後，請連線到裝置本機 Web UI。  |
|**[4.設定 Azure Stack Edge Pro 的網路設定](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |設定網路，包括裝置的計算網路和 Web Proxy 設定。   |
|**[5.設定 Azure Stack Edge Pro 的裝置設定](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |指派裝置名稱和 DNS 網域，設定更新伺服器和裝置時間。 |
|**[6.設定 Azure Stack Edge Pro 的安全性設定](azure-stack-edge-gpu-deploy-configure-certificates.md)** |設定裝置的憑證。 使用裝置產生的憑證或攜帶您自己的憑證。   |
|**[7.啟動 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md)** |使用服務的啟用金鑰來啟動裝置。 裝置已就緒，可設定 SMB 或 NFS 共用或透過 REST 連線。 |
|**[8.設定計算](azure-stack-edge-gpu-deploy-configure-compute.md)** |在您的裝置上設定計算角色。 這同時也會建立 Kubernetes 叢集。 |
|**[9A.使用 Edge 共用傳輸資料](azure-stack-edge-j-series-deploy-add-shares.md)** |新增共用，並透過 SMB 或 NFS 連線至共用。 |
|**[9B.使用 Edge 儲存體帳戶傳輸資料](azure-stack-edge-j-series-deploy-add-storage-accounts.md)** |新增儲存體帳戶，並透過 REST API 連線到 Blob 儲存體。 |


您現在可以開始收集 Azure Stack Edge Pro 裝置的軟體配置相關資訊。

## <a name="deployment-configuration-checklist"></a>部署設定檢查清單

在部署裝置之前，您必須收集資訊以設定 Azure Stack Edge Pro 裝置上的軟體。 事先備妥這些資訊，可協助簡化在環境中部署裝置的程序。 使用 [Azure Stack Edge Pro 部署組態檢查清單](azure-stack-edge-gpu-deploy-checklist.md)，以記下您部署裝置時的設定詳細資訊。


## <a name="prerequisites"></a>Prerequisites

以下是 Azure Stack Edge 資源、Azure Stack Edge Pro 裝置及資料中心網路的設定先決條件。

### <a name="for-the-azure-stack-edge-resource"></a>針對 Azure Stack Edge 資源

在您開始前，請確定：

- 已針對 Azure Stack Edge 資源啟用您的 Microsoft Azure 訂用帳戶。 確定您所使用的是受支援的訂用帳戶，例如 [Microsoft Enterprise 合約 (EA)](https://azure.microsoft.com/overview/sales-number/)、[雲端方案提供者 (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp) 或 [Microsoft Azure 贊助](https://azure.microsoft.com/offers/ms-azr-0036p/)。 不支援隨用隨付訂用帳戶。
- 您在 Azure Stack Edge Pro/資料箱閘道、IoT 中樞及 Azure 儲存體資源的資源群組層級上，具有擁有者或參與者存取權限。

    - 若要建立任何 Azure Stack Edge/ 資料箱閘道資源，您應該要有以資源群組層級作為範圍的參與者權限 (或更高權限)。 您也需要確定已註冊 `Microsoft.DataBoxEdge` 提供者。 如需有關如何註冊的資訊，請移至[註冊資源提供者](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。
    - 若要建立任何 IoT 中樞資源，請確定已註冊該 Microsoft.Devices 提供者。 如需有關如何註冊的資訊，請移至[註冊資源提供者](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。
    - 同樣的，若要建立儲存體帳戶資源，您需要以資源群組層級作為範圍的參與者存取權限 (或更高權限)。 根據預設，Azure 儲存體是已註冊的資源提供者。
- 您有 Azure Active Directory 圖形 API 的管理員或使用者存取權。 如需詳細資訊，請參閱 [Azure Active Directory 圖形 API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)。
- 您擁有的 Microsoft Azure 儲存體帳戶具有存取認證。

### <a name="for-the-azure-stack-edge-pro-device"></a>針對 Azure Stack Edge Pro 裝置

在您部署實體裝置之前，請確定：

- 您已檢閱出貨套件內含的安全資訊。
- 您在資料中心的標準 19 吋機架中有 1U 插槽可用來以機架掛接裝置。
- 您有平穩的工作表面可供安全地放置裝置。
- 您要安裝裝置的場地具有來自獨立來源或含不斷電供應系統 (UPS) 之機架式配電單元 (PDU) 的標準 AC 電源。
- 您可以拆裝實體裝置。


### <a name="for-the-datacenter-network"></a>對於資料中心的網路

在您開始前，請確定：

- 資料中心內的網路是根據 Azure Stack Edge Pro 裝置的網路需求所設定的。 如需詳細資訊，請參閱 [Azure Stack Edge Pro 系統需求](azure-stack-edge-system-requirements.md)。

- 如需 Azure Stack Edge Pro 的正常運作條件，您具有：

    - 最少 10 Mbps 的下載頻寬來確定裝置可維持在更新狀態。
    - 最少 20 Mbps 的專用上傳與下載頻寬，以傳輸檔案。

## <a name="create-a-new-resource"></a>建立新的資源

如果您目前已有可用來管理實體裝置的 Azure Stack Edge 資源，請略過此步驟，並移至[取得啟用金鑰](#get-the-activation-key)。

若要建立 Azure Stack Edge 資源，請在 Azure 入口網站中執行下列步驟。

1. 使用您的 Microsoft Azure 認證經由以下 URL 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

2. 在左窗格中，選取 [+ 建立資源]。 搜尋並選取 **Azure Stack Edge/資料箱閘道**。 選取 [建立]。 

3. 挑選要用於 Azure Stack Edge Pro 裝置的訂用帳戶。 選取您要寄送此實體裝置的國家/地區。 選取 [顯示裝置]。

    ![建立資源 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. 選取裝置類型。 在 **Azure Stack Edge Pro** 下，選擇 [Azure Stack Edge Pro 搭配 GPU]，然後選擇 [選取]。 如果您看到任何問題，或無法選取裝置類型，請移至[訂單問題疑難排解](azure-stack-edge-troubleshoot-ordering.md)。

    ![建立資源 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. 根據您的業務需求，您可以從 Nvidia 選取具有 1 或 2 個圖形處理單元 (GPU) 的 Azure Stack Edge Pro。 

    ![建立資源 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)

6. 在 [基本概念]  索引標籤上，輸入或選取下列 [專案詳細資料]  。
    
    |設定  |值  |
    |---------|---------|
    |訂用帳戶    |這會根據您稍早的選取項目自動填入。 訂用帳戶會連結到您的帳單帳戶。 |
    |資源群組  |選取現有的群組或建立新的群組。<br>深入了解 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)。     |

7. 輸入或選取下列 [執行個體詳細資料]  。

    |設定  |值  |
    |---------|---------|
    |名稱   | 可識別資源的易記名稱。<br>此名稱介於 2 到 50 個字元之間，並且可包含字母、數字和連字號。<br> 名稱必須以字母或數字為開頭或結尾。        |
    |區域     |如需 Azure Stack Edge 適用區域的完整清單，請參閱[依區域提供的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 如果使用 Azure Government，如 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)所示的所有政府區域都適用。<br> 請選擇與您要部署裝置的地理區域最接近的位置。|

    ![建立資源 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)


8. 完成時，選取 [下一步:**** 交貨地址]。

    - 如果您已經有一個裝置，請選取 [我有 Azure Stack Edge Pro 裝置] 的下拉式方塊。

        ![建立資源 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - 如果這是您所訂購的新裝置，請輸入連絡人姓名、公司、裝置交貨地址和連絡資訊。

        ![建立資源 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

9. 完成時，選取 [下一步:標籤]。 選擇性地提供標籤來分類資源和合併計費。 完成時，選取 [下一步:檢閱 + 建立]。

10. 在 [檢閱 + 建立] 索引標籤上，檢閱 [定價詳細資料]、[使用規定]，以及您的資源詳細資料。 選取 [我已檢閱隱私權條款] 的下拉式方塊。

    ![建立資源 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

11. 選取 [建立]。

建立資源需要幾分鐘的時間。 順利建立及部署資源之後，您就會接獲通知。 選取 [前往資源]  。

![移至 Azure Stack Edge Pro 資源](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-1.png)

下單之後，Microsoft 會檢閱順訂單並與您接洽 (透過電子郵件) 交貨詳細資料。

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

如果您在訂單處理期間遇到任何問題，請參閱[訂單問題疑難排解](azure-stack-edge-troubleshoot-ordering.md)。

## <a name="get-the-activation-key"></a>取得啟用金鑰

在 Azure Stack Edge 資源已啟動並執行之後，您將必須取得啟用金鑰。 此金鑰可用來啟動 Azure Stack Edge Pro 裝置，並將其與資源連線。 您現在可以在 Azure 入口網站中取得此金鑰。

1. 選取您建立的資源。 選取[概觀]，然後選取 [裝置設定]。

    ![選取 [裝置設定]](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)

2. 在 [啟用] 圖格上，選取 [產生金鑰] 以建立啟用金鑰。 選取 [複製] 圖示以複製金鑰，並儲存金鑰以供日後使用。

    ![取得啟用金鑰](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

> [!IMPORTANT]
> - 啟用金鑰在產生之後 3 天就會到期。
> - 如果金鑰已過期，請產生新的金鑰。 舊金鑰沒有效用。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure Stack Edge Pro 的相關主題，像是：

> [!div class="checklist"]
> * 建立新的資源
> * 取得啟用金鑰

請繼續進行下一個教學課程，以了解如何安裝 Azure Stack Edge Pro。

> [!div class="nextstepaction"]
> [安裝 Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-install.md)



