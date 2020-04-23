---
title: 快速入門:建立私有雲
titleSuffix: Azure VMware Solutions by CloudSimple
description: 瞭解如何透過雲端簡單建立及設定使用 Azure VMware 解決方案的私有雲端
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a2fe7daf1476f19f6a6eea83174342fa1273fe14
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867971"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>快速入門 ─設定私有雲環境

在本文中,瞭解如何創建雲簡單私有雲並設置私有雲環境。

## <a name="before-you-begin"></a>開始之前

檢視[網路先決條件](cloudsimple-network-checklist.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-private-cloud"></a>建立私人雲端

私有雲是一個孤立的 VMware 堆疊,支援 ESXi 主機、vCenter、vSAN 和 NSX。

私有雲通過雲簡單門戶進行管理。 它們有自己的 vCenter 伺服器。 堆疊在專用節點和隔離裸機硬體節點上運行。

1. 選取 [所有服務]  。
2. 搜尋**雲簡單服務**。
3. 選擇要在其中創建私有雲的雲簡單服務。
4. 從**概述**中,按兩下 **「創建私有雲**」可為雲端簡單門戶打開新的瀏覽器選項卡。  如果出現提示,請使用 Azure 登錄認證登錄。  

    ![從 Azure 建立私有雲](media/create-private-cloud-from-azure.png)

5. 在雲簡單門戶中,為私有雲提供名稱。
6. 選擇私有雲**的位置**。
7. 選擇**節點類型**,與在 Azure 上預配的內容一致。
8. 指定**節點計數**。  創建私有雲至少需要三個節點。

    ![建立私有雲 - 基本資訊](media/create-private-cloud-basic-info.png)

9. 按下 **「下一步:高級選項**」 。。
10. 輸入 vSphere/vSAN 子網的 CIDR 範圍。 確保 CIDR 範圍不會與任何本地或其他 Azure 子網(虛擬網路)或閘道裝置偵疊。

    **CIDR 範圍選項**:/24、/23、/22 或 /21。 /24 CIDR 範圍最多支援 26 個節點,/23 CIDR 範圍最多支援 58 個節點,/22 和 /21 CIDR 範圍支援 64 個節點(私有雲中最大節點數)。  要瞭解更多資訊以及 VLAN 和子網,請參閱[VLAN 和子網概述](cloudsimple-vlans-subnets.md)。

      > [!IMPORTANT]
      > vSphere/vSAN CIDR 範圍內的 IP 位址保留供私有雲基礎設施使用。  不要在任何虛擬機器上使用此範圍內的 IP 位址。

11. 點選 **「下一步」來查看並建立**。
12. 查看設置。 如果需要更改任何設置,請按下 **「 上一個**」。
13. 按一下頁面底部的 [新增]  。

私有雲預配過程開始。  預配私有雲最多可能需要兩個小時。

## <a name="launch-cloudsimple-portal"></a>啟動雲簡單門戶

可以從 Azure 門戶訪問雲簡單門戶。  CloudSimple 門戶將使用單一登錄 (SSO) 使用 Azure 登錄認證啟動。  訪問雲簡單門戶需要您授權**雲簡單服務授權**應用程式。  有關授予權限的詳細資訊,請參閱[同意雲端簡單服務授權應用程式](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)。

1. 選取 [所有服務]  。
2. 搜尋**雲簡單服務**。
3. 選擇要在其中創建私有雲的雲簡單服務。
4. 從概述中,按一下「**轉到雲簡單」門戶**可打開 CloudSimple 門戶的新瀏覽器選項卡。  如果出現提示,請使用 Azure 登錄認證登錄。  

    ![啟動雲簡單門戶](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>建立點對點 VPN

點對點 VPN 連接是從電腦連接到私有雲的最簡單方法。 如果您遠端連接到私有雲,請使用點對點 VPN 連接。  要快速訪問私有雲,請按照以下步驟操作。  可透過[網站到網站 VPN](vpn-gateway.md)或[Azure ExpressRoute](on-premises-connection.md)從本地網路存取雲端簡單區域。

### <a name="create-gateway"></a>建立閘道

1. 啟動雲端簡單門戶並選擇**網路**。
2. 選擇**VPN 閘道**。
3. 按下 **「新 VPN 閘道**」。。

    ![建立 VPN 閘道](media/create-vpn-gateway.png)

4. 對於**閘道配置**,請指定以下設置,然後按**下 「下一步**」。

    * 選擇**指向網站 VPN**作為閘道類型。
    * 輸入名稱以標識網關。
    * 選擇部署雲端簡單服務的 Azure 位置。
    * 指定指向網站閘道的用戶端子網。  當您連接時,將從該子網提供 DHCP 位址。

5. 對於**連接/使用者**,指定以下設置,然後按下 **「下一步**」。

    * 要自動允許所有當前和未來的用戶通過此指向網站閘道存取私有雲,請選擇「**自動添加所有使用者**」。 選擇此選項後,「使用者」清單中的所有使用者將自動選擇。 您可以通過取消清單中的單個使用者來覆蓋自動選項。
    * 要僅選擇單個使用者,請按下「使用者」清單中的複選框。

6. VLAN/子網部分允許您為閘道和連接指定管理和使用者 VLAN/子網。

    * **「自動新增**」選項設定此閘道的全域策略。 這些設置適用於當前閘道。 可以在 **「選擇」** 區域中覆蓋這些設定。
    * 選擇**此群組的管理 VLAN/子網路**。
    * 要添加所有使用者定義的 VLAN/子網,請單擊「**添加使用者定義的 VLAN/子網**」。
    * 選擇**Select**「設定將覆寫 **」自動新增**「下的全域設定。

7. 按下 **「下一步**」查看設置。 按下「編輯」圖示可進行任何更改。
8. 按下「**建立**」 以創建 VPN 閘道。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>使用點對點 VPN 輕鬆連線到雲端

VPN 用戶端需要從您的電腦連接到雲端簡單。  下載適用於 MacOS 和 OS X 的 Windows 或[黏度](https://www.sparklabs.com/viscosity/download/)的[OpenVPN 用戶端](https://openvpn.net/community-downloads/)。

1. 啟動雲端簡單門戶並選擇**網路**。
2. 選擇**VPN 閘道**。
3. 從 VPN 閘道清單中,按一下指向網站 VPN 閘道。
4. 選擇**使用者**。
5. 按下 **「下載我的 VPN 配置**」。

    ![下載 VPN 組態](media/download-p2s-vpn-configuration.png)

6. 在 VPN 用戶端上導入配置。

    * 在[Windows 用戶端匯入設定](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)的說明
    * 有關在[macOS 或 OS X 上匯入設定](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)的說明

7. 連接到雲簡單。

## <a name="create-a-vlan-for-your-workload-vms"></a>為您的工作負載 VM 建立 VLAN

建立私有雲後,請創建一個 VLAN,您可以在其中部署工作負荷/應用程式 VM。

1. 在雲簡單門戶中,選擇 **"網路**"。
2. 點**選 VLAN/子網**。
3. 按下 **「創建 VLAN/子網**」。。

    ![建立 VLAN/子網路](media/create-new-vlan-subnet.png)

4. 為新的 VLAN/子網選擇**私有雲**。
5. 從清單中選擇 VLAN ID。  
6. 輸入子網名稱以標識子網。
7. 指定子網 CIDR 範圍和遮罩。  此範圍不得與任何現有子網重疊。
8. 按一下 [提交]  。

    ![建立 VLAN/子網詳細資訊](media/create-new-vlan-subnet-details.png)

將創建 VLAN/子網。  現在,您可以使用此 VLAN ID 在私有雲 vCenter 上創建分散式埠組。

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>將環境連線到 Azure 虛擬網路

雲簡單為您提供了專用雲的 ExpressRoute 電路。 您可以將 Azure 上的虛擬網路連接到 ExpressRoute 電路。 有關設定連線的完整詳細資訊,[請依照 Azure 虛擬網路連線中的步驟使用 ExpressRoute](https://docs.microsoft.com/azure/vmware-cloudsimple/cloudsimple-azure-network-connection)。

## <a name="sign-in-to-vcenter"></a>登入 vCenter

您現在可以登錄到 vCenter 來設置虛擬機器和策略。

1. 要訪問 vCenter,從雲簡單門戶開始。 在主頁上,在 **「常見任務**」下,按下 **「啟動 vSphere 用戶端**」。  選擇私有雲,然後單擊在私有雲上**啟動 vSphere 用戶端**。

    ![啟動 vSphere 用戶端](media/launch-vcenter-from-cloudsimple-portal.png)

2. 選擇您首選的 vSphere 客戶端以訪問 vCenter,並使用使用者名和密碼登錄。  預設為：
    * 使用者名稱:`CloudOwner@cloudsimple.local`
    * 密碼：`CloudSimple123!`

下一個過程中的 vCenter 螢幕來自 vSphere (HTML5) 用戶端。

## <a name="change-your-vcenter-password"></a>變更 vCenter 密碼

CloudSimple 建議您在首次登錄到 vCenter 時更改密碼。  
您設定的密碼必須滿足以下要求:

* 最長存留期:密碼必須每 365 天更改一次
* 限制重用:使用者無法重複使用前五個密碼中的任何一個
* 長度: 8 - 20 個字元
* 特殊字元:至少特殊字元
* 字母字元:至少一個大寫字元 A-Z 和至少一個小寫字元 a-z
* 數位:至少一個數字字元,0-9
* 最大相同的相鄰字元:三個

    示例:CC 或 CCC 作為密碼的一部分是可以接受的,但 CCCC 不是。

如果您設定的密碼不符合要求:

* 如果您使用 vSphere 快閃記憶體,它會回報錯誤
* 如果使用 HTML5 用戶端,它不報告錯誤。 用戶端不接受更改,舊密碼繼續工作。

## <a name="access-nsx-manager"></a>存取 NSX 管理員

NSX 管理員使用默認密碼部署。 

* 使用者名稱:**管理員**
* 密碼:**雲簡單123!**

您可以在雲端簡單門戶上找到 NSX 管理員完全限定的功能變數名稱 (FQDN) 和 IP 位址。

1. 啟動雲端簡單門戶並選擇**資源**。
2. 單擊要使用的私有雲。
3. 選擇**vSphere 管理網路**
4. 使用**NSX 管理員**的 FQDN 或 IP 位址並使用 Web 瀏覽器進行連接。

    ![尋找 NSX 管理員 FQDN](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>建立連接埠群組

要在 vSphere 中建立分散式埠組,請:

1. 按照[vSphere 網路指南](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)中的「添加分散式埠組」中的說明進行操作。
2. 設置分散式埠組時,請提供[在為工作負載 VM 創建 VLAN](#create-a-vlan-for-your-workload-vms)中創建的 VLAN ID。

## <a name="next-steps"></a>後續步驟

* [在 Azure 上取用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* [使用 Azure 快速路由連接到本地網路](on-premises-connection.md)
* [從本地設定網站到網站 VPN](vpn-gateway.md)
