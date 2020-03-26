---
title: 在 Azure 入口網站中連線、設定和啟動 Azure 資料箱閘道
description: 部署資料箱閘道的第三個教學課程將說明如何連線、設定和啟動您的虛擬裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: c2a0dde496d6af66387210ca9b2ebf9cb4bdae7f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79087946"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>教學課程：連線、設定、啟動 Azure 資料箱閘道

## <a name="introduction"></a>簡介

本教學課程說明如何使用本機 Web UI 來連線、設定和啟動您的資料箱閘道裝置。 

設定和啟用程序可能需要約 10 分鐘才能完成。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 連線至虛擬裝置
> * 設定並啟動虛擬裝置

## <a name="prerequisites"></a>Prerequisites

在您設定及安裝資料箱閘道之前，請先確定：

* 您已根據[在 Hyper-V 中佈建資料箱閘道](data-box-gateway-deploy-provision-hyperv.md)或[在 VMware 中佈建資料箱閘道](data-box-gateway-deploy-provision-vmware.md)中的詳細說明，佈建虛擬裝置並取得其連線的 URL。
* 您具有先前建立用以管理資料箱閘道裝置的資料箱閘道服務適用的啟用金鑰。 如需詳細資訊，請移至[準備部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。


## <a name="connect-to-the-local-web-ui-setup"></a>連線至本機 Web UI 設定 

1. 開啟瀏覽器視窗，然後在以下位置存取裝置的本機 Web UI：
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   請使用您在上一個教學課程中記下的連線 URL。 您會看到錯誤或警告指出網站的安全性憑證有問題。

2. 選取 [繼續瀏覽此網頁]  。 這些步驟可能會因您使用的瀏覽器而有所不同。
   
    ![網站安全性憑證錯誤訊息](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. 登入虛擬裝置的 Web UI。 預設密碼為 *Password1*。 
   
    ![登入本機 Web UI](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. 在提示中變更裝置密碼。 新密碼必須包含 8 到 16 個字元。 密碼必須包含下列 3 項：大寫、小寫、數字和特殊字元。

    ![變更裝置密碼](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

您現在位於裝置的**儀表板**上。

## <a name="set-up-and-activate-the-virtual-device"></a>設定並啟動虛擬裝置
 
您的儀表板會顯示向資料箱閘道服務設定及註冊虛擬裝置所需的各種設定。 [裝置名稱]  、[網路設定]  、[Web Proxy 設定]  及 [時間設定]  是可省略的。 只有 [雲端設定]  是必要的設定。
   
![本機 Web UI 的 [儀表板] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. 在左側窗格中，選取 [裝置名稱]  ，然後為您的裝置輸入一個易記的名稱。 易記名稱必須包含 1 到 15 個字元，且包含字母、數字及連字號。

    ![本機 Web UI 的 [裝置名稱] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (選擇性) 在左側窗格中，選取 [網路設定]  ，然後進行設定。 在虛擬裝置中，您會看到一個或多個網路介面，視您在基礎虛擬機器中設定的數量而定。 以下是已啟用一個網路介面的虛擬裝置所顯示的 [網路設定]  頁面。
    
    ![本機 Web UI 的 [網路設定] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    進行網路設定時，請牢記下列事項：

    - 如果您的環境中啟用了 DHCP，則會自動設定網路介面。 系統會自動指派 IP 位址、子網路、閘道 和 DNS。
    - 如果未啟用 DHCP，您可以視需要指派靜態 IP。
    - 您可以將網路介面設定為 IPv4。

     >[!NOTE] 
     > 我們建議您不要將網路介面的本機 IP 位址從靜態切換成 DHCP，除非您有另一個連接到裝置的 IP 位址。 如果使用一個網路介面並切換至 DHCP，就無法判斷 DHCP 位址。 如果您想要變更為 DHCP 位址，請等到裝置已向服務註冊之後再變更。 然後，您就可以在您服務的 Azure 入口網站中於 [裝置屬性]  中檢視所有介面卡的 IP。

3. (選用) 設定 Web Proxy 伺服器。 雖然 Web Proxy 設定是選用的，但如果您使用 Web Proxy，便只能在此頁面上設定。
   
   ![本機 Web UI 的 [Web Proxy 設定] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   在 [Web Proxy]  頁面上，執行下列動作：
   
   1. 在 [Web proxy URL]  方塊中，以下列格式輸入 URL：`http://&lt;host-IP address or FQDN&gt;:Port number`。 此處不支援 HTTPS URL。
   2. 在 [驗證]  底下，選取 [無]  或 [NTLM]  。
   3. 如果您使用驗證，請輸入**使用者名稱**和**密碼**。
   4. 若要驗證並套用所設定的 Web Proxy 設定，請選取 [套用]  。

   > [!NOTE]
   > 不支援 Proxy 自動設定 (PAC) 檔案。 PAC 檔案會定義網頁瀏覽器和其他使用者代理程式如何自動選擇適當的 Proxy 伺服器 (存取方法) 來擷取指定的 URL。
   > 嘗試攔截和讀取所有流量 (然後以自己的憑證重新簽署所有內容) 的 Proxy 不受系統相容，因為 Proxy 的憑證不受信任。
   > 通常透明的 Proxy 與 Azure 資料箱閘道搭配較能運作良好。

4. (選擇性) 在左側窗格中，選取 [時間設定]  ，然後設定您裝置的時區，以及主要和次要 NTP 伺服器。 

    NTP 伺服器是必要的，因為您的裝置必須讓時間同步，才能與您的雲端服務提供者進行驗證。
    
    ![本機 Web UI 的 [時間設定] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    在 [時間設定]  頁面上，執行下列操作：
    
    1. 在 [時區]  下拉式清單中，選取與裝置部署所在地理位置相對應的時區。
        裝置的預設時區是太平洋標準時間。 裝置將針對所有排程的操作使用這個時區。

    2. 指定裝置的 [主要 NTP 伺服器]  ，或是接受預設值 `time.windows.com`。   
        請確定您的網路允許 NTP 流量從您的資料中心通過網際網路。

    3. (選擇性) 在 [次要 NTP 伺服器]  方塊中，輸入您裝置的次要伺服器。

    4. 若要驗證並套用所設定的時間設定，請選取 [套用]  。

6. 在左側窗格中，選取 [雲端設定]  ，然後向 Azure 入口網站中的資料相閘道服務啟動您的裝置。
    
    1. 在 [啟用金鑰]  方塊中，輸入您在[取得啟用金鑰](data-box-gateway-deploy-prep.md#get-the-activation-key)中為資料箱閘道取得的**啟用金鑰**。

    2. 選取 [啟用]  。
       
         ![本機 Web UI 的 [雲端設定] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. 裝置會隨之啟動並自動套用重大更新 (如果有的話)。 您會看到該效果的通知。 您可以透過 Azure 入口網站監視更新進度。

        ![本機 Web UI 的 [雲端設定] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **對話方塊中也有復原金鑰，您應複製該金鑰並儲存在安全的位置。萬一裝置無法開機時，此金鑰可用來復原資料。**


    4. 您可能需要稍等幾分鐘，讓更新成功完成。 更新完成之後，請登入裝置。 [雲端設定]  頁面會更新並指出裝置已成功啟動。

        ![本機 Web UI 的 [雲端設定] 頁面更新](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

裝置設定完成。 您現在可以在裝置上新增共用。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 連線至虛擬裝置
> * 設定並啟動虛擬裝置

若要了解如何使用資料箱閘道來傳輸資料，請參閱：

> [!div class="nextstepaction"]
> [使用資料箱閘道傳輸資料](./data-box-gateway-deploy-add-shares.md)。
