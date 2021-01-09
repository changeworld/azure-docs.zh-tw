---
title: 準備 Azure 入口網站、資料中心環境以部署 Azure Stack Edge Pro R 的教學課程
description: 第一個有關部署 Azure Stack Edge Pro R 的教學課程牽涉到如何準備 Azure 入口網站。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: 7ca9b21838d35b54b4ed84d5aaf3aa797b02d9e0
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630763"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-r"></a>教學課程：準備部署 Azure Stack Edge Pro R

這是一系列部署教學課程中的第一個，為完全部署 Azure Stack Edge Pro R 的必要課程。本教學課程說明如何準備 Azure 入口網站以部署 Azure Stack Edge 資源。 本教學課程使用不斷電供應系統 (UPS) 隨附的 1 個節點 Azure Stack Edge Pro R 裝置。

您需要有系統管理員權限，才能完成安裝和設定程序。 入口網站準備工作不到 10 分鐘就能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 建立新的資源
> * 取得啟用金鑰

### <a name="get-started"></a>開始使用

若要部署 Azure Stack Edge Pro R，請依指定的順序參閱下列教學課程。

| 如要進行此步驟 | 使用這些文件 |
| --- | --- |
| **準備** |這些步驟是針對將要進行的部署所必須完成的準備工作。 |
| **[部署設定檢查清單](#deployment-configuration-checklist)** |使用此檢查清單，將部署之前和部署期間的資訊加以收集並記錄。 |
| **[部署必要條件](#prerequisites)** |這些會驗證環境是否準備就緒以供部署。 |
|  | |
|**部署教學課程** |需要這些教學課程，才能在生產環境中部署您的 Azure Stack Edge Pro R 裝置。 |
|**[1.為裝置準備 Azure 入口網站](azure-stack-edge-pro-r-deploy-prep.md)** |在安裝 Azure Stack Edge 實體裝置之前，請先建立並設定您的 Azure Stack Edge 資源。 |
|**[2.安裝裝置](azure-stack-edge-pro-r-deploy-install.md)**|檢查實體裝置並接線。  |
|**[3.將裝置連線](azure-stack-edge-pro-r-deploy-connect.md)** |安裝裝置之後，請連線到裝置本機 Web UI。  |
|**[4.設定網路](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)** |設定網路，包括裝置的計算網路和 Web Proxy 設定。   |
|**[5.設定裝置](azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)** |指派裝置名稱和 DNS 網域，設定更新伺服器和裝置時間。 |
|**[6.設定安全性](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)** |設定裝置憑證、VPN 及待用加密。 使用裝置產生的憑證或攜帶您自己的憑證。   |
|**[7.啟用裝置](azure-stack-edge-pro-r-deploy-activate.md)** |使用服務的啟用金鑰來啟動裝置。 裝置已就緒，可設定 SMB 或 NFS 共用或透過 REST 連線。 |
|**[8.設定計算](azure-stack-edge-gpu-deploy-configure-compute.md)** |在您的裝置上設定計算角色。 這同時也會建立 Kubernetes 叢集。 |

現在您可以開始設定 Azure 入口網站。

## <a name="deployment-configuration-checklist"></a>部署設定檢查清單

在部署裝置之前，您必須收集資訊以設定 Azure Stack Edge Pro 裝置上的軟體。 事先備妥這些資訊，可協助簡化在環境中部署裝置的程序。 使用 [Azure Stack Edge Pro R 部署組態檢查清單](azure-stack-edge-pro-r-deploy-checklist.md)，以記下您部署裝置時的設定詳細資訊。

## <a name="prerequisites"></a>Prerequisites

以下是 Azure Stack Edge 資源、Azure Stack Edge 裝置及資料中心網路的設定先決條件。

### <a name="for-the-azure-stack-edge-resource"></a>針對 Azure Stack Edge 資源

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>針對 Azure Stack Edge 裝置

在您部署實體裝置之前，請確定：

- 您已在下列位置檢閱此裝置的安全資訊：[Azure Stack Edge 裝置的安全性指導方針](azure-stack-edge-pro-r-safety.md)。
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 



### <a name="for-the-datacenter-network"></a>對於資料中心的網路

在您開始前，請確定：

- 資料中心內的網路是根據 Azure Stack Edge 裝置的網路需求所設定的。 如需詳細資訊，請參閱 [Azure Stack Edge Pro R 系統需求](azure-stack-edge-pro-r-system-requirements.md)。

- 如需裝置的正常運作條件，您必須具有：

    - 最少 10 Mbps 的下載頻寬來確定裝置可維持在更新狀態。
    - 最少 20 Mbps 的專用上傳與下載頻寬，以傳輸檔案。

## <a name="create-a-new-resource"></a>建立新的資源

如果您目前已有可用來管理實體裝置的 Azure Stack Edge 資源，請略過此步驟，並移至[取得啟用金鑰](#get-the-activation-key)。

若要建立 Azure Stack Edge 資源，請在 Azure 入口網站中執行下列步驟。

1. 使用您的 Microsoft Azure 認證經由以下 URL 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

2. 在左窗格中，選取 [+ 建立資源]。 搜尋並選取 **Azure Stack Edge/資料箱閘道**。 選取 [建立]。 

3. 挑選要用於 Azure Stack Edge Pro 裝置的訂用帳戶。 選取您要寄送此實體裝置的國家/地區。 選取 [顯示裝置]。

    ![建立資源 1](media/azure-stack-edge-pro-r-deploy-prep/create-resource-1.png)

4. 選取裝置類型。 在 **Azure Stack Edge** 下，選擇 [Azure Stack Edge Pro R]，然後選擇 [選取]。 如果您看到任何問題，或無法選取裝置類型，請移至[訂單問題疑難排解](azure-stack-edge-troubleshoot-ordering.md)。

    ![建立資源 2](media/azure-stack-edge-pro-r-deploy-prep/create-resource-2.png)

5. 根據業務需求，您可以選取 **Azure Stack Edge Pro R 單一節點** 或 **使用 UPS 的 Azure Stack Edge Pro R 單一節點**。  

    ![建立資源 3](media/azure-stack-edge-pro-r-deploy-prep/create-resource-3.png)

6. 在 [基本概念]  索引標籤上，輸入或選取下列 [專案詳細資料]  。
    
    |設定  |值  |
    |---------|---------|
    |訂用帳戶    |這會根據您稍早的選取項目自動填入。 訂用帳戶會連結到您的帳單帳戶。 |
    |資源群組  |選取現有的群組或建立新的群組。<br>深入了解 [Azure 資源群組](../azure-resource-manager/management/overview.md)。     |

7. 輸入或選取下列 [執行個體詳細資料]  。

    |設定  |值  |
    |---------|---------|
    |名稱   | 可識別資源的易記名稱。<br>此名稱介於 2 到 50 個字元之間，並且可包含字母、數字和連字號。<br> 名稱必須以字母或數字為開頭或結尾。        |
    |區域     |如需 Azure Stack Edge 適用區域的完整清單，請參閱[依區域提供的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 如果使用 Azure Government，如 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)所示的所有政府區域都適用。<br> 請選擇與您要部署裝置的地理區域最接近的位置。|

    ![建立資源 4](media/azure-stack-edge-pro-r-deploy-prep/create-resource-4.png)


8. 完成時，選取 [下一步:交貨地址]。

    - 如果您已經有一個裝置，請選取 [我有 Azure Stack Edge Pro R 裝置] 的下拉式方塊。

        ![建立資源 5](media/azure-stack-edge-pro-r-deploy-prep/create-resource-5.png)

    - 如果這是您所訂購的新裝置，請輸入連絡人姓名、公司、裝置交貨地址和連絡資訊。

        ![建立資源 6](media/azure-stack-edge-pro-r-deploy-prep/create-resource-6.png)

9. 完成時，選取 [下一步:標籤]。 選擇性地提供標籤來分類資源和合併計費。 完成時，選取 [下一步:檢閱 + 建立]。

10. 在 [檢閱 + 建立] 索引標籤上，檢閱 [定價詳細資料]、[使用規定]，以及您的資源詳細資料。 選取 [我已檢閱隱私權條款] 的下拉式方塊。

    ![建立資源 7](media/azure-stack-edge-pro-r-deploy-prep/create-resource-7.png) 

    您也會在建立資源期間收到通知，已啟用受控服務識別 (MSI)，可讓您向雲端服務進行驗證。 只要資源存在，就會存在此身分識別。

11. 選取 [建立]。

建立資源需要幾分鐘的時間。 也會建立一個 MSI，讓 Azure Stack Edge 裝置與 Azure 中的資源提供者進行通訊。

順利建立及部署資源之後，您就會接獲通知。 選取 [前往資源]  。

![移至 Azure Stack Edge Pro 資源](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-1.png)

下單之後，Microsoft 會檢閱順訂單並與您接洽 (透過電子郵件) 交貨詳細資料。

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

如果您在訂單處理期間遇到任何問題，請參閱[訂單問題疑難排解](azure-stack-edge-troubleshoot-ordering.md)。

## <a name="get-the-activation-key"></a>取得啟用金鑰

在 Azure Stack Edge 資源已啟動並執行之後，您將必須取得啟用金鑰。 此金鑰可用來啟動 Azure Stack Edge Pro 裝置，並將其與資源連線。 您現在可以在 Azure 入口網站中取得此金鑰。

1. 選取您建立的資源。 選取[概觀]，然後選取 [裝置設定]。

    ![選取 [裝置設定]](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-2.png)

2. 在 [啟動] 圖格上，提供 Azure Key Vault 的名稱或接受預設名稱。 金鑰保存庫名稱可介於 3 到 24 個字元之間。 

    系統會為使用您的裝置啟動的每個 Azure Stack Edge 資源建立金鑰保存庫。 金鑰保存庫可讓您儲存及存取祕密，例如，服務的通道完整性金鑰 (CIK) 會儲存在金鑰保存庫中。 

    指定金鑰保存庫名稱之後，請選取 [產生金鑰] 以建立啟用金鑰。 

    ![取得啟用金鑰](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-3.png)

    請等候幾分鐘讓金鑰保存庫和啟用金鑰建立。 選取 [複製] 圖示以複製金鑰，並儲存金鑰以供日後使用。


> [!IMPORTANT]
> - 啟用金鑰在產生之後 3 天就會到期。
> - 如果金鑰已過期，請產生新的金鑰。 舊金鑰沒有效用。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure Stack Edge 的相關主題，像是：

> [!div class="checklist"]
> * 建立新的資源
> * 取得啟用金鑰

請繼續進行下一個教學課程，以了解如何安裝 Azure Stack Edge。

> [!div class="nextstepaction"]
> [安裝 Azure Stack Edge](./azure-stack-edge-pro-r-deploy-install.md)
