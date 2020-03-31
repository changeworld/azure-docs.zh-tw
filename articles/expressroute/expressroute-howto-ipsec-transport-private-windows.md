---
title: Azure ExpressRoute 專用對等互連：配置 IPsec 傳輸模式 - Windows 主機
description: 如何透過 ExpressRoute 私人對等互連使用 GPO 和 OU 在 Azure Windows VM 與內部部署 Windows 主機之間啟用 IPsec 傳輸模式。
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: 1bc33047d31262af443cddc418853fbacd88aec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022000"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>設定 ExpressRoute 私人對等互連的 IPsec 傳輸模式

本文將協助您透過 ExpressRoute 私人對等互連，在執行 Windows 的 Azure VM 與內部部署 Windows 主機之間建立傳輸模式的 IPsec 通道。 本文中的步驟會使用群組原則物件建立此組態。 雖然不使用組織單位 (OU) 和群組原則物件 (GPO) 也可以建立此組態，但 OU 和 GPO 的搭配使用將有助於簡化您對安全性原則的控制，並且可讓您快速相應增加。 本文中的步驟假設您已有 Active Directory 組態，且您熟悉 OU 和 GPO 的使用方式。

## <a name="about-this-configuration"></a>關於此組態

下列步驟中的組態會搭配使用單一 Azure 虛擬網路 (VNet) 與 ExpressRoute 私人對等互連。 不過，此組態可以跨多個 Azure Vnet 和內部部署網路。 本文將協助您定義 IPsec 加密原則，並將其套用至屬於相同 OU 的一組 Azure VM 和內部部署主機。 您可以僅針對目的地連接埠為 8080 的 HTTP 流量設定 Azure VM (vm1 和 vm2) 與內部部署 host1 之間的加密。 您可以根據自己的需求建立不同類型的 IPsec 原則。

### <a name="working-with-ous"></a>使用 OU 

與 OU 相關聯的安全性原則會透過 GPO 推送到電腦。 除了將原則套用至單一主機以外，使用 OU 的優點還包括：

* 建立原則與 OU 的關聯，可確保屬於相同 OU 的電腦會使用相同的原則。
* 若變更與 OU 相關聯的安全性原則，變更將會套用至 OU 中的所有主機。

### <a name="diagrams"></a>圖表

下圖顯示互相連線和指派的 IP 位址空間。 Azure VM 和內部部署主機皆執行 Windows 2016。 Azure VM 和內部部署 host1 屬於相同網域。 Azure VM 和內部部署主機可使用 DNS 正確解析名稱。

[![1]][1]

此圖表會顯示在 ExpressRoute 私人對等互連中傳輸的 IPsec 通道。

[![4]][4]

### <a name="working-with-ipsec-policy"></a>使用 IPsec 原則

在 Windows 中，加密會與 IPsec 原則相關聯。 IPsec 原則會決定要保護的 IP 流量，以及套用至 IP 封包的安全性機制。
**IPSec 原則**由下列項目所組成：**篩選清單**、**篩選動作**和**安全性規則**。

設定 IPsec 原則時，請務必了解下列 IPsec 原則術語：

* **IPsec 原則：** 規則的集合。 在任一時間都只能有一個作用中 (「已指派」) 的原則。 每個原則可以有一或多個規則，而這些規則可以全部同時啟用。 同一時間只能為一部電腦指派一個作用中的 IPsec 原則。 不過，在 IPsec 原則中，您可以定義多個在不同情況下執行的動作。 每一組 IPsec 規則分別與一份篩選清單相關聯，而對套用規則的網路流量類型產生影響。

* **篩選清單：** 篩選清單是一或多個篩選條件的組合。 一份清單可包含多個篩選條件。 篩選條件會根據 IP 位址範圍、通訊協定或甚至特定的通訊協定連接埠，定義要允許、保護還是封鎖通訊。 每個篩選條件分別會比對一組特定的條件；例如，透過特定目的地連接埠從特定子網路傳送至特定電腦的封包。 當網路條件相符其中一或多個篩選條件時，就會啟動篩選清單。 每個篩選條件都會定義在特定的篩選清單中。 不同的篩選清單之間不可共用篩選條件。 不過，給定的篩選清單可以併入數個 IPsec 原則中。 

* **篩選動作：** 安全性方法會定義電腦在 IKE 交涉期間提供的一組安全性演算法、通訊協定和金鑰。 篩選動作是安全性方法的清單，依偏好程度的順序排名。  當電腦交涉 IPsec 工作階段時，它會根據儲存在篩選動作清單中的安全性設定接受或傳送建議項目。

