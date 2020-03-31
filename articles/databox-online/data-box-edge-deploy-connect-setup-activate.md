---
title: 在 Azure 入口網站中連線、設定及啟用 Azure Data Box Edge 裝置的教學課程 | Microsoft Docs
description: 部署 Data Box Edge 的教學課程將說明如何連線、設定和啟動實體裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 144c59c8bc24e8e10584702ec6cd48f7aa8c15c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79222905"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>教學課程：連線、設定及啟動 Azure Data Box Edge 

本教學課程說明如何使用本機 Web UI 來連線、設定及啟用 Azure Data Box Edge 裝置。

設定和啟用程序可能需要約 20 分鐘才能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 連線至實體裝置
> * 設定並啟動實體裝置

## <a name="prerequisites"></a>Prerequisites

在您設定及安裝 Data Box Edge 裝置之前，請先確定：

* 您已依照[安裝 Data Box Edge](data-box-edge-deploy-install.md) 中的詳細說明安裝實體裝置。
* 您具有先前建立用以管理 Data Box Edge 裝置的 Data Box Edge 服務適用的啟用金鑰。 如需詳細資訊，請移至[準備部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)。

## <a name="connect-to-the-local-web-ui-setup"></a>連線至本機 Web UI 設定 

1. 使用靜態 IP 位址 192.168.100.5 和子網路 255.255.255.0，在電腦上設定要連線到 Data Box Edge 裝置的乙太網路介面卡。

