---
title: 在 Azure 入口網站中設定 Azure Stack Edge Pro R 裝置憑證的教學課程 | Microsoft Docs
description: 部署 Azure Stack Edge Pro R 的教學課程將說明如何在實體裝置上設定憑證。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: fad3e5dcb0ecda82f3fb35cadf1719a62c99bd97
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463681"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>教學課程：為您 Azure Stack Edge Pro R 設定憑證

本教學課程說明如何使用本機 Web UI 來設定 Azure Stack Edge Pro R 裝置憑證。

此步驟所花費的時間會根據您選擇的特定選項，以及如何在您的環境中建立憑證流程而有所不同。

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 必要條件
> * 設定實體裝置的憑證
> * 設定 VPN
> * 設定待用加密

## <a name="prerequisites"></a>必要條件

在您設定及安裝 Azure Stack Edge Pro R 裝置之前，請先確定：

* 您已依照[安裝 Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md) 中的詳細說明安裝實體裝置。
* 如果您要攜帶自己的憑證：
    - 您應該備妥適當格式的憑證，包括簽署鏈結憑證。 如需憑證的詳細資訊，請移至[管理憑證](azure-stack-edge-j-series-manage-certificates.md)



## <a name="configure-certificates-for-device"></a>設定裝置憑證

1. 您會在 **憑證** 頁面上設定憑證。 根據您是否已在 **裝置** 頁面中變更裝置名稱或 DNS 網域，可以為憑證選擇下列其中一個選項。

    - 如果您在先前的步驟中未變更裝置名稱或 DNS 網域，則可以略過此步驟，並繼續進行下一個步驟。 裝置已自動產生自我簽署憑證，可以開始使用。 

    - 如果您變更了裝置名稱或 DNS 網域，就會看到憑證的狀態顯示為 **無效**。 

        ![本機 Web UI 的「憑證」頁面 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        選取憑證以檢視狀態的詳細資料。

        ![本機 Web UI 的「憑證」頁面 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        這是因為憑證不會反映更新的裝置名稱和 DNS 網域 (用於主體名稱和主體替代項目)。 若要成功啟用裝置，您可以攜帶自己的已簽署端點憑證和對應的簽署鏈結。 您必須先新增簽署鏈結，才能上傳端點憑證。 如需詳細資訊，請移至[攜帶您自己的憑證以供 Azure Stack Edge Pro R 裝置使用](#bring-your-own-certificates)。

    - 如果您變更了裝置名稱或 DNS 網域，且並未攜帶您自己的憑證，則會 **封鎖啟用**。

    
#### <a name="bring-your-own-certificates"></a>攜帶您自己的憑證

請遵循這些步驟來新增您自己的憑證，包括簽署鏈結。

1. 若要上傳憑證，請在 **憑證** 頁面上，選取 [+ 新增憑證]。

    ![本機 Web UI 的「憑證」頁面 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. 先上傳簽署鏈結，然後選取 [驗證和新增]。

    ![本機 Web UI 的「憑證」頁面 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. 現在您可以上傳其他憑證。 例如，您可以上傳 Azure Resource Manager 和 Blob 儲存體端點憑證。

    ![本機 Web UI 的「憑證」頁面 6](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    您也可以上傳本機 Web UI 憑證。 上傳此憑證之後，您必須開啟瀏覽器並清除快取。 接著，您必須連線到裝置的本機 Web UI。  

    ![本機 Web UI 的「憑證」頁面 7](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    您也可以上傳節點憑證。

    ![本機 Web UI 的「憑證」頁面 8](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    最後，您可以上傳 VPN 憑證。

    ![本機 Web UI 的「憑證」頁面 9](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    您隨時都可以選取憑證並檢視詳細資料，確保這些憑證與您上傳的憑證相符。

    憑證頁面應該會更新，以反映新增的憑證。

    ![本機 Web UI 的「憑證」頁面 10](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > 除了 Azure 公用雲端以外，在啟用所有雲端設定 (Azure Government 或 Azure Stack) 之前，必須先放入簽署鏈結憑證。

1. 選取 [< 返回開始使用]。

## <a name="configure-vpn"></a>設定 VPN

1. 在 **安全性** 圖格上，針對 VPN 選取 [設定]。

    ![本機 Web UI 的 [VPN] 頁面](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    若要設定 VPN，您需要先確定已在 Azure 中完成所有必要設定。 如需詳細資訊，請參閱[設定必要條件](azure-stack-edge-placeholder.md)和[設定 VPN 的 Azure 資源](azure-stack-edge-placeholder.md)。 完成後，您就可以在本機 UI 中設定。
    
    1. 在 VPN 頁面上，選取 [設定]。
    2. 在 [設定 VPN ] 刀鋒視窗中︰

    - 啟用 **VPN 設定**。
    - 提供 **VPN 共用密碼**。 這是您在建立 Azure VPN 連線物件時所提供的共用金鑰。
    - 提供 **VPN 閘道 IP** 位址。 這是 Azure 本機網路閘道 IP 位址。
    - 針對 **PFS 群組**，請選取 [無]。 
    - 針對 **H 群組**，請選取 [群組 2]。
    - 針對 **IPsec 完整性方法**，請選取 [SHA256]。
    - 針對 **IPseccipher 轉換常數**，請選取 [GCMAES256]。
    - 針對 **IPsec 驗證常數**，請選取 [GCMAES256]。
    - 針對 **IKE 加密方法**，請選取 [AES256]。
    - 選取 [套用]。

        ![設定本機 UI 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. 若要上傳 VPN 路由組態檔，請選取 [上傳]。 
    
        ![設定本機 UI 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - 瀏覽至您在上一個步驟中下載到本機系統上的 VPN 組態 json 檔案。
        - 選取區域作為與裝置、虛擬網路和閘道相關聯的 Azure 區域。
        - 選取 [套用]。
    
            ![設定本機 UI 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. 若要新增用戶端特定路由，請將 IP 位址範圍設定為僅使用 VPN 存取。 
    
        - 在 **僅限使用 VPN 存取的 IP 位址範圍** 下，選取 [設定]。
        - 請提供有效的 IPv4 範圍，然後選取 [新增]。 重複步驟來新增其他範圍。
        - 選取 [套用]。
    
            ![設定本機 UI 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. 選取 [< 返回開始使用]。

## <a name="configure-encryption-at-rest"></a>設定待用加密

1. 在 **安全性** 圖格上，針對待用加密選取 [設定]。 這是必要的設定，而且在成功設定此值之前，您無法啟動裝置。 

    在中心，一旦裝置經過映像處理，就會啟用磁碟區層級的 BitLocker 加密。 收到裝置之後，您需要設定待用加密。 系統會重新建立儲存集區和磁碟區，而您可以提供 BitLocker 金鑰來啟用待用加密，進而為待用資料建立第二層加密。

1. 在 **待用加密** 窗格中，提供 32 個字元的 Base-64 編碼金鑰。 這是一次性設定，此金鑰是用來保護實際的加密金鑰。 您可以選擇自動產生此金鑰，或輸入金鑰。

    ![本機 Web UI 的「待用加密」窗格](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    在裝置啟用後，金鑰會儲存在 **雲端詳細資料** 頁面的金鑰檔案中。

1. 選取 [套用]。 這項作業需要幾分鐘的時間，而且作業的狀態會顯示在 **安全性** 圖格上。

    ![本機 Web UI 的「待用加密」頁面](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. 當狀態顯示為 **完成** 之後，請選取 [< 回到開始使用]。

您的裝置現在已準備就緒，可供啟動。 


## <a name="next-steps"></a>後續步驟

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 必要條件
> * 設定實體裝置的憑證
> * 設定 VPN
> * 設定待用加密

若要了解如何啟動您的 Azure Stack Edge Pro R 裝置，請參閱：

> [!div class="nextstepaction"]
> [啟動 Azure Stack Edge Pro R 裝置](./azure-stack-edge-pro-r-deploy-activate.md)
