---
title: 在 Azure Site Recovery 中部署設定伺服器
description: 本文說明如何使用 Azure Site Recovery 部署 VMware 災害復原的組態伺服器
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: a8ceb3df68ebe42f83c70ed62327bf59c0dfc225
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359810"
---
# <a name="deploy-a-configuration-server"></a>部署設定伺服器

當您使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 和實體伺服器災害復原到 Azure 時，便會部署內部部署設定伺服器。 設定伺服器會協調內部部署 VMware 與 Azure 之間的通訊。 它也會管理資料複寫。 本文會逐步引導您完成當您將 VMware VM 複寫至 Azure 時部署設定伺服器所需的步驟。 如果您需要設定實體伺服器複寫的設定伺服器，請參閱 [設定伺服器以進行實體伺服器到 Azure](physical-azure-set-up-source.md)的嚴重損壞修復。

> [!TIP]
> 若要瞭解設定伺服器在 Azure Site Recovery 架構中的角色，請參閱 [VMware 至 Azure 的損毀修復架構](vmware-azure-architecture.md)。

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>透過 OVA 範本部署設定伺服器

設定伺服器必須設定為高可用性 VMware VM，並具備特定的最低硬體和大小需求。 為了方便且輕鬆地部署，Site Recovery 提供可下載的開放式虛擬化應用程式 (OVA) 範本，以設定符合此處所列之所有強制需求的設定伺服器。

## <a name="prerequisites"></a>先決條件

下列各節摘要說明設定伺服器的最低硬體需求。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure Active Directory 許可權需求

您必須在 Azure Active Directory (Azure AD) 中設定下列許可權之一的使用者，才能向 Azure Site Recovery 服務註冊設定伺服器。

1. 使用者必須有應用程式開發人員角色，才能建立應用程式。
    - 若要確認，請登入 Azure 入口網站。</br>
    - 移至**Azure Active Directory**  >  **角色和系統管理員**。</br>
    - 確認已將應用程式開發人員角色指派給使用者。 如果沒有，請使用具有此許可權的使用者，或洽詢 [系統管理員以啟用許可權](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md#assign-roles)。
    
2. 如果無法指派應用程式開發人員角色，請確定已將 [ **使用者可以註冊應用程式** ] 旗標設為 **true** ，讓使用者建立身分識別。 若要啟用這些許可權：
    - 登入 Azure 入口網站。
    - 移至**Azure Active Directory**  >  **使用者設定**]。
    - 在 [ **應用程式註冊**] 下， **使用者可以註冊應用程式**，請選取 **[是]**。

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> *不支援*Active Directory 同盟服務。 使用透過 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)管理的帳戶。

## <a name="download-the-template"></a>下載範本

