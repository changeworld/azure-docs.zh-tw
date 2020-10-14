---
title: 在 Azure 入口網站中設定搭配 GPU 的 Azure Stack Edge Pro 裝置網路設定的教學課程 | Microsoft Docs
description: 部署 Azure Stack Edge Pro GPU 的教學課程會指示您設定實體裝置的網路、計算網路和 Web Proxy 設定。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: a6b72c9e6cc366d04937598f653f8ba887ce1b85
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952177"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-with-gpu"></a>教學課程：設定搭配 GPU 的 Azure Stack Edge Pro 網路

本教學課程說明如何使用本機 Web UI 設定搭配上線 GPU 的 Azure Stack Edge Pro 裝置的網路。

連線程序需要約 20 分鐘才能完成。

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 必要條件
> * 設定網路
> * 啟用計算網路
> * 設定 Web Proxy


## <a name="prerequisites"></a>必要條件

在您設定及安裝搭配 GPU 的 Azure Stack Edge Pro 裝置之前，請先確定：

* 您已依照[安裝 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md) 中的詳細說明安裝實體裝置。
* 您已連線到裝置的本機 Web UI，如[連線到 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md) 中所述


## <a name="configure-network"></a>設定網路

您的**開始使用**頁面會顯示向 Azure Stack Edge 服務設定及註冊實體裝置所需的各種設定。 

請依照這些步驟設定裝置上的網路。

1. 在裝置的本機 Web UI 中，移至**開始使用**頁面。 

