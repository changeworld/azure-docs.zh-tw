---
title: 準備 Azure 入口網站、資料中心環境以部署 Azure Stack Edge Pro 的教學課程 | Microsoft Docs
description: 第一個有關部署 Azure Stack Edge Pro 的教學課程牽涉到如何準備 Azure 入口網站。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/11/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 3943caba5249432b3a0a4b7c2e63b2b818e2b7a1
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575698"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro"></a>教學課程：準備部署 Azure Stack Edge Pro  

這是完整部署 Azure Stack Edge Pro 所需之一系列部署教學課程中的第一個教學課程。 本教學課程說明如何準備 Azure 入口網站以部署 Azure Stack Edge 資源。

您需要有系統管理員權限，才能完成安裝和設定程序。 入口網站準備工作不到 10 分鐘就能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 建立新的資源
> * 取得啟用金鑰

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="get-started"></a>開始使用

若要部署 Azure Stack Edge Pro，請依指定的順序參閱下列教學課程。

| **#** | **在此步驟中** | **使用這些文件** |
| --- | --- | --- | 
| 1. |**[準備 Azure Stack Edge Pro 的 Azure 入口網站](azure-stack-edge-deploy-prep.md)** |在安裝 Azure Stack Edge 實體裝置之前，請先建立並設定您的 Azure Stack Edge 資源。 |
| 2. |**[安裝 Azure Stack Edge Pro](azure-stack-edge-deploy-install.md)**|打開包裝，然後接上 Azure Stack Edge Pro 實體裝置的機架和纜線。  |
| 3. |**[連線、設定及啟動 Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md)** |連線至本機 Web UI、完成裝置設定，並啟動裝置。 裝置已就緒，可設定 SMB 或 NFS 共用。  |
| 4. |**[使用 Azure Stack Edge Pro 傳輸資料](azure-stack-edge-deploy-add-shares.md)** |新增共用，並透過 SMB 或 NFS 連線至共用。 |
| 5. |**[使用 Azure Stack Edge Pro 轉換資料](azure-stack-edge-deploy-configure-compute.md)** |在裝置上設定計算模組，以在資料移至 Azure 時加以轉換。 |

現在您可以開始設定 Azure 入口網站。

## <a name="prerequisites"></a>Prerequisites

以下是 Azure Stack Edge 資源、Azure Stack Edge Pro 裝置及資料中心網路的設定先決條件。

### <a name="for-the-azure-stack-edge-resource"></a>針對 Azure Stack Edge 資源

在您開始前，請確定：