1. 在保存庫中，移至 [準備基礎結構]   > [來源]  。
2. 在 [準備來源]  中，選取 [+設定伺服器]  。
3. 在 [新增伺服器]  中，檢查 [VMware 的組態伺服器]  是否出現在 [伺服器類型]  中。
4. 下載設定伺服器的 OVA 範本。

   > [!TIP]
   >您也可以直接從 [Microsoft 下載中心](https://aka.ms/asrconfigurationserver)下載最新版的設定伺服器範本。

> [!NOTE]
> OVA 範本所提供的授權是有效期限為180天的評估授權。 在這段期間之後，您必須購買授權。

## <a name="import-the-template-in-vmware"></a>在 VMware 中匯入範本

1. 使用 VMware vSphere 用戶端，登入 VMware vCenter 伺服器或 vSphere ESXi 主機。
2. 在 [檔案 **] 功能表上選取 [** **部署 OVF 範本** ]，以啟動 [ **部署 OVF 範本** ] wizard。

     ![部署 OVF 範本](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. 在 [選取來源]  上，輸入所下載 OVF 的位置。
4. 在 [檢閱詳細資料]  上，選取 [下一步]  。
5. 在 [選取名稱和資料夾]  和 [選取組態]  上，接受預設設定。
6. 在 [選取儲存體]  上，於 [選取虛擬磁碟格式]  中選取 [Thick Provision Eager Zeroed]  ，以達到最佳效能。 使用精簡布建選項可能會影響設定伺服器的效能。
7. 在精靈的其餘頁面上，接受所有的預設設定。
8. 在 [準備要完成]**** 上：

    * 若要使用預設設定來設定 VM，請選取 [在**部署完成後開啟電源**  >  **]**。
    * 若要新增額外的網路介面，請清除 [在部署後開啟電源]****，然後選取 [完成]****。 預設會使用單一 NIC 部署設定伺服器範本。 您可以在部署後新增其他 NIC。

> [!IMPORTANT]
> 請勿變更資源設定，例如記憶體、核心和 CPU 限制，或在部署後修改或刪除設定伺服器上已安裝的服務或檔案。 這些類型的變更會影響設定伺服器與 Azure 服務的註冊，以及設定伺服器的效能。

## <a name="add-an-additional-adapter"></a>新增其他介面卡

> [!NOTE]
> 如果您打算保留來源機器的 IP 位址進行容錯移轉，而且想要在稍後容錯回復至內部部署，則需要兩個 Nic。 一個 NIC 會連線到來源機器，而另一個 NIC 則會用於 Azure 連接。

如果您想要將其他 NIC 新增至設定伺服器，請在保存庫中註冊伺服器前新增。 註冊之後，便不支援新增其他介面卡。

1. 在 vSphere 用戶端詳細目錄中，以滑鼠右鍵按一下 VM 並選取 [編輯設定]  。
2. 在 [硬體]  中，選取 [新增]   > [乙太網路介面卡]  。 然後選取 [下一步]  。
3. 選取介面卡類型和網路。
4. 若要在 VM 開啟時連線虛擬 NIC，請選取 [在電源開啟時連線]****。 然後選取 [**下一步**  >  **完成**  >  **]**。

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>向 Azure Site Recovery 服務註冊設定伺服器

1. 從 VMware vSphere 用戶端主控台開啟 VM。
2. VM 會開機進入 Windows Server 2016 安裝體驗。 接受授權合約，並輸入系統管理員密碼。
3. 在安裝完成之後，以系統管理員身分登入 VM。
4. 當您第一次登入時，Azure Site Recovery 設定工具會在幾秒鐘內啟動。
5. 輸入用來向 Site Recovery 註冊設定伺服器的名稱。 然後選取 [下一步]  。
6. 此工具會檢查 VM 是否可連線到 Azure。 建立連線之後，選取 [登入]  以登入您的 Azure 訂用帳戶。</br>
    a. 認證必須能夠存取您要在其中註冊組態伺服器的保存庫。</br>
    b. 確定選擇的使用者帳戶具有在 Azure 中建立應用程式的許可權。 若要啟用必要的許可權，請遵循 [Azure Active Directory 許可權需求](#azure-active-directory-permission-requirements)一節中的指導方針。
7. 此工具會執行一些設定工作，而後重新開機。
8. 再次登入機器。 Configuration server 管理嚮導會在幾秒鐘內自動啟動。

### <a name="configure-settings"></a>進行設定

1. 在設定伺服器管理精靈中，選取 [設定連線]  。 從下拉式方塊中，先選取內建進程伺服器用來在來源機器上進行行動服務探索及推入安裝的 NIC。 然後選取設定伺服器用來與 Azure 連線的 NIC。 選取 [儲存]。 您在設定後便無法變更此設定。 請勿變更設定伺服器的 IP 位址。 確定指派給設定伺服器的 IP 是靜態 IP 而不是 DHCP IP。
2. 在 [ **選取復原服務保存庫**] 上，使用 [向 Azure Site Recovery 服務註冊設定伺服器](#register-the-configuration-server-with-azure-site-recovery-services)的步驟6中所使用的認證登入 Microsoft Azure。
3. 登入後，請選取您的 Azure 訂用帳戶和相關的資源群組和保存庫。

    > [!NOTE]
    > 註冊之後，就不需要變更復原服務保存庫的彈性。
    > 變更復原服務保存庫需要從目前保存庫解除與設定伺服器的配置，且設定伺服器底下所有受保護虛擬機器的複寫都會停止。 若要深入瞭解，請參閱 [管理 VMWARE VM 嚴重損壞修復的設定伺服器](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)。

4. **安裝協力廠商軟體**：

    |案例   |要依循的步驟  |
    |---------|---------|
    |是否可以手動下載並安裝 MySQL？     |  是。 下載 MySQL 應用程式，將它放在 **C:\Temp\ASRSetup**資料夾中，然後手動安裝。 接受條款並選取 [ **下載並安裝**] 之後，入口網站就會顯示 *已安裝*。 您可以繼續進行下一個步驟。       |
    |是否可以避免線上下載 MySQL？     |   是。 請將您的 MySQL 安裝程式應用程式放在 **C:\Temp\ASRSetup** 資料夾中。 接受條款，選取 [ **下載並安裝**]，入口網站就會使用您新增的安裝程式來安裝應用程式。 安裝完成之後，請繼續進行下一個步驟。    |
    |我想要透過 Azure Site Recovery 下載並安裝 MySQL。    |  接受授權合約，然後選取 [ **下載並安裝**]。 安裝完成之後，請繼續進行下一個步驟。       |

5. 在 [ **驗證設備**設定] 上，必要條件會在您繼續之前進行驗證。
6. 在 [ **設定 vCenter Server/VSphere ESXi 伺服器**] 上，輸入您想要複寫的 vm 所在的 vCenter 伺服器或 vSphere 主機的 FQDN 或 IP 位址。 輸入伺服器所接聽的連接埠。 輸入要用於保存庫中 VMware 伺服器的易記名稱。
7. 輸入供設定伺服器用來連線至 VMware 伺服器的認證。 Site Recovery 會使用這些認證來自動探索可用於複寫的 VMware VM。 選取 [新增] > [繼續]。 在這裡輸入的認證會儲存在本機。
8. 在 [ **設定虛擬機器認證**] 上，輸入虛擬機器的使用者名稱和密碼，以在複寫期間自動安裝行動服務。 針對 **Windows** 機器，此帳戶需要您想要複寫之機器上的本機系統管理員許可權。 若為 **Linux**，請提供根帳號的詳細資料。
9. 選取 [完成設定]  以完成註冊。
10. 註冊完成之後，請開啟 Azure 入口網站，然後確認 [復原服務保存庫]   > [管理]   > [Site Recovery 基礎結構]   > [設定伺服器]  上已列出設定伺服器和 VMware 伺服器。

## <a name="upgrade-the-configuration-server"></a>升級設定伺服器

若要將設定伺服器升級為最新版本，請參閱 [管理設定伺服器以進行 VMWARE VM](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)嚴重損壞修復。 如需有關如何升級所有 Site Recovery 元件的指示，請參閱 [Site Recovery 中的服務更新](service-updates-how-to.md)。

## <a name="manage-the-configuration-server"></a>管理組態伺服器

若要避免進行中的複寫中斷，請確定設定伺服器的 IP 位址在設定伺服器註冊至保存庫之後不會變更。 若要深入瞭解一般設定伺服器管理工作，請參閱 [管理設定伺服器以進行 VMWARE VM](vmware-azure-manage-configuration-server.md)嚴重損壞修復。

## <a name="troubleshoot-deployment-issues"></a>為部署問題進行疑難排解

請參閱我們的 [疑難排解文章](vmware-azure-troubleshoot-configuration-server.md) ，以解決部署 & 連接問題。

## <a name="faqs"></a>常見問題集

* 透過 OVF 部署的設定伺服器上提供的授權期限有多長？ 如果我未重新啟用授權，會發生什麼事？

    OVA 範本所提供的授權是有效期限為180天的評估授權。 到期之前，您必須啟用授權。 否則，它可能會導致頻繁地關閉設定伺服器，並導致阻礙複寫活動。 如需詳細資訊，請參閱 [管理 VMWARE VM 嚴重損壞修復的設定伺服器](vmware-azure-manage-configuration-server.md#update-windows-license)。

* 我可以使用安裝設定伺服器的 VM 來進行不同的用途嗎？

    否。 使用 VM 作為設定伺服器的唯一用途。 確定您遵循 [必要條件](#prerequisites) 中所述的所有規格，以有效率地管理嚴重損壞修復。
* 是否可以將已在設定伺服器中註冊的保存庫與新建立的保存庫交換？

    否。 在保存庫向設定伺服器註冊之後，就無法變更。
* 是否可以使用相同的設定伺服器來保護實體和虛擬機器？

    是。 您可以使用相同的設定伺服器來複寫實體和虛擬機器。 不過，實體機器只能容錯回復到 VMware VM。
* 設定伺服器的用途為何，以及使用的位置為何？

    若要深入瞭解設定伺服器及其功能，請參閱 [VMware 至 Azure 複寫架構](vmware-azure-architecture.md)。
* 我可以在哪裡找到最新版本的設定伺服器？

    如需透過入口網站升級組態伺服器的步驟，請參閱[升級組態伺服器](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)。 如需有關如何升級所有 Site Recovery 元件的指示，請參閱 [Site Recovery 中的服務更新](./service-updates-how-to.md)。
* 哪裡可以下載設定伺服器的複雜密碼？

    若要下載複雜密碼，請參閱 [管理設定伺服器以進行 VMWARE VM](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)嚴重損壞修復。
* 我可以變更複雜密碼嗎？

    否。 請勿變更設定伺服器的複雜密碼。 複雜密碼的變更會中斷受保護機器的複寫，並導致嚴重的健全狀況狀態。
* 哪裡可以下載保存庫註冊金鑰？

    在 [復原**服務保存庫**] 中，選取 [**管理**  >  **Site Recovery 基礎結構**設定  >  **伺服器**]。 在 [伺服器]**** 中，選取 [下載註冊金鑰]**** 以下載保存庫認證檔案。
* 我可以複製現有的設定伺服器，並將它用於複寫協調流程嗎？

    否。 不支援使用複製的設定伺服器元件。 複製相應放大進程伺服器也是不受支援的案例。 複製 Site Recovery 元件會影響進行中的複寫。

* 我可以變更設定伺服器的 IP 嗎？

    否。 請勿變更設定伺服器的 IP 位址。 確定指派給設定伺服器的所有 Ip 都是靜態 Ip 而不是 DHCP Ip。
* 是否可以在 Azure 上設定設定伺服器？

    在內部部署環境中設定一部設定伺服器，並透過 v 中心直接進行連線，並將資料傳輸延遲降至最低。 您可以對設定伺服器建立排程的備份，以實現[容錯回復目的](vmware-azure-manage-configuration-server.md#failback-requirements)。

* 我可以變更設定伺服器或相應放大進程伺服器上的快取驅動程式嗎？

    否，設定完成之後，就無法變更快取驅動程式。

如需更多有關設定伺服器的常見問題，請參閱設定 [伺服器的常見問題](vmware-azure-common-questions.md#configuration-server)。

## <a name="next-steps"></a>後續步驟

為 [VMware VM](vmware-azure-tutorial.md) 設定以 Azure 作為目標的災害復原。