* **安全性規則：** 規則可控制 IPsec 原則保護通訊的方法和時機。 它會使用**篩選清單**和**篩選動作**，建立據以建置 IPsec 連線的 IPsec 規則。 每個原則可以有一或多個規則，而這些規則可以全部同時啟用。 每個規則都包含 IP 篩選清單，以及在符合該篩選清單時所將執行的安全性動作集合：
  * IP 篩選動作
  * 驗證方法
  * IP 通道設定
  * 連線類型

[![5]][5]

## <a name="before-you-begin"></a>開始之前

請確保符合下列必要條件︰

* 您必須有正常運作的 Active Directory 組態可用來實作群組原則設定。 如需 GPO 的詳細資訊，請參閱[群組原則物件](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx)。

* 您必須擁有作用中的 ExpressRoute 線路。
  * 如需建立 ExpressRoute 線路的相關資訊，請參閱[建立 ExpressRoute 路線](expressroute-howto-circuit-arm.md)。 
  * 確認連線提供者已啟用線路。 
  * 確認您已為線路設定 Azure 私人對等互連。 請參閱 [設定路由](expressroute-howto-routing-arm.md) 一文，以取得路由指示。 
  * 確認您已建立並完整佈建 VNet 和虛擬網路閘道。 請依照指示[為 ExpressRoute 建立虛擬網路閘道](expressroute-howto-add-gateway-resource-manager.md)。 ExpressRoute 的虛擬網路閘道會使用 GatewayType 'ExpressRoute'，而不是 VPN。

* ExpressRoute 虛擬網路閘道必須連線至 ExpressRoute 線路。 如需詳細資訊，請參閱[將 VNet 連線至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)。

* 確認 Azure Windows VM 已部署至 VNet。

* 確認內部部署主機與 Azure VM 之間已有連線。

* 確認 Azure Windows VM 和內部部署主機可使用 DNS 正確解析名稱。

### <a name="workflow"></a>工作流程

1. 建立 GPO 並建立它與 OU 的關聯。
2. 定義 IPsec **篩選動作**。
3. 定義 IPsec **篩選清單**。
4. 建立含有**安全性規則**的 IPsec 原則。
5. 將 IPsec GPO 指派給 OU。

### <a name="example-values"></a>範例值

* **網域名稱：** ipsectest.com

* **OU：** IPSecOU

* **內部部署 Windows 電腦：** host1

* **Azure Windows VM：** vm1、vm2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. 創建 GPO

1. 若要建立連結至 OU 的新 GPO，請開啟群組原則管理嵌入式管理單元，並尋找 GPO 所將連結的 OU。 在此範例中，OU 的名稱為 **IPSecOU**。 

   [![9]][9]
2. 在群組原則管理嵌入式管理單元中選取 OU，然後按一下滑鼠右鍵。 在下拉式清單中，按一下 [在這個網域中建立 GPO 並連結到...]****。

   [![10]][10]
3. 為 GPO 指定直覺式名稱，以便後續能輕鬆找出。 按一下 [確定]**** 以建立並連結 GPO。

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. 啟用 GPO 連結

若要將 GPO 套用至 OU，不但必須將 GPO 連結至 OU，還必須啟用連結。

1. 找出您所建立的 GPO，並按一下滑鼠右鍵，然後從下拉式清單中選取 [編輯]****。
2. 若要將 GPO 套用至 OU，請選取 [啟用連結]****。

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. 定義 IP 篩選器操作

1. 在下拉式清單中以滑鼠右鍵按一下 [Active Directory 上的 IP 安全性原則]****，然後按一下 [管理 IP 篩選清單和篩選動作...]****。

   [![15]][15]
2. 在 [管理篩選動作]**** 索引標籤上，按一下 [新增]****。

   [![16]][16]

3. 在 [IP 安全性篩選動作精靈]**** 上，按 [下一步]****。

   [![17]][17]
4. 為篩選動作指定直覺式名稱，以方便後續尋找。 在此範例中，篩選動作的名稱為 **myEncryption**。 您也可以新增描述。 然後按 [下一步]****。

   [![18]][18]
5. **交涉安全性**可讓您定義無法對另一部電腦建立 IPsec 時的行為。 選取 [交涉安全性]****，然後按 [下一步]****。

   [![19]][19]
6. 在 [與不支援 IPsec 的電腦通訊]**** 頁面上，選取 [不允許不安全的通訊]****，然後按 [下一步]****。

   [![20]][20]
7. 在 [IP 流量和安全性]**** 頁面上選取 [自訂]****，然後按一下 [設定...]****。

   [![21]][21]