2. 在**網路**圖格上，選取 [設定]。  
    
    ![本機 Web UI 的「網路設定」圖格](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    您的實體裝置上有六個網路介面。 連接埠 1 和連接埠 2 是 1 Gbps 的網路介面。 連接埠 3、連接埠 4、連接埠 5 和連接埠 6 都是 25 Gbps 的網路介面，但也可作為 10 Gbps 網路介面。 連接埠 1 會自動設定為管理專用連接埠，連接埠 2 到連接埠 6 則都是資料連接埠。 新裝置的**網路設定**頁面如下所示。
    
    ![本機 Web UI 的 [網路設定] 頁面](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)


   
3. 若要變更網路設定，請選取連接埠，然後在顯示的右側窗格中修改 IP 位址、子網路、閘道、主要 DNS 和次要 DNS。 

    - 如果您選取連接埠 1，可以看到其已預先設定為靜態。 

        ![本機 Web UI 的「連接埠 1 網路設定」](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - 如果您選取連接埠 2、連接埠 3、連接埠 4或連接埠 5，則系統會將這些連接埠都預設為 DHCP。

        ![本機 Web UI 的「連接埠 3 網路設定」](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    進行網路設定時，請牢記下列事項：

   * 如果您的環境中啟用了 DHCP，則會自動設定網路介面。 系統會自動指派 IP 位址、子網路、閘道 和 DNS。
   * 如果未啟用 DHCP，您可以視需要指派靜態 IP。
   * 您可以將網路介面設定為 IPv4。
   * 在 25-Gbps 介面上，您可以將 RDMA (遠端直接存取記憶體) 模式設定為 iWarp 或 RoCE (透過聚合式乙太網路的 RDMA)。 當低延遲是主要需求而不用考慮可擴縮性時，請使用 RoCE。 當延遲是關鍵需求，同時也必須優先考量使用便利性和可擴縮性時，iWARP 是最佳的選擇。
   * 任何連接埠的序號都會對應到節點序號。

    設定裝置網路後，頁面會更新，如下所示。

    ![本機 Web UI 的 [網路設定] 頁面 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     >[!NOTE]
     >
     > * 我們建議您不要將網路介面的本機 IP 位址從靜態切換成 DCHP，除非您有另一個連接到裝置的 IP 位址。 如果使用一個網路介面並切換至 DHCP，就無法判斷 DHCP 位址。 如果您想要變更為 DHCP 位址，請等到裝置已啟動服務之後再變更。 然後，您就可以在您服務的 Azure 入口網站中於 [裝置屬性] 中檢視所有介面卡的 IP。


    在您設定並套用網路設定後，請選取 [下一步:計算] 以設定計算網路。

## <a name="enable-compute-network"></a>啟用計算網路

請遵循下列步驟來啟用計算和設定計算網路。 

<!--1. Go to the **Get started** page in the local web UI of your device. On the **Network** tile, select **Compute network**.  

    ![Compute page in local UI 1](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)-->

1. 在**計算**頁面上，選取您想要為計算啟用的網路介面。 

    ![本機 UI 中的計算頁面 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. 在**網路設定**對話方塊中，選取 [啟用]。 啟用計算時，系統會在您的裝置上建立該網路介面的虛擬交換器。 虛擬交換器會用於裝置上的計算基礎結構。 
    
1. 指派 **Kubernetes 節點 IP**。 這些靜態 IP 位址可供計算 VM 使用。  

    針對 n 個節點裝置，系統會使用開始和結束 IP 位址，為計算 VM 提供最少「n + 1」個 IPv4 位址 (或更多) 的連續範圍。 假設 Azure Stack Edge 是 1 個節點的裝置，則至少會提供 2 個連續的 IPv4 位址。

    > [!IMPORTANT]
    > Azure Stack Edge 上的 Kubernetes 會針對 Pod 使用 172.27.0.0/16 子網路，並針對服務使用 172.28.0.0/16 子網路。 請確定您的網路中未使用這些子網路。 如果您的網路中已使用這些子網路，您可以從裝置的 PowerShell 介面執行 `Set-HcsKubeClusterNetworkInfo` Cmdlet，以變更這些子網路。 如需詳細資訊，請參閱[變更 Kubernetes 的 Pod 和服務子網路](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets)。


1. 指派 **Kubernetes 外部服務 IP**。 這些也是負載平衡的 IP 位址。 這些連續的 IP 位址可供您想要在 Kubernetes 叢集外部公開的服務使用，而且您可以根據公開的服務數目來指定靜態 IP 範圍。 
    
    > [!IMPORTANT]
    > 強烈建議您為 Azure Stack Edge Pro 中樞服務指定最少 1 個 IP 位址，以存取計算模組。 接著，您可以針對需要從叢集外部存取的其他服務/IoT Edge 模組 (每個服務/模組 1 個)，選擇性地指定其他 IP 位址。 您稍後可以更新服務 IP 位址。 
    
1. 選取 [套用]。

    ![本機 UI 中的計算頁面 3](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. 系統需要幾分鐘才會套用組態，建議您重新整理瀏覽器。 您可以看到系統已啟用指定的連接埠以執行計算。 
 
    ![本機 UI 中的計算頁面 4](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    完成時，選取 [下一步:Web Proxy] 以設定 Web Proxy。  

  
## <a name="configure-web-proxy"></a>設定 Web Proxy

這是選用組態。

> [!IMPORTANT]
> * 若您允許在 Azure Stack Edge Pro 裝置上計算及使用 IoT Edge 模組，建議將 Web Proxy 驗證設定為 [無]。 不支援 NTLM。
>* 不支援 Proxy 自動設定 (PAC) 檔案。 PAC 檔案會定義網頁瀏覽器和其他使用者代理程式如何自動選擇適當的 Proxy 伺服器 (存取方法) 來擷取指定的 URL。 嘗試攔截和讀取所有流量 (然後以自己的憑證重新簽署所有內容) 的 Proxy 不受系統相容，因為 Proxy 的憑證不受信任。 通常透明的 Proxy 與 Azure Stack Edge Pro 搭配較能運作良好。 不支援非透明的 Web Proxy。

<!--1. Go to the **Get started** page in the local web UI of your device.
2. On the **Network** tile, configure your web proxy server settings. Although web proxy configuration is optional, if you use a web proxy, you can configure it on this page only.

   ![Local web UI "Web proxy settings" page](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)-->

1. 在 **Web Procy 設定**頁面上，採取下列步驟：

    1. 在 [Web proxy URL] 方塊中，以下列格式輸入 URL：`http://host-IP address or FQDN:Port number`。 此處不支援 HTTPS URL。

    2. 在 [驗證] 底下，選取 [無] 或 [NTLM]。 若您允許在 Azure Stack Edge Pro 裝置上計算及使用 IoT Edge 模組，建議將 Web Proxy 驗證設定為 [無]。 不支援 **NTLM**。

    3. 如果您使用驗證，請輸入使用者名稱和密碼。

    4. 若要驗證並套用所設定的 Web Proxy 設定，請選取 [套用]  。
    
   ![本機 Web UI 的 [Web Proxy 設定] 頁面 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. 套用設定之後，請選取 [下一步:裝置]。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]
> * 必要條件
> * 設定網路
> * 啟用計算網路
> * 設定 Web Proxy


若要了解如何設定您的 Azure Stack Edge Pro 裝置，請參閱：

> [!div class="nextstepaction"]
> [設定裝置設定](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
