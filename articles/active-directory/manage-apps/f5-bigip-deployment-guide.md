---
title: Azure AD 使用 F5 部署的安全混合式存取指南 |Microsoft Docs
description: 此教學課程說明如何在 Azure IaaS 中部署 F5 BIG IP 虛擬 Edition (VE) VM 以進行安全的混合式存取
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f962bf131b87f17712186145b8c8b8e6090f7002
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730651"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>在 Azure IaaS 中部署 F5 大型 IP 虛擬 Edition VM 以進行安全混合式存取的教學課程

本教學課程會逐步引導您完成在 Azure IaaS 中部署大型 IP Vitural Edition (VE) 的端對端程式。 在本教學課程結尾處，您應該會有：

- 完整準備的大型 IP 虛擬機器 (VM) 模型化安全的混合式存取 (SHA) 概念證明

- 用於測試新的大型 IP 系統更新和修補程式的預備實例

## <a name="prerequisites"></a>Prerequisites

不過，我們不需要在前面加上 F5 的大型 IP 體驗或知識，而是建議您熟悉自己的 [F5 大型 ip 術語](https://www.f5.com/services/resources/glossary)。 在 Azure 中部署 SHA 的大型 IP 需要：

- 付費的 Azure 訂用帳戶或免費的12個月 [試用訂](https://azure.microsoft.com/free/)用帳戶。

- 下列任一 F5 BIG IP 授權 Sku

  - F5 大型 IP®最佳套件組合

  - F5 BIG-IP Access Policy Manager™ (APM) 獨立授權

  - F5 大型 IP 存取原則管理員™ (APM) 現有的大型 IP F5 大型 IP®本機流量管理員™ (LTM) 的附加元件授權
  
  - 90天的大型 IP 完整功能 [試用版授權](https://www.f5.com/trial/big-ip-trial.php)。

-  (SAN) 憑證的萬用字元或主體替代名稱，可透過安全通訊端層 (SSL) 發佈 web 應用程式。 [讓我們加密](https://letsencrypt.org/) 提供免費的90天憑證來進行測試。

- SSL 憑證，用於保護 BIG-IPs 管理介面。 如果憑證的主體對應至大型 IP 的完整功能變數名稱 (FQDN) ，則可以使用用來發佈 web 應用程式的憑證。 例如，以 subject *. contoso.com 定義的萬用字元憑證適合 `https://big-ip-vm.contoso.com:8443`

VM 部署和基本系統組態大約需要30分鐘的時間，此時您的大型 IP 平臺將會準備好執行 [此處](f5-aad-integration.md)所列的任何 SHA 案例。

針對測試案例，本教學課程假設大型 IP 將會部署到包含 Active Directory (AD) 環境的 Azure 資源群組中。 環境應該包含網域控制站 (DC) ，以及 (IIS) Vm 的 web 主機。 將這些伺服器包含在大型 IP VM 的其他位置也是很好的，提供大型 IP 對支援特定案例所需的每個角色都可以看到。 也支援透過 VPN 連線將大型 IP VM 連接到另一個環境的案例。

如果您沒有此處提及的專案來進行測試，您可以使用此 [腳本](https://github.com/Rainier-MSFT/Cloud_Identity_Lab)將整個 AD 網域環境部署到 Azure。 您也可以使用此 [編寫腳本的自動化](https://github.com/jeevanbisht/DemoSuite)，以程式設計的方式，將範例測試應用程式的集合部署至 IIS web 主機。

>[!NOTE]
>[Azure 入口網站](https://portal.azure.com/#home)不斷演進，因此本教學課程中的某些步驟可能會與 Azure 入口網站中觀察到的實際配置不同。

## <a name="azure-deployment"></a>Azure 部署

大型 IP 可以部署在不同的拓撲中。 本指南著重于單一網路介面 (NIC) 部署。 但是，如果您的大型 IP 部署需要多個網路介面來提供高可用性、網路隔離或超過 1 GB 的輸送量，請考慮使用 F5 預先編譯 [Azure Resource Manager (ARM) 範本](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)。

完成下列工作，以從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps)部署大型 IP VE。

1. 使用有權建立 Vm 的帳戶登入 [Azure 入口網站](https://portal.azure.com/#home) 。 例如，參與者

2. 在頂端的功能區搜尋方塊中，輸入 **marketplace**，然後 **輸入**

3. 在 Marketplace 篩選中輸入 **F5** ，然後 **輸入**

4. 從頂端功能區選取 [ **+ 新增** ]，然後在 marketplace 篩選中輸入 **F5** ，然後 **輸入**

5. 選取 [ **f5 BIG-ip Virtual Edition] (BYOL)**  >  **選取軟體方案**  >  **F5 BIG-ip VE-ALL (BYOL，2個開機位置)**

6. 選取 [建立]  。

![此影像顯示選取軟體方案的步驟](./media/f5ve-deployment-plan/software-plan.png)

7. 逐步執行 **基本** 功能表並使用下列設定

 |  專案詳細資料     |  值     |
 |:-------|:--------|
 |訂用帳戶|大型 IP VM 部署的目標訂用帳戶|
 |資源群組 | 現有的 Azure 資源群組：大型 IP VM 將會部署到或建立一個。 應該是 DC 和 IIS Vm 的相同資源群組|
 | **執行個體詳細資料**|  |
 |虛擬機器名稱| 大型 IP-VM 範例 |
 |區域 | 大型 IP VM 的目標 Azure 地理位置 |
 |可用性選項| 只有在生產環境中使用 VM 時才啟用|
 |映像| F5 BIG-IP VE-ALL (BYOL，2個啟動位置) |
 |Azure Spot 執行個體| 否，但如果適用，可以隨意啟用 |
 |大小| 最小規格應該是2個 vcpu 和 8 Gb 記憶體|
 |**系統管理員帳戶**|  |
 |驗證類型|現在請選取 [密碼]。 您可以稍後切換至金鑰組 |
 |使用者名稱|將建立為大型 IP 本機帳戶以存取其管理介面的身分識別。 使用者名稱區分大小寫。|
 |密碼|使用強式密碼保護系統管理員存取權|
 |**輸入連接埠規則**|  |
 |公用輸入連接埠|無|

8. 選取 **[下一步]：** 保留所有預設值，然後選取 **[下一步：網路]**。

9. 在 [ **網路** 功能] 功能表上，完成這些設定。

 |網路介面|      值 |
 |:--------------|:----------------|
 |虛擬網路|DC 和 IIS Vm 使用相同的 Azure VNet，或建立一個|
 |子網路| 與您的 DC 和 IIS Vm 相同的 Azure 內部子網，或建立一個|
 |公用 IP |  無|
 |NIC 網路安全性群組| 如果您在先前步驟中選取的 Azure 子網已與網路安全性群組（ (NSG) ;）相關聯，請選取 [無]。否則請選取 [基本]|
 |加速網路| 關閉 |
 |**負載平衡**|     |
 |負載平衡 VM| 否|

10. 選取 **[下一步：管理]** ，然後完成這些設定。

 |監視|    值 |
 |:---------|:-----|
 |詳細監視| 關閉|
 |開機診斷|使用自訂儲存體帳戶啟用。 允許透過 Azure 入口網站中的序列主控台選項連接到大 IP 安全殼層 (SSH) 介面。 選取任何可用的 Azure 儲存體帳戶|
 |**身分識別**|  |
 |系統指派的受控身分識別|關閉|
 |Azure Active Directory|大型 IP 目前不支援此選項|
 |**自動關機**|    |
 |啟用自動關機| 如果測試，請考慮將大型 IP VM 設定為每天關機|

11. 選取 **[下一步]： [** 預設值]，然後選取 **[下一步：標記]**。

12. 選取 **[下一步：檢查 + 建立]** ，以檢查您的大型 IP VM 設定，然後選取 [ **建立** ] 以開始部署。

13. 完整部署大型 IP VM 的時間通常是5分鐘。 [完成] 時，請選取 [ **移至資源**]，而不是展開 Azure 入口網站的左側功能表，然後選取 [ **資源群組** ] 以流覽至新的大 IP VM。 如果 VM 建立失敗，請選取 [上一頁 **] 和 [下一步]**。

## <a name="network-configuration"></a>網路組態

當大型 IP VM 第一次開機時，將會布建其 NIC，並使用動態主機設定通訊協定所發行的 **主要** 私人 IP (其所連接之 Azure 子網的 DHCP) 服務。 大型 IP 的流量管理作業系統會使用此 IP (TMOS) 來與通訊：

- 與其他主機和服務通訊

- 對外存取公用網際網路

- BIG-IPs web 設定和 SSH 管理介面的輸入存取

將其中一個管理介面公開到網際網路會增加 BIG-IPs 的受攻擊面，因此在部署期間不會以公用 IP 布建 BIG-IPs 的主要 IP。 相反地，將會為發佈服務布建次要內部 IP 和相關聯的公用 IP。
VM 公用 IP 與私人 IP 之間的1對1對應可讓外部流量抵達 VM。 不過，若要允許流量，也需要 Azure NSG 規則，與防火牆的方式大致相同。

下圖顯示在 Azure 中使用主要 IP 進行一般操作與管理的單一 NIC 部署，以及用於發佈服務的個別虛擬伺服器 Ip。
在這種安排中，NSG 規則允許目的地的遠端流量 `intranet.contoso.com` 路由傳送至已發佈服務的公用 ip，然後再轉送至大型 IP 虛擬伺服器。

![根據預設，映射會顯示單一 nic 部署 ](./media/f5ve-deployment-plan/single-nic-deployment.png) ，發給 Azure vm 的私人和公用 ip 一律為動態，因此可能會在每次重新開機 VM 時變更。 將 BIG-IPs 管理 IP 變更為靜態，並對用於發佈服務的次要 Ip 進行相同的動作，以避免未預期的連線問題。

1. 從您的大型 IP VM 功能表中，移至 [**設定**  >  **網路**]

2. 在 [網路] 視圖中，選取 [**網路介面**] 右邊的連結

![此影像會顯示網路設定](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>VM 名稱是在部署期間隨機產生的。

3. 在左側窗格中，選取 [ **IP** 設定]，然後選取 [ **ipconfig1** 資料列]。

4. 將 **IP 指派** 選項設定為 [靜態]，並視需要變更大型 IP vm 的主要 ip 位址。 然後選取 [ **儲存** 並關閉 ipconfig1] 功能表

>[!NOTE]
>您將使用此主要 IP 來連接和管理大型 IP VM。

5. 選取頂端功能區上的 [ **+ 新增** ]，並提供次要私人 IP 的名稱，例如 vm1-ipconfig2

6. 將私人 IP 位址設定的 **配置** 選項設定為 [ **靜態**]。 提供下一個較高或較低的連續 IP，有助於讓事情有條理。

7. 設定要 **建立關聯** 的公用 IP 位址，並選取 [**建立**]

8. 提供新公用 IP 位址的名稱，例如，「大 IP-VM_ipconfig2_Public

9. 如果出現提示，請將 **SKU** 設定為標準

10. 如果出現提示，請將 **層級** 設定為 **全域** 和

11. 將 **指派** 選項設定為 [ **靜態**]，然後選取 **[確定]** 兩次

您的大型 IP VM 現在已準備好設定：

- **主要私人 IP**：專門用來透過其 Web 設定公用程式和 SSH 來管理大 IP VM。 大型 IP 系統會使用它來作為其 **自我 IP** ，以連接到發佈的後端服務。 此外，它也會連接到外部服務，例如 NTP、AD 和 LDAP。

- **次要私人 IP**：建立大型 IP APM 虛擬伺服器來接聽已發佈之服務的輸入要求時，所使用的私人 ip (s) 。

- **公用 ip**：與次要私人 ip 相關聯，可讓來自公用網際網路的用戶端流量連接到已發佈之服務 (的大型 IP 虛擬伺服器) 

此範例說明 VM 公用和私人 Ip 之間的1對1關聯性。 Azure VM NIC 只能有一個主要 IP，而任何額外建立的 Ip 一律稱為「次要」。

>[!NOTE]
>您將需要用來發佈大型 IP 服務的次要 IP 對應。

![此圖顯示所有次要 Ip](./media/f5ve-deployment-plan/secondary-ips.png)

若要使用「大型 IP **存取引導式** 設定」來執行 SHA，請重複步驟5-11，為您計畫透過大型 ip APM 發佈的每個額外服務，建立額外的私人和公用 IP 配對。 如果使用 BIG-IPs **Advanced Configuration** 發行服務，也可以使用相同的方法。 不過，在這種情況下，您可以選擇使用 [伺服器名稱指標 (SNI) ](https://support.f5.com/csp/#/article/K13452) 設定來避免公用 IP 額外負荷。 在此設定中，大型 IP 虛擬伺服器將會接受它接收的所有用戶端流量，並將其路由傳送到其目的地。

## <a name="dns-configuration"></a>DNS 組態

您必須設定 DNS，讓用戶端將已發佈的 SHA 服務解析成您的大型 IP VM 的公用 IP (s) 。

下列步驟假設您在 Azure 中管理 SHA 服務所使用之公用網域的 DNS 區域。 但是，不論您的 DNS 區域的管理位置為何，建立定位器記錄的相同 DNS 原則仍適用。

1. 如果尚未開啟，請展開入口網站的左側功能表，然後透過 [ **資源群組** ] 選項流覽至您的大 IP VM

2. 從您的大型 IP VM 功能表中，移至 [**設定**  >  **網路**]

3. 在 [大型 IP-Vm 網路] 視圖中，從下拉式 IP 設定清單中選取第一個次要 IP，然後選取其 **NIC 公用 IP** 的連結

![顯示 NIC 公用 IP 的螢幕擷取畫面](./media/f5ve-deployment-plan/networking.png)

4. 在左窗格的 [設定] 區段下方 **，選取 [** **設定**] 以顯示所選次要 IP 的 [公用 IP] 和 [DNS 內容] 功能表

5. 選取並 **建立** 別名記錄，然後從下拉式清單中選取您的 **DNS 區域** 。 如果 DNS 區域尚不存在，則可在 Azure 之外進行管理，或者您可以針對您在 Azure AD 中驗證的網域尾碼建立一個。

6. 使用下列詳細資料來建立第一個 DNS 別名記錄：

 | 欄位 | 值 |
 |:-------|:-----------|
 |訂用帳戶| 與大型 IP VM 相同的訂用帳戶|
 |DNS 區域| 您已發佈的網站將會使用之已驗證網域尾碼的授權 DNS 區域，例如 www.contoso.com |
 |Name | 您指定的主機名稱會解析為與所選取次要 IP 相關聯的公用 IP。 請務必將正確的 DNS 定義為 IP 對應。 請參閱網路功能的 [內容] 區段中的最後一個影像，例如 intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |TTL 單位 | 小時 |

7. 選取 [ **建立** Azure] 以將這些設定儲存至公用 DNS。

8. 將 **DNS 名稱標籤保留 (選用)** ，然後選取 [ **儲存** ]，然後關閉 [公用 IP] 功能表。

9. 重複步驟1到6，以使用大 IP 的引導式設定來為您打算發佈的每個服務建立額外的 DNS 記錄。

  有了 DNS 記錄之後，您就可以使用任何線上工具（例如 [dns 檢查](https://dnschecker.org/) 程式）來確認已建立的記錄已成功傳播到所有全域公用 DNS 伺服器。

  如果您使用外部提供者（例如 [GoDaddy](https://www.godaddy.com/)）來管理 DNS 網域命名空間，則必須使用自己的 dns 管理設備來建立記錄。

>[!NOTE]
>如果測試和經常切換 DNS 記錄，您也可以使用電腦的本機主機檔案。 您可以在鍵盤上按 Win + R，然後在 [執行] 方塊中提交文字 **驅動程式** ，來存取 Windows 電腦上的 localhosts 檔案。 請注意，localhost 記錄只會提供本機電腦的 DNS 解析，而不會提供其他用戶端的 DNS 解析。

## <a name="client-traffic"></a>用戶端流量

根據預設，Azure Vnet 和相關聯的子網是無法接收網際網路流量的私人網路。 您的大型 IP VM NIC 應附加至您在部署期間指定的 NSG。 若要讓外部網路流量到達大型 IP VM，您必須定義輸入 NSG 規則，以允許埠 443 (HTTPS) 和 80 (HTTP) 從公用網際網路。

1. 從大型 IP VM 的主要 **[總覽**] 功能表中，選取 [**網路** 功能]

2. 選取 [ **新增** 輸入規則]，輸入下列 NSG 規則屬性：

 |     欄位   |   值        |
 |:------------|:------------|
 |來源| 任意|
 |來源連接埠範圍| *|
 |目的地 IP 位址|所有大型 IP-VM 次要私人 Ip 的逗點分隔清單|
 |目的地連接埠| 80,443|
 |通訊協定| TCP |
 |動作| Allow|
 |優先順序|介於 100-4096 之間的最小可用值|
 |Name | 描述性名稱，例如： `BIG-IP-VM_Web_Services_80_443`|

3. 選取 [ **新增** ] 以認可變更，然後關閉 [ **網路** 功能] 功能表。

來自任何位置的 HTTP 和 HTTPS 流量現在都可以連線到您所有的大型 IP Vm 次要介面。 允許埠80可讓大 IP APM 自動將使用者從 HTTP 重新導向至 HTTPS。 您可以視需要編輯此規則，以新增或移除目的地 Ip。

## <a name="manage-big-ip"></a>管理大型 IP

系統會透過其 web 設定 UI 來管理大型 IP 系統，您可以使用下列其中一種建議的方法來加以存取：

- 從大型 IP 的內部網路內的電腦

- 從連線到大 IP VM 內部網路的 VPN 用戶端

- 透過[Azure AD 應用程式 Proxy](./application-proxy-add-on-premises-application.md)發佈

您必須決定最適合的方法，才能繼續進行其餘設定。 如有必要，您可以使用公用 IP 設定大型 IP 的主要 IP，從網際網路直接連線至 web 設定。 然後新增 NSG 規則，以允許該主要 IP 的8443流量。 請務必將來源限制在您自己的受信任 IP，否則任何人都可以連接。

準備好之後，請確認您可以連線到大型 IP VM 的 web 設定，並使用 VM 部署期間指定的認證登入：

- 如果您是從內部網路上的 VM 或透過 VPN 連線，請直接連接到 BIG-IPs 的主要 IP 和 web 設定埠。 例如： `https://<BIG-IP-VM_Primary_IP:8443` 。 您的瀏覽器會提示連線不安全，但您可以忽略提示，直到設定大 IP 為止。 如果瀏覽器堅持封鎖存取，請清除其快取，然後再試一次。

- 如果您透過應用程式 Proxy 發佈 web 設定，則請使用定義的 URL 來存取外部的 web 設定，而不需要附加埠（例如） `https://big-ip-vm.contoso.com` 。 內部 URL 必須使用 web config 埠來定義，例如， `https://big-ip-vm.contoso.com:8443` 

您也可以透過其基礎 SSH 環境來管理大型 IP 系統，此環境通常用於命令列 (CLI) 工作和根層級存取。 有幾個選項可用來連接到 CLI，包括：

- [Azure 防禦服務](../../bastion/bastion-overview.md)：允許從任何位置對 vNET 內的任何 VM 進行快速且安全的連接

- 透過 JIT 方法直接透過 SSH 用戶端（例如 PuTTY）連接

- 序列主控台：在入口網站中 [Vm] 功能表的 [支援與疑難排解] 區段底部提供。 它不支援檔案傳輸。

- 如同 web 設定，您也可以透過使用公用 IP 設定大型 IP 的主要 IP，並新增 NSG 規則以允許 SSH 流量，從網際網路直接連線至 CLI。 同樣地，如果使用此方法，請務必將來源限制為您自己的信任 IP。  

## <a name="big-ip-license"></a>大型 IP 授權

必須先啟用並布建 APM 模組的大型 IP 系統，才能針對發行服務和 SHA 進行設定。

1. 重新登入 web 設定，並在 [**一般] 屬性** 頁面上選取 [**啟用**]

2. 在 [ **基底註冊金鑰** ] 欄位中，輸入 F5 所提供的區分大小寫金鑰

3. 將 [ **啟用] 方法** 保持設定為 [ **自動** ]，然後選取 **[下一步]**，大 IP 將會驗證授權，並顯示 (EULA) 的使用者授權合約。

4. 選取 [ **接受** ] 並等候啟用完成，然後選取 [ **繼續**]。

5. 重新登入，然後在 [授權摘要] 頁面底部，選取 **[下一步]**。 大 IP 現在會顯示模組清單，這些模組會提供 SHA 所需的各種功能。 如果您沒有看到此選項，請從 [主要] 索引標籤移至 [**系統**  >  **資源** 布建]。

6. 檢查布建資料行中是否有 (APM) 的存取原則

![此影像顯示存取權布建](./media/f5ve-deployment-plan/access-provisioning.png)

7. 選取 [ **提交** 並接受警告]

8. 請耐心等候，然後等候大型 IP 完成新功能的照明。 它可能會在完全初始化之前迴圈數次。 

9. 當您準備好時，選取 [ **繼續** ]，然後從 [ **關於** ] 索引標籤選取 [ **執行**

>[!IMPORTANT]
>每次都有一個大型 IP VE 實例使用 F5 授權。 可能的原因可能是想要將授權從某個實例遷移到另一個實例，如果這樣做，請務必先 [撤銷](https://support.f5.com/csp/article/K41458656) 使用中實例的試用授權再解除委任，否則授權將會永久遺失。

## <a name="provision-big-ip"></a>布建大型 IP

保護進出 BIG-IPs web 設定的管理流量相當重要。 設定裝置管理憑證，以協助保護 web 設定通道的安全。

1. 從左側導覽列中，移至 **系統**  >  **憑證管理**  >  **流量憑證管理**  >  **SSL 憑證清單** 匯  >  **入**

2. 從 [匯 **入類型** ] 下拉式清單中，選取 [ **PKCS 12 (IIS)** ，然後 **選擇 [** 檔案]。 在接下來的幾個步驟中，找出具有主體名稱或 SAN 的 SSL web 憑證，以涵蓋您將指派大 IP VM 的 FQDN

3. 提供憑證的密碼，然後選取 [匯 **入**]

4. 從左側導覽列移至 [**系統**  >  **平臺**]

5. 設定具有完整主機名稱的大型 IP VM 和其環境的時區，後面接著 **更新**

![影像會顯示一般屬性](./media/f5ve-deployment-plan/general-properties.png)

6. 從左側導覽列中，移至 [**系統**  >  **配置**  >  **裝置**  >  **NTP** ]

7. 指定可靠的 NTP 來源，然後選取 [ **新增**]，然後選取 [ **更新**]。 例如， `time.windows.com`

您現在需要 DNS 記錄，以將先前步驟中所指定的 BIG-IPs FQDN 解析為其主要私人 IP。 您應將記錄新增至環境的內部 DNS，或新增至電腦的 localhost 檔案，以用來連線到大型 IP 的 web 設定。無論何種方式，當您直接連接到 web 設定時，都不應該再出現瀏覽器警告。 也就是說，不是透過應用程式 Proxy 或任何其他反向 Proxy。

## <a name="ssl-profile"></a>SSL 設定檔

作為反向 proxy，大型 IP 系統可以作為簡單轉送服務（亦稱為透明 proxy），或主動參與用戶端與伺服器之間交換的完整 proxy。
完整的 proxy 會建立兩個不同的連線：前端 TCP 用戶端連線和個別的後端 TCP 伺服器連線，並沿著中間的軟間距結合。 用戶端會連線到一端的 proxy 接聽程式（亦稱為 **虛擬伺服器**），而 proxy 會建立與後端伺服器分開的獨立連接。 這兩方都是雙向的。
在此完整 proxy 模式中，F5 BIG IP 系統能夠檢查流量，因此也可以與要求和回應互動。 某些功能（例如負載平衡和 web 效能優化）以及更高階的流量管理服務（例如應用層安全性、web 加速、頁面路由及安全的遠端存取）都會依賴此功能。
發佈以 SSL 為基礎的服務時，在用戶端和後端服務之間解密和加密流量的程式是由大型 IP SSL 設定檔處理。

有兩種類型的設定檔存在：

- **用戶端 ssl**：設定大型 IP 系統以使用 SSL 發佈內部服務的最常見方式，就是建立用戶端 ssl 設定檔。 使用用戶端 SSL 設定檔時，大型 IP 系統可以先將輸入用戶端要求解密，再將它們傳送到下游服務。 然後，它會在將輸出後端回應傳送給用戶端之前將其加密。

- **伺服器 SSL**：針對針對 HTTPS 設定的後端服務，您可以設定大 IP 以使用伺服器 SSL 設定檔。 使用伺服器 SSL 設定檔時，大 IP 會重新加密用戶端要求，再將其傳送至目的地後端服務。 當伺服器傳回加密的回應時，大型 IP 系統會透過已設定的用戶端 SSL 設定檔，將回應解密並重新加密，然後再傳送至用戶端。

用戶端和伺服器 SSL 設定檔的布建，將會為所有 SHA 案例預先設定並準備好使用的大型 IP。

1. 從左側導覽列中，移至 **系統**  >  **憑證管理**  >  **流量憑證管理**  >  **SSL 憑證清單** 匯  >  **入**

2. 從 [匯 **入類型** ] 下拉式清單中，選取 [ **PKCS 12 (IIS)**

3. 提供匯入憑證的名稱，例如  `ContosoWilcardCert`

4. 選取 [ **選擇** 檔案]，流覽至 [主體名稱] 對應至您計畫使用發佈服務之網域尾碼的 SSL web 憑證

5. 提供匯入憑證的 **密碼**，然後選取 [匯 **入**]

6. 從左側導覽列中，移至 [**本機流量**  >  **設定檔**  >  **SSL**  >  **用戶端**]，然後選取 [**建立**]

7. 在 [ **新增用戶端 Ssl 設定檔** ] 頁面上，為新的用戶端 ssl 設定檔提供唯一的易記名稱，並確定父設定檔已設定為 **clientssl**

![此影像顯示更新大 ip](./media/f5ve-deployment-plan/client-ssl.png)

8. 選取 **憑證金鑰鏈** 資料列中最右邊的核取方塊，然後選取 [**新增**]

9. 從三個下拉式清單中，選取您未使用複雜密碼匯入的萬用字元憑證，然後選取 [**新增**  >  **完成**]。

![此影像顯示更新大 ip contoso 萬用字元](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. 重複步驟6-9 以建立 **SSL 伺服器憑證設定檔**。 從頂端功能區中，選取 [ **SSL**  >  **伺服器**  >  **建立**]。

11. 在 [ **新的伺服器 Ssl 設定檔** ] 頁面上，為新的伺服器 ssl 設定檔提供唯一的易記名稱，並確定父設定檔已設定為 **serverssl**

12. 選取憑證和金鑰資料列的最右邊核取方塊，然後從下拉式清單中選取您匯入的憑證，然後再選取 [ **完成**]。

![此影像顯示更新大型 ip 伺服器所有設定檔](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>如果您無法購買 SSL 憑證，請不要絕望，您可以使用整合式自我簽署的大型 IP 伺服器和用戶端 SSL 憑證。 您只會在瀏覽器中看到憑證錯誤。

準備大型 IP 以進行 SHA 的最後一個步驟是確保它能夠找出其發行的資源，以及它依賴 SSO 的目錄服務。 大 IP 有兩個來源的名稱解析，從其本機/.../hosts 檔案開始，或者，如果找不到記錄，則大型 IP 系統會使用任何已設定的 DNS 服務。 Hosts file 方法不適用於使用 FQDN 的 APM 節點和集區。

1. 在 web 設定中，移至 [**系統**  >  **配置**  >  **裝置**  >  **DNS** ]。

2. 在 [ **Dns 查閱伺服器清單**] 中，輸入您的環境 DNS 伺服器的 IP 位址

3. 選取 **新增**  >  **更新**

您可以考慮使用 [LDAP](https://somoit.net/f5-big-ip/authentication-using-active-directory) 設定來驗證對 AD 的大型 ip 系統管理員，而不是管理本機的大型 ip 帳戶，以做為個別且選擇性的步驟。

## <a name="update-big-ip"></a>更新大型 IP

您必須更新您的大 IP VM，以將以 [案例為基礎的指引](f5-aad-integration.md)中所涵蓋的所有新功能解除鎖定。 您可以檢查系統 TMOS 版本將滑鼠指標停留在主頁面左上角的 BIG-IPs 主機名稱上方。 建議您執行 v15 和更新版本（可從 [F5 下載](https://downloads.f5.com/esd/productlines.jsp)中獲得）。 使用 [指導](https://support.f5.com/csp/article/K34745165) 方針來更新主要系統作業系統 (TMOS) 。

如果無法更新主要 TMOS，則請考慮至少使用下列步驟來升級引導式設定。

1. 從大型 IP web 設定的 [主要] 索引標籤中，移至 [**存取**  >  **引導式** 設定]

2. 在 [**引導式** 設定] 頁面上，選取 [**升級引導式** 設定]

![此影像顯示如何更新大型 ip](./media/f5ve-deployment-plan/update-big-ip.png)

3. 在 [**升級引導式** 設定] 對話方塊上，**選擇**[檔案] 以上傳已下載的使用案例套件，然後選取 **[上傳並安裝**]

4. 當升級完成時，請選取 [ **繼續**]。

## <a name="backup-big-ip"></a>備份大型 IP

現在完整布建了大 IP 系統，建議您完整備份其設定：

1. 移至 **系統** 封存  >    >  **建立**

2. 提供唯一的 **檔案名**，並使用複雜密碼啟用 **加密**

3. 將 [ **私密金鑰** ] 選項設定為 **包含** ，以同時備份裝置和 SSL 憑證

4. 選取 [ **完成** ] 並等候進程完成

5. 作業狀態將會顯示，並提供備份結果的狀態。 選取 [確定]

6. 選擇備份的連結並選取 [ **下載**]，以將使用者設定集 (UCS) 封存在本機。

選擇性步驟是，您也可以使用 [Azure 快照](../../virtual-machines/windows/snapshot-copy-managed-disk.md)集來備份整個系統磁片，這種方式與 web 設定備份會提供一些在 TMOS 版本之間進行測試或回復至全新系統的應變。

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>還原大型 IP

還原大型 IP 時，會遵循與備份類似的程式，也可以用來在大型 IP Vm 之間遷移。 您應該先觀察支援的升級路徑詳細資料，然後再匯入備份。

1. 移至 **系統**  >  封存

2. 選擇您想要還原之備份的連結，或選取 [ **上傳** ] 按鈕，流覽至先前儲存但不在清單中的 UCS 封存

3. 提供備份的複雜密碼，然後選取 [**還原**]

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>在撰寫本文時，AzVmSnapshot Cmdlet 只會根據日期還原最新的快照集。 快照集會儲存在 VM 資源群組的根目錄中。 請注意，還原快照集會重新開機 Azure VM，因此請謹慎進行。

## <a name="additional-resources"></a>其他資源

-   [在 Azure 中重設大型 IP VE 密碼](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [在不使用入口網站的情況下重設密碼](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [變更用於大型 IP VE 管理的 NIC](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [關於單一 NIC 設定中的路由](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure： Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>後續步驟

選取 [部署案例](f5-aad-integration.md) ，並開始執行。