2. 將電腦連線到裝置上的連接埠 1。 請使用下圖來識別您裝置上的連接埠 1。

    ![已連接纜線的裝置後擋板](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. 開啟瀏覽器視窗，然後在 `https://192.168.100.10` 存取裝置的本機 Web UI。  
    在您開啟裝置之後，此動作可能需要花費幾分鐘的時間。 

    您會看到錯誤或警告指出網站的安全性憑證有問題。 
   
    ![網站安全性憑證錯誤訊息](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. 選取 [繼續瀏覽此網頁]  。  
    這些步驟可能會因您使用的瀏覽器而有所不同。

5. 登入裝置的 Web UI。 預設密碼為 *Password1*。 
   
    ![Data Box Edge 裝置登入頁面](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. 出現提示時，變更裝置系統管理員密碼。  
    新密碼必須包含 8 到 16 個字元。 此密碼必須包含下列其中 3 種字元：大寫、小寫、數字及特殊字元。

您現在位於裝置的儀表板上。

## <a name="set-up-and-activate-the-physical-device"></a>設定並啟動實體裝置
 
您的儀表板會顯示向 Data Box Edge 服務設定及註冊實體裝置所需的各種設定。 [裝置名稱]  、[網路設定]  、[Web Proxy 設定]  及 [時間設定]  是可省略的。 只有 [雲端設定]  是必要的設定。
   
![本機 Web UI 的 [儀表板] 頁面](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. 在左側窗格中，選取 [裝置名稱]  ，然後為您的裝置輸入一個易記的名稱。  
    易記名稱必須包含 1 到 15 個字元，且包含字母、數字及連字號。

    ![本機 Web UI 的 [裝置名稱] 頁面](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (選擇性) 在左側窗格中，選取 [網路設定]  ，然後進行設定。  
    您的實體裝置上有六個網路介面。 連接埠 1 和連接埠 2 是 1 Gbps 的網路介面。 連接埠 3、連接埠 4、連接埠 5 和連接埠 6 都是 25 Gbps 的網路介面，但也可作為 10 Gbps 網路介面。 連接埠 1 會自動設定為管理專用連接埠，連接埠 2 到連接埠 6 則都是資料連接埠。 [網路設定]  頁面如下所示。
    
    ![本機 Web UI 的 [網路設定] 頁面](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    進行網路設定時，請牢記下列事項：

   - 如果您的環境中啟用了 DHCP，則會自動設定網路介面。 系統會自動指派 IP 位址、子網路、閘道 和 DNS。
   - 如果未啟用 DHCP，您可以視需要指派靜態 IP。
   - 您可以將網路介面設定為 IPv4。

     >[!NOTE] 
     > 我們建議您不要將網路介面的本機 IP 位址從靜態切換成 DCHP，除非您有另一個連接到裝置的 IP 位址。 如果使用一個網路介面並切換至 DHCP，就無法判斷 DHCP 位址。 如果您想要變更為 DHCP 位址，請等到裝置已向服務註冊之後再變更。 然後，您就可以在您服務的 Azure 入口網站中於 [裝置屬性]  中檢視所有介面卡的 IP。

3. (選擇性) 在左側窗格中，選取 [Web Proxy 設定]  ，然後設定 Web Proxy 伺服器。 雖然 Web Proxy 設定是選用的，但如果您使用 Web Proxy，便只能在此頁面上設定它。
   
   ![本機 Web UI 的 [Web Proxy 設定] 頁面](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   在 [Web Proxy 設定]  頁面上，執行下列動作：
   
   a. 在 [Web proxy URL]  方塊中，以下列格式輸入 URL：`http://host-IP address or FQDN:Port number`。 此處不支援 HTTPS URL。

   b. 在 [驗證]  底下，選取 [無]  或 [NTLM]  。

   c. 如果您使用驗證，請輸入使用者名稱和密碼。

   d. 若要驗證並套用所設定的 Web Proxy 設定，請選取 [套用設定]  。
   
   > [!NOTE]
   > 不支援 Proxy 自動設定 (PAC) 檔案。 PAC 檔案會定義網頁瀏覽器和其他使用者代理程式如何自動選擇適當的 Proxy 伺服器 (存取方法) 來擷取指定的 URL。
   > 嘗試攔截和讀取所有流量 (然後以自己的憑證重新簽署所有內容) 的 Proxy 不受系統相容，因為 Proxy 的憑證不受信任。
   > 通常透明的 Proxy 與 Azure Data Box Edge 搭配較能運作良好。

4. (選擇性) 在左側窗格中，選取 [時間設定]  ，然後設定您裝置的時區，以及主要和次要 NTP 伺服器。  
    NTP 伺服器是必要的，因為您的裝置必須讓時間同步，才能與您的雲端服務提供者進行驗證。
       
    在 [時間設定]  頁面上，執行下列操作：
    
    1. 在 [時區]  下拉式清單中，選取與裝置部署所在地理位置相對應的時區。
        裝置的預設時區是太平洋標準時間。 裝置將針對所有排程的操作使用這個時區。

    2. 在 [主要 NTP 伺服器]  方塊中，輸入您裝置的主要伺服器，或接受預設值 time.windows.com。  
        請確定您的網路允許 NTP 流量從您的資料中心通過到網際網路。

    3. (選擇性) 在 [次要 NTP 伺服器]  方塊中，輸入您裝置的次要伺服器。

    4. 若要驗證並套用所設定的時間設定，請選取 [套用設定]  。

        ![本機 Web UI 的 [時間設定] 頁面](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (選擇性) 在左側窗格中選取 [儲存體設定]  ，即可在您裝置上設定儲存體恢復功能。 此功能目前為預覽狀態。 根據預設，裝置上的儲存體並無復原功能，一旦裝置上的資料磁碟故障，資料就會遺失。 當您啟用復原選項時，裝置上的儲存體將會重新設定，而裝置將可承受一個資料磁碟上的失敗，並且不會遺失資料。 將儲存體設定為具有復原性，將會減少您裝置的可用容量。

    > [!IMPORTANT] 
    > 復原功能只能在啟動裝置之前設定。 

    ![本機 Web UI 的 [儲存體設定] 頁面](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. 在左側窗格中，選取 [雲端設定]  ，然後向 Azure 入口網站中的 Data Box Edge 服務啟用您的裝置。
    
    1. 在 [啟用金鑰]  方塊中，輸入您在[取得啟用金鑰](data-box-edge-deploy-prep.md#get-the-activation-key)中為 Data Box Edge 取得的啟用金鑰。
    2. 選取 [套用]  。
       
        ![本機 Web UI 的 [雲端設定] 頁面](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. 首先，該裝置會啟動。 接著系統會掃描裝置，檢查是否有任何重大更新，如果有的話，系統會自動套用更新。 您會看到該效果的通知。

        對話方塊中也有復原金鑰，您應複製該金鑰並儲存在安全的位置。 萬一裝置無法開機時，此金鑰可用來復原資料。

        ![本機 Web UI 的 [雲端設定] 頁面更新](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. 成功完成更新後，您可能需要稍等幾分鐘。 此頁面會更新並指出裝置已成功啟動。

        ![本機 Web UI 的 [雲端設定] 頁面更新](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

裝置設定完成。 您現在可以在裝置上新增共用。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 連線至實體裝置
> * 設定並啟動實體裝置

若要了解如何使用 Data Box Edge 裝置來傳輸資料，請參閱：

> [!div class="nextstepaction"]
> [使用 Data Box Edge 傳輸資料](./data-box-edge-deploy-add-shares.md)。
