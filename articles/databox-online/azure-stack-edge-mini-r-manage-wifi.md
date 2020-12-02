---
title: Azure Stack Edge 迷你 R Wi-Fi 管理
description: 說明如何使用 Azure 入口網站來管理 Azure Stack Edge 上的 Wi-Fi。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/28/2020
ms.author: alkohli
ms.openlocfilehash: f00be43d023d912d4b0b6e825dfe9d3e0ca2d250
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466387"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>使用本機 web UI 來管理您 Azure Stack Edge 迷你 R 的無線連線能力

本文說明如何管理 Azure Stack Edge 迷你 R 裝置上的無線網路連線能力。 您可以透過 Azure Stack Edge 迷你 R 裝置上的本機 web UI，來新增、連接及刪除 Wi-Fi 設定檔。

## <a name="about-wi-fi"></a>關於 Wi-Fi

您的 Azure Stack Edge 迷你 R 裝置可在有線至網路或透過無線網路時運作。 裝置必須啟用 Wi-Fi 埠，才能讓裝置連接到無線網路。 

您的裝置有五個埠，埠1到埠4，第五個 Wi-Fi 埠。 以下是連接到無線網路時，迷你 R 裝置背面平面的圖表。

![Wi-Fi 的纜線](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>新增、連接 Wi-Fi 設定檔

請在裝置的本機 UI 中執行下列步驟，以新增並聯機至 Wi-Fi 設定檔。

1. 在裝置的本機 Web UI 中，移至 **開始使用** 頁面。 在 **網路** 圖格上，選取 [設定]。  
    
    在您的實體裝置上，有五個網路介面。 連接埠 1 和連接埠 2 是 1 Gbps 的網路介面。 埠3和埠4全都是 10 Gbps 的網路介面。 第五個埠是 Wi-Fi 埠。 

    [![本機 web UI 的 [網路設定] 頁面1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    選取 Wi-Fi 埠並設定埠設定。 
    
    > [!IMPORTANT]
    > 強烈建議您設定 Wi-Fi 埠的靜態 IP 位址。  

    ![本機 Web UI 的 [網路設定] 頁面 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    當您套用 Wi-Fi 通訊埠設定之後，[ **網路** ] 頁面就會更新。

    ![本機 web UI 的 [網路設定] 第3頁](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. 選取 [ **新增 Wi-Fi 設定檔** ]，然後上傳您的 Wi-Fi 設定檔。 

    ![本機 web UI 「埠 WiFi 網路設定」1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    無線網路設定檔包含 SSID (網路名稱) 、密碼金鑰和安全性資訊，以連接到無線網路。 您可以從網路系統管理員取得環境的 Wi-Fi 設定檔。

    ![本機 web UI 「埠 WiFi 網路設定」2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    新增設定檔之後，Wi-Fi 設定檔的清單會更新，以反映新的設定檔。 設定檔應該會將 **連接狀態** 顯示為 [已 **中斷** 連線]。 

    ![本機 web UI 「埠 WiFi 網路設定」3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. 在無線網路設定檔成功載入之後，請連接到此設定檔。 選取 **[連接到 Wi-Fi 設定檔]**。 

    ![本機 web UI 「埠 Wi-Fi 網路設定」4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. 選取您在上一個步驟中新增的 Wi-Fi 設定檔，然後選取 [套用 **]。** 

    ![本機 web UI 「埠 Wi-Fi 網路設定」5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    **連接狀態** 應該會更新為 [**已連線**]。 信號強度會更新以指出信號的品質。 

    ![本機 web UI 「埠 Wi-Fi 網路設定」6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > 若要傳輸大量資料，我們建議您使用有線連線，而不是無線網路。 


## <a name="download-wi-fi-profile"></a>下載 Wi-Fi 設定檔

您可以下載用來進行無線網路連線的 Wi-Fi 設定檔。

1. 在裝置的本機 web UI 中，移至 [設定 **> 網路**]。 

2. 在 [Wi-Fi 設定檔設定] 下，選取 [ **下載設定檔**]。 這應該會下載您目前使用的 Wi-Fi 設定檔。


## <a name="delete-wi-fi-profile"></a>刪除 Wi-Fi 設定檔

您可以刪除用來進行無線網路連線的 Wi-Fi 設定檔。


1. 在裝置的本機 web UI 中，移至 [設定 **> 網路**]。 

2. 在 [Wi-Fi 設定檔設定] 下，選取 [ **刪除 Wi-Fi 設定檔**]。

3. 在 [ **刪除 Wi-Fi 設定檔** ] 分頁中，選擇您要刪除的設定檔。 選取 [套用]。


## <a name="configure-cisco-wi-fi-profile"></a>設定 Cisco Wi-Fi 設定檔

以下是如何在裝置上管理和設定 Cisco 無線控制器和存取點的一些指引。 

### <a name="dhcp-bridging-mode"></a>DHCP 橋接模式

若要將 Cisco 無線控制器用於您的裝置，您必須在無線區域網路控制器上啟用動態主機設定通訊協定 (DHCP) 橋接模式 (WLC) 。

如需詳細資訊，請參閱 [DHCP 橋接模式](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9)。

#### <a name="bridging-configuration-example"></a>橋接設定範例

若要在控制器上啟用 DHCP 橋接功能，您必須停用控制器上的 DHCP proxy 功能。 使用命令列啟用 DHCP 橋接：

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

如果 DHCP 伺服器不存在於與用戶端相同的第2層 (L2) 網路上，則應該使用 IP 協助程式將廣播轉送至用戶端閘道上的 DHCP 伺服器。 這是此設定的範例：

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

DHCP 橋接功能是全域設定，因此它會影響控制器內的所有 DHCP 交易。 您必須在有線基礎結構中，為控制器上所有必要的虛擬區域網路絡 (VLAN) s 新增 IP 協助程式語句。

### <a name="enable-the-passive-client-for-wlan"></a>啟用適用于 WLAN 的被動式用戶端

若要啟用無線區域網路的被動式用戶端功能 (Cisco 無線控制器上的 WLAN) ：

* 與 WLAN 相關聯的介面必須啟用 VLAN 標記。
* 必須啟用 WLAN 的多播 VLAN。
* WLC 上必須啟用 GARP 轉送。

如需詳細資訊，請參閱多播 [優化的多播 VLAN 資訊](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html)。

### <a name="troubleshoot"></a>疑難排解

如果您遇到在 Azure Stack Edge 迷你 R 裝置上執行的 Vm 上的 IP 位址配置問題，則應該驗證您網路環境上的上述設定設定。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [部署 Azure Stack Edge 迷你 R 裝置](azure-stack-edge-mini-r-deploy-prep.md)。