8. 在 [自訂安全性方法設定]**** 頁面上，選取 [資料完整性和加密 (ESP)：SHA1、3DES]****。 然後按一下 **[確定]**。

   [![22]][22]
9. 在 [管理篩選動作]**** 頁面上，您會看到 **myEncryption** 篩選條件已成功新增。 按一下 **關閉**。

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. 定義 IP 篩選器清單

建立篩選清單，並為其指定目的地連接埠為 8080 的加密 HTTP 流量。

1. 若要限定哪些類型的流量必須加密，請使用 **IP 篩選清單**。 在 [管理 IP 篩選清單]**** 索引標籤中按一下 [新增]****，以新增 IP 篩選清單。

   [![24]][24]
2. 在 [名稱]**** 欄位中，輸入 IP 篩選清單的名稱。 例如 **azure-onpremises-HTTP8080**。 然後，按一下"**添加**"。

   [![25]][25]
3. 在 [IP 篩選描述和鏡像屬性]**** 頁面上，選取 [鏡像]****。 鏡像設定會比對雙向傳輸、而支援雙向通訊的封包。 然後按一下 [下一步]****。

   [![26]][26]
4. 在 [IP 流量來源]**** 頁面上，從 [來源位址：]**** 下拉式清單中選擇 [特定的 IP 位址或子網路]****。 

   [![27]][27]
5. 指定 IP 流量的來源位址下方的 [IP 位址或子網路：]****，然後按 [下一步]****。

   [![28]][28]
6. 指定 [目的地位址：]**** 下方的 IP 位址或子網路。 然後按 [下一步]****。

   [![29]][29]
7. 在 [IP 通訊協定類型]**** 上，選取 [TCP]****。 然後按 [下一步]****。

   [![30]][30]
8. 在 [IP 通訊協定連接埠]**** 頁面上，選取 [從任何連接埠]**** 和 [到此連接埠：]****。 在文字方塊中輸入 **8080**。 這些設定會指定只有目的地連接埠 8080 的 HTTP 流量會加密。 然後按 [下一步]****。

   [![31]][31]
9. 檢視 IP 篩選清單。  IP 篩選器清單的組態 **azure-onpremises-HTTP8080** 會對符合下列準則的所有流量觸發加密程序：

   * 10.0.1.0/24 (Azure Subnet2) 中的任何來源位址
   * 10.2.27.0/25 (內部部署子網路) 中的任何目的地位址
   * TCP 通訊協定
   * 目的地連接埠 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. 編輯 IP 篩選器清單

若要為相同類型的反向流量 (從內部部署主機到 Azure VM) 加密，您需要第二個 IP 篩選條件。 設定新篩選條件的程序，與您用來設定第一個 IP 篩選條件的程序相同。 兩者只有來源子網路和目的地子網路不相同。

1. 若要將新的 IP 篩選條件新增至 IP 篩選清單，請選取 [編輯]****。

   [![33]][33]
2. 在 [IP 篩選清單]**** 頁面上，按一下 [新增]****。

   [![34]][34]
3. 使用下列範例中的設定建立第二個 IP 篩選條件：

   [![35]][35]
4. 建立第二個 IP 篩選條件後，IP 篩選清單將顯示如下：

   [![36]][36]

如果必須在內部部署位置與 Azure 子網路之間加以保護應用程式，您可以新增 IP 篩選清單，而不要修改現有的 IP 篩選清單。 建立 2 個 IP 篩選清單與相同 IPsec 原則的關聯，可提供較高的彈性，因為特定 IP 篩選清單可以隨時修改或移除，而不會影響到其他 IP 篩選清單。

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. 創建 IPsec 安全性原則 

建立含有安全性規則的 IPsec 原則。

1. 選取與 OU 相關聯的 **Active directory 上的 IP 安全性原則**。 按一下滑鼠右鍵，然後選取 [建立 IP 安全性原則]****。

   [![37]][37]
2. 為安全性原則命名。 例如 **policy-azure-onpremises**。 然後按 [下一步]****。

   [![38]][38]
3. 不選取核取方塊而直接按 [下一步]****。

   [![39]][39]
4. 確認已選取 [編輯屬性]**** 核取方塊，然後按一下 [完成]****。

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. 編輯 IPsec 安全性原則

將 IPsec 原則新增至您先前設定的 [IP 篩選清單]**** 和 [篩選動作]****。

1. 在 HTTP 原則屬性的 [規則]**** 索引標籤上，按一下 [新增]****。

   [![41]][41]
2. 在 [歡迎] 頁面中按 [下一步]****。

   [![42]][42]