* 已針對 Azure Stack Edge 資源啟用您的 Microsoft Azure 訂用帳戶。 確定您所使用的是受支援的訂用帳戶，例如 [Microsoft Enterprise 合約 (EA)](https://azure.microsoft.com/overview/sales-number/)、[雲端方案提供者 (CSP)](/partner-center/azure-plan-lp) 或 [Microsoft Azure 贊助](https://azure.microsoft.com/offers/ms-azr-0036p/)。 不支援隨用隨付訂用帳戶。

* 您在 Azure Stack Edge / 資料箱閘道、IoT 中樞及 Azure 儲存體資源的資源群組層級上，具有擁有者或參與者存取權限。

  * 您應該是訂用帳戶層級的 **擁有者**，才能授與參與者存取權。 若要將參與者存取權授與其他人，請在 Azure 入口網站中移至 [所有服務] > [訂用帳戶] > [存取控制 (IAM)] > [+ 新增] > [新增角色指派]。 如需詳細資訊，請參閱[教學課程：使用 Azure 入口網站為使用者授與 Azure 資源的存取權](../role-based-access-control/quickstart-assign-role-user-portal.md)。

  * 若要建立任何 Azure Stack Edge/ 資料箱閘道資源，您應該要有以資源群組層級作為範圍的參與者權限 (或更高權限)。 您也需要確定已註冊 `Microsoft.DataBoxEdge` 資源提供者。 如需如何註冊資源提供者的相關資訊，請參閱[註冊資源提供者](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。
  * 若要建立任何 IoT 中樞資源，請確定已註冊該 Microsoft.Devices 提供者。 如需有關如何註冊的資訊，請移至[註冊資源提供者](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。
  * 同樣的，若要建立儲存體帳戶資源，您需要以資源群組層級作為範圍的參與者存取權限 (或更高權限)。 根據預設，Azure 儲存體是已註冊的資源提供者。
* 您有 Azure Active Directory 圖形 API 的管理員或使用者存取權。 如需詳細資訊，請參閱 [Azure Active Directory 圖形 API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)。
* 您擁有的 Microsoft Azure 儲存體帳戶具有存取認證。
* 您的系統管理員所設定的任何 Azure 原則都不會封鎖您。 如需原則的詳細資訊，請參閱[快速入門：建立原則指派以識別不符合規範的資源](../governance/policy/assign-policy-portal.md)。

### <a name="for-the-azure-stack-edge-pro-device"></a>針對 Azure Stack Edge Pro 裝置

在您部署實體裝置之前，請確定：

* 您已檢閱出貨套件內含的安全資訊。
* 您在資料中心的標準 19 吋機架中有 1U 插槽可用來以機架掛接裝置。
* 您有平穩的工作表面可供安全地放置裝置。
* 您要安裝裝置的場地具有來自獨立來源或含不斷電供應系統 (UPS) 之機架式配電單元 (PDU) 的標準 AC 電源。
* 您可以拆裝實體裝置。

### <a name="for-the-datacenter-network"></a>對於資料中心的網路

在您開始前，請確定：

* 資料中心內的網路是根據 Azure Stack Edge Pro 裝置的網路需求所設定的。 如需詳細資訊，請參閱 [Azure Stack Edge Pro 系統需求](azure-stack-edge-system-requirements.md)。

* 如需 Azure Stack Edge Pro 的正常運作條件，您具有：

  * 最少 10 Mbps 的下載頻寬來確定裝置可維持在更新狀態。
  * 最少 20 Mbps 的專用上傳與下載頻寬，以傳輸檔案。

## <a name="create-a-new-resource"></a>建立新的資源

如果您目前已有可用來管理實體裝置的 Azure Stack Edge 資源，請略過此步驟，並移至[取得啟用金鑰](#get-the-activation-key)。

若要建立 Azure Stack Edge 資源，請在 Azure 入口網站中執行下列步驟。

1. 使用您的 Microsoft Azure 認證登入： 

    - 位於以下 RUL 的 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
    - 或者，位於以下 RUL 的 Azure Government 入口網站：[https://portal.azure.us](https://portal.azure.us)。 如需詳細資訊，請移至[使用入口網站連線到 Azure Government](../azure-government/documentation-government-get-started-connect-with-portal.md)。

2. 在左窗格中，選取 [+ 建立資源]。 搜尋並選取 **Azure Stack Edge/資料箱閘道**。 選取 [建立]。
3. 挑選要用於 Azure Stack Edge Pro 裝置的訂用帳戶。 選取要部署 Azure Stack Edge 資源的區域。 如需 Azure Stack Edge 適用區域的完整清單，請參閱[依區域提供的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。

    請選擇與您要部署裝置的地理區域最接近的位置。 該區域只儲存裝置管理的中繼資料。 實際資料可儲存在任何儲存體帳戶中。
    
    在 **Azure Stack Edge Pro** 選項中，選取 [建立]。

    ![搜尋 Azure Stack Edge 服務](media/azure-stack-edge-deploy-prep/data-box-edge-sku.png)

3. 在 [基本概念] 索引標籤上，輸入或選取下列 [專案詳細資料]。
    
    |設定  |值  |
    |---------|---------|
    |訂用帳戶    |這會根據您稍早的選取項目自動填入。 訂用帳戶會連結到您的帳單帳戶。 |
    |資源群組  |選取現有的群組或建立新的群組。<br>深入了解 [Azure 資源群組](../azure-resource-manager/management/overview.md)。     |

4. 輸入或選取下列 [執行個體詳細資料]。

    |設定  |值  |
    |---------|---------|
    |名稱   | 可識別資源的易記名稱。<br>此名稱介於 2 到 50 個字元之間，並且可包含字母、數字和連字號。<br> 名稱必須以字母或數字為開頭或結尾。        |
    |區域     |如需 Azure Stack Edge 適用區域的完整清單，請參閱[依區域提供的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 如果使用 Azure Government，如 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)所示的所有政府區域都適用。<br> 請選擇與您要部署裝置的地理區域最接近的位置。|

    ![專案和執行個體詳細資料](media/azure-stack-edge-deploy-prep/data-box-edge-resource.png)

5. 完成時，選取 [下一步:交貨地址]。

    - 如果您已經有一個裝置，請選取 [我有 Azure Stack Edge Pro 裝置] 的下拉式方塊。
    - 如果這是您所訂購的新裝置，請輸入連絡人姓名、公司、裝置交貨地址和連絡資訊。

    ![新裝置的交貨地址](media/azure-stack-edge-deploy-prep/data-box-edge-resource1.png)

6. 完成時，選取 [下一步:檢閱 + 建立]。

7. 在 [檢閱 + 建立] 索引標籤上，檢閱 [定價詳細資料]、[使用規定]，以及您的資源詳細資料。 選取 [我已檢閱隱私權條款] 的下拉式方塊。

    ![檢閱 Azure Stack Edge 資源詳細資料和隱私權條款](media/azure-stack-edge-deploy-prep/data-box-edge-resource2.png)

8. 選取 [建立]。

建立資源需要幾分鐘的時間。 順利建立及部署資源之後，您就會接獲通知。 選取 [前往資源]。

![移至 Azure Stack Edge 資源](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

下單之後，Microsoft 會檢閱順訂單並與您接洽 (透過電子郵件) 交貨詳細資料。

![檢閱 Azure Stack Edge Pro 訂單的通知](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>取得啟用金鑰

在 Azure Stack Edge 資源已啟動並執行之後，您將必須取得啟用金鑰。 此金鑰可用來啟動 Azure Stack Edge Pro 裝置，並將其與資源連線。 您現在可以在 Azure 入口網站中取得此金鑰。

1. 選取您建立的資源。 選取[概觀]，然後選取 [裝置設定]。

    ![選取 [裝置設定]](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. 在 [啟用] 圖格上，選取 [產生金鑰] 以建立啟用金鑰。 選取 [複製] 圖示以複製金鑰，並儲存金鑰以供日後使用。

    ![取得啟用金鑰](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * 啟用金鑰在產生之後 3 天就會到期。
> * 如果金鑰已過期，請產生新的金鑰。 舊金鑰沒有效用。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure Stack Edge Pro 的相關主題，像是：

> [!div class="checklist"]
>
> * 建立新的資源
> * 取得啟用金鑰

請繼續進行下一個教學課程，以了解如何安裝 Azure Stack Edge Pro。

> [!div class="nextstepaction"]
> [安裝 Azure Stack Edge Pro](./azure-stack-edge-deploy-install.md)