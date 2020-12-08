---
title: 在 Azure 入口網站中設定 Azure Stack Edge Mini R 裝置憑證的教學課程 | Microsoft Docs
description: 部署 Azure Stack Edge Mini R 的教學課程將說明如何在實體裝置上設定憑證。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: 1e81ab61b04f9cca6aff57de1736eac25a871c97
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463812"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>教學課程：為您的 Azure Stack Edge Mini R 設定憑證、VPN 和加密

本教學課程說明如何使用本機 Web UI 來設定 Azure Stack Edge Mini R 裝置憑證、VPN 和待用加密。

此步驟所花費的時間會根據您選擇的特定選項，以及如何在您的環境中建立憑證流程而有所不同。

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 必要條件
> * 設定實體裝置的憑證
> * 設定 VPN
> * 設定待用加密

## <a name="prerequisites"></a>必要條件

在您設定及安裝 Azure Stack Edge Mini R 裝置之前，請先確定：

* 您已依照[安裝 Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md) 中的詳細說明安裝實體裝置。

* 如果您要攜帶自己的憑證：
    - 您應該備妥適當格式的憑證，包括簽署鏈結憑證。 如需憑證的詳細資訊，請移至[管理憑證](azure-stack-edge-j-series-manage-certificates.md)

    - 如果您的裝置部署在 Azure Government 或 Azure Government Secret 或 Azure Government 的最高祕密雲端，而未部署在 Azure 公用雲端中，則需要簽署鏈結憑證，才可啟用裝置。 
    如需憑證詳細資訊，請移至[管理憑證](azure-stack-edge-j-series-manage-certificates.md)。


## <a name="configure-certificates-for-device"></a>設定裝置憑證

1. 您會在 **憑證** 頁面上設定憑證。 根據您是否已在 **裝置** 頁面中變更裝置名稱或 DNS 網域，可以為憑證選擇下列其中一個選項。

    - 如果您在先前的步驟中未變更預設裝置名稱或預設 DNS 網域，而且不想要攜帶自己的憑證，則可以略過此步驟，並繼續進行下一個步驟。 裝置已自動產生自我簽署憑證，可以開始使用。 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - 如果您變更了裝置名稱或 DNS 網域，就會看到憑證的狀態顯示為 **無效**。 

        ![本機 Web UI 的「憑證」頁面 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        選取憑證以檢視狀態的詳細資料。

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        憑證狀態為 **無效**，這是因為憑證不會反映更新的裝置名稱和 DNS 網域 (用於主體名稱和主體替代項目)。 若要成功啟用裝置，您可以攜帶自己的已簽署端點憑證和對應的簽署鏈結。 您必須先新增簽署鏈結，才能上傳端點憑證。 如需詳細資訊，請移至[攜帶您自己的憑證以供 Azure Stack Edge Mini R 裝置使用](#bring-your-own-certificates)。


    - 如果您變更了裝置名稱或 DNS 網域，且並未攜帶您自己的憑證，則會 **封鎖啟用**。


#### <a name="bring-your-own-certificates"></a>攜帶您自己的憑證

您已在此裝置的先前步驟中新增簽署鏈結。 現在可以上傳端點憑證、節點憑證、本機 UI 憑證和 VPN 憑證。 請遵循這些步驟來新增您自己的憑證。

1. 若要上傳憑證，請在 **憑證** 頁面上，選取 [+ 新增憑證]。

    [![本機 Web UI 的「憑證」頁面 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. 您可以上傳其他憑證。 例如，您可以上傳 Azure Resource Manager 和 Blob 儲存體端點憑證。

    ![本機 Web UI 的「憑證」頁面 6](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. 您也可以上傳本機 Web UI 憑證。 上傳此憑證之後，您必須開啟瀏覽器並清除快取。 接著，您必須連線到裝置的本機 Web UI。  

    ![本機 Web UI 的「憑證」頁面 7](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. 您也可以上傳節點憑證。


    ![本機 Web UI 的「憑證」頁面 8](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. 最後，您可以上傳 VPN 憑證。
        
    ![本機 web UI 的「憑證」頁面](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. 您隨時都可以選取憑證並檢視詳細資料，確保這些憑證與您上傳的憑證相符。

    [![本機 Web UI 的「憑證」頁面 9](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    憑證頁面應該會更新，以反映新增的憑證。

    [![本機 Web UI 的「憑證」頁面 10](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > 除了 Azure 公用雲端以外，在啟用所有雲端設定 (Azure Government 或 Azure Stack Hub) 之前，必須先放入簽署鏈結憑證。


## <a name="configure-vpn"></a>設定 VPN

1. 在 **安全性** 圖格上，針對 VPN 選取 [設定]。 

    若要設定 VPN，您需要先確定已在 Azure 中完成所有必要設定。 如需詳細資訊，請參閱[透過 PowerShell 設定 Azure Stack Edge Mini R 裝置的 VPN](azure-stack-edge-placeholder.md)。 完成後，您就可以在本機 UI 中設定。
    
    1. 在 VPN 頁面上，選取 [設定]。
        ![設定 VPN 本機 UI 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. 在 **設定 VPN** 刀鋒視窗中︰

        1. 提供電話簿作為輸入項目。
        2. 提供 Azure 資料中心 IP 範圍 JSON 檔案作為輸入項目。 可於 [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) 下載此檔案。
        3. 選取 [eastus] 作為區域。
        4. 選取 [套用]。

        ![設定 VPN 本機 UI 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. 設定僅限使用 VPN 存取的 IP 位址範圍。 
    
        - 在 **僅限使用 VPN 存取的 IP 位址範圍** 下，選取 [設定]。
        - 輸入您為 Azure 虛擬網路選擇的 VNET IPv4 範圍。
        - 選取 [套用]。
    
        ![設定 VPN 本機 UI 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

您的裝置現在已準備就緒，可供加密。 設定待用加密。


## <a name="enable-encryption"></a>啟用加密

1. 在 **安全性** 圖格上，針對待用加密選取 [設定]。 這是必要的設定，而且在成功設定此值之前，您無法啟動裝置。 

    ![本機 Web UI 的「待用加密」頁面 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    在中心，一旦裝置經過映像處理，就會啟用磁碟區層級的 BitLocker 加密。 收到裝置之後，您需要設定待用加密。 系統會重新建立儲存集區和磁碟區，而您可以提供 BitLocker 金鑰來啟用待用加密，進而為待用資料建立第二層加密。

1. 在 **待用加密** 窗格中，提供 32 個字元 (AES-256 位元) 的 Base-64 編碼金鑰。 這是一次性設定，此金鑰是用來保護實際的加密金鑰。 

    ![本機 Web UI 的「待用加密」頁面 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    您也可以選擇自動產生此金鑰。

    ![本機 Web UI 的「待用加密」頁面 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. 選取 [套用]。 這項作業需要幾分鐘的時間，而且作業的狀態會顯示在 **安全性** 圖格上。

    ![本機 Web UI 的「待用加密」頁面 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. 當狀態顯示為 **完成** 之後，請返回 **開始使用**。

您的裝置現在已準備就緒，可供啟動。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 必要條件
> * 設定實體裝置的憑證
> * 設定 VPN
> * 設定待用加密

若要了解如何啟動您的 Azure Stack Edge Mini R 裝置，請參閱：

> [!div class="nextstepaction"]
> [啟動 Azure Stack Edge Mini R 裝置](./azure-stack-edge-mini-r-deploy-activate.md)