3. 規則會提供用來定義 IPsec 模式的選項：通道模式或傳輸模式。

   * 在通道模式中，原始封包會以一組 IP 標頭封裝。 通道模式會對原始封包的 IP 標頭加密，以保護內部路由資訊。 通道模式廣泛運用在站對站 VPN 部署的閘道之間。 通道模式通常用於主機之間的端對端加密。

   * 傳輸模式只會對承載和 ESP 結尾加密；原始封包的 IP 標頭不會加密。 在傳輸模式中，封包的 IP 來源和 IP 目的地不會變更。

   選取 [此規則未指定通道]****，然後按 [下一步]****。

   [![43]][43]
4. [網路類型]**** 會定義要與安全性原則相關聯的網路連線。 選取 [所有網路連線]****，然後按 [下一步]****。

   [![44]][44]
5. 選取您先前建立的 IP 篩選清單 **azure-onpremises-HTTP8080**，然後按 [下一步]****。

   [![45]][45]
6. 選取您先前建立的現有篩選動作 **myEncryption**。

   [![46]][46]
7. Windows 支援四種不同類型的驗證：Kerberos、憑證、NTLMv2 和預先共用金鑰。 由於我們使用已加入網域的主機，請選取 [Active Directory 預設值 (Kerberos V5 通訊協定)]****，然後按 [下一步]****。

   [![47]][47]
8. 新的原則會建立安全性規則：**azure-onpremises-HTTP8080**。 按一下 [確定]****。

   [![48]][48]

IPsec 原則會要求目的地連接埠 8080 上的所有 HTTP 連線使用 IPsec 傳輸模式。 由於 HTTP 是純文字通訊協定，啟用安全性原則將可確保資料在透過 ExpressRoute 私人對等互連傳輸時會進行加密。 相較於採用進階安全性的 Windows 防火牆，Active Directory 的 IP 安全性原則在設定上會較複雜，但也容許較大程度的 IPsec 連線自訂。

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. 將 IPsec GPO 分配給 OU

1. 檢視原則。 安全性群組原則已定義，但尚未指派。

   [![49]][49]
2. 若要將安全性群組原則指派給 OU **IPSecOU**，請以滑鼠右鍵按一下安全性原則，然後選擇 [指派]****。
   屬於該 OU 的每部電腦都會被指派安全性群組原則。

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>檢查流量加密

若要查看在 OU 上套用加密 GPO，請在所有 Azure VM 上和 host1 中安裝 IIS。 每個 IIS 都會進行自訂，以回應連接埠 8080 上的 HTTP 要求。
若要驗證加密，您可以在 OU 中的所有電腦上安裝網路 sniffer (例如 Wireshark)。
Powershell 指令碼會以 HTTP 用戶端的形式運作，以產生連接埠 8080 上的 HTTP 要求：

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
下列網路擷取顯示內部部署 host1 使用顯示篩選 ESP 針對加密流量進行比對的結果：

[![51]][51]

如果您在內部部署 (HTTP 用戶端) 上執行 Powershell 指令碼，在 Azure VM 中的網路擷取將會顯示類似的追蹤。

## <a name="next-steps"></a>後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "透過 ExpressRoute 執行 IPsec 傳輸模式的網路圖表"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec 特殊流量"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPsec 原則"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "群組原則中的組織單位"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "建立與 OU 相關聯的 GPO"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "將名稱指派給與 OU 相關聯的 GPO"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "編輯 GPO"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "管理 IP 篩選清單和篩選動作"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "新增篩選動作"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "動作精靈"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "篩選動作名稱"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "篩選動作"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "指定建立不安全連線的行為"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "安全性機制"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "自訂安全性方法"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "篩選動作清單"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "新增 IP 篩選清單"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "將 HTTP 流量新增至 IP 篩選條件"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "比對雙向的封包"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "選取來源子網路"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "來源網路"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "目的地網路"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "通訊協定"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "來源連接埠和目的地連接埠"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "篩選清單"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "HTTP 流量的 IP 篩選清單"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "新增第二個 IP 篩選條件"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP 篩選清單 - 第二個項目"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP 篩選清單 - 第二個項目"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "建立 IP 安全性原則"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "IPsec 原則的名稱"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPsec 原則精靈"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "編輯 IPsec 原則"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "將新的安全性規則新增至 IPsec 原則"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "建立新的安全性規則"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "傳輸模式"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "網路類型"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "選取現有的 IP 篩選清單"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "選取現有的篩選動作"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "選取驗證方法"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "安全性原則建立程序的結尾"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "IPsec 原則已連結至 GPO，但未指派"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "指派給 GPO 的 IPsec 原則"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "擷取 IPsec 加密流量"
