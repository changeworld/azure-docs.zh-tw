---
title: 在 Azure 網站恢復中部署佈建服務器
description: 本文說明如何使用 Azure Site Recovery 部署 VMware 災害復原的組態伺服器
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: 5209dab5e0934cc98bb1334a1565cc13998a7d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257377"
---
# <a name="deploy-a-configuration-server"></a>部署設定伺服器

當您使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 和實體伺服器災害復原到 Azure 時，便會部署內部部署設定伺服器。 設定伺服器會協調內部部署 VMware 與 Azure 之間的通訊。 它也會管理資料複寫。 本文會逐步引導您完成當您將 VMware VM 複寫至 Azure 時部署設定伺服器所需的步驟。 如果需要為物理伺服器複製設置佈建服務器，請參閱[將佈建服務器設置為物理伺服器的災害復原到 Azure](physical-azure-set-up-source.md)。

> [!TIP]
> 要瞭解佈建服務器作為 Azure 網站恢復體系結構的一部分的角色，請參閱[VMware 到 Azure 災害復原體系結構](vmware-azure-architecture.md)。

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>通過 OVA 範本部署佈建服務器

佈建服務器必須設置為高度可用的 VMware VM，具有某些最低硬體和大小要求。 為了方便、輕鬆地部署，網站恢復提供了可下載的開放式虛擬化應用程式 （OVA） 範本，用於設置符合此處列出的所有授權要求的佈建服務器。

## <a name="prerequisites"></a>Prerequisites

佈建服務器的最低硬體要求概述如下部分。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure 活動目錄許可權要求

您必須讓具有以下許可權之一的使用者在 Azure 活動目錄 （Azure AD） 中註冊佈建服務器到 Azure 網站恢復服務。

1. 使用者必須具有應用程式開發人員角色才能創建應用程式。
    - 要驗證，請登錄到 Azure 門戶。</br>
    - 轉到**Azure 活動目錄** > **角色和管理員**。</br>
    - 驗證應用程式開發人員角色是否分配給使用者。 否則，請使用具有此許可權的使用者或與[管理員聯繫以啟用 許可權](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles)。
    
2. 如果無法分配應用程式開發人員角色，請確保**使用者可以註冊應用程式**標誌設置為**true，** 以便使用者創建標識。 要啟用這些許可權，我們：
    - 登入 Azure 入口網站。
    - 轉到**Azure 活動目錄** > **使用者設置**。
    - 在**應用註冊**下，**使用者可以註冊應用程式**，選擇 **"是**"。

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> 不支援活動目錄聯合*服務。* 使用通過 Azure[活動目錄](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)管理的帳戶。

## <a name="download-the-template"></a>下載範本

1. 在保存庫中，轉到 **"準備基礎結構** > **源**"。
2. 在 [準備來源]**** 中，選取 [+設定伺服器]****。
3. 在 [新增伺服器]**** 中，檢查 [VMware 的組態伺服器]**** 是否出現在 [伺服器類型]**** 中。
4. 下載佈建服務器的 OVA 範本。

   > [!TIP]
   >您也可以直接從[微軟下載中心](https://aka.ms/asrconfigurationserver)下載最新版本的佈建服務器範本。

> [!NOTE]
> OVA 範本提供的許可證是有效期為 180 天的評估許可證。 在此期限之後，您必須獲得許可證。

## <a name="import-the-template-in-vmware"></a>在 VMware 中匯入範本

1. 使用 VMWare vSphere 用戶端，登入 VMware vCenter 伺服器或 vSphere ESXi 主機。
2. 在 **"檔**"功能表上，選擇 **"部署 OVF 範本**"以啟動 **"部署 OVF 範本"** 嚮導。

     ![部署 OVF 範本](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. 在 [選取來源]**** 上，輸入所下載 OVF 的位置。
4. 在 [檢閱詳細資料]**** 上，選取 [下一步]****。
5. 在 [選取名稱和資料夾]**** 和 [選取組態]**** 上，接受預設設定。
6. 在 [選取儲存體]**** 上，於 [選取虛擬磁碟格式]**** 中選取 [Thick Provision Eager Zeroed]****，以達到最佳效能。 使用精簡預配選項可能會影響佈建服務器的性能。
7. 在精靈的其餘頁面上，接受所有的預設設定。
8. 在 [準備要完成]**** 上：

    * 要使用預設設置設置 VM，請選擇 > **Finish****部署完成後打開電源**。
    * 若要新增額外的網路介面，請清除 [在部署後開啟電源]****，然後選取 [完成]****。 預設會使用單一 NIC 部署設定伺服器範本。 您可以在部署後新增其他 NIC。

> [!IMPORTANT]
> 不要更改資源配置，如記憶體、內核和 CPU 限制，或在部署後修改或刪除佈建服務器上已安裝的服務或檔。 這些類型的更改會影響佈建服務器與 Azure 服務的註冊以及佈建服務器的性能。

## <a name="add-an-additional-adapter"></a>新增其他介面卡

> [!NOTE]
> 如果計畫在容錯移轉時保留源電腦的 IP 位址，並希望稍後故障回本地，則需要兩個 NIC。 一個 NIC 連接到源電腦，另一個 NIC 用於 Azure 連接。

如果您想要將其他 NIC 新增至設定伺服器，請在保存庫中註冊伺服器前新增。 註冊之後，便不支援新增其他介面卡。

1. 在 vSphere 用戶端詳細目錄中，以滑鼠右鍵按一下 VM 並選取 [編輯設定]****。
2. 在 [硬體]**** 中，選取 [新增]**** > [乙太網路介面卡]****。 然後選擇 **"下一步**"。
3. 選取介面卡類型和網路。
4. 若要在 VM 開啟時連線虛擬 NIC，請選取 [在電源開啟時連線]****。 然後選擇 **"下一步** > **完成** > **確定**"。

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>向 Azure Site Recovery 服務註冊設定伺服器

1. 從 VMWare vSphere 用戶端主控台開啟 VM。
2. VM 會開機進入 Windows Server 2016 安裝體驗。 接受授權合約，並輸入系統管理員密碼。
3. 在安裝完成之後，以系統管理員身分登入 VM。
4. 首次登錄時，Azure 網站恢復組態工具將在幾秒鐘內啟動。
5. 輸入用來向 Site Recovery 註冊設定伺服器的名稱。 然後選擇 **"下一步**"。
6. 此工具會檢查 VM 是否可連線到 Azure。 建立連線之後，選取 [登入]**** 以登入您的 Azure 訂用帳戶。</br>
    a. 認證必須能夠存取您要在其中註冊組態伺服器的保存庫。</br>
    b. 確保所選使用者帳戶具有在 Azure 中創建應用程式的許可權。 要啟用所需的許可權，請按照[Azure 活動目錄許可權要求](#azure-active-directory-permission-requirements)部分中的指南操作。
7. 此工具會執行一些設定工作，而後重新開機。
8. 再次登入機器。 佈建服務器管理嚮導在幾秒鐘內自動啟動。

### <a name="configure-settings"></a>進行設定

1. 在設定伺服器管理精靈中，選取 [設定連線]****。 首先從下拉清單中選擇內置進程伺服器用於發現和推送在源電腦上安裝移動服務的 NIC。 然後選擇佈建服務器用於與 Azure 連接的 NIC。 選取 [儲存]****。 您在設定後便無法變更此設定。 不要更改佈建服務器的 IP 位址。 確保分配給佈建服務器的 IP 是靜態 IP 而不是 DHCP IP。
2. 在**選擇恢復服務保存庫**上，使用使用[Azure 網站恢復服務註冊佈建服務器](#register-the-configuration-server-with-azure-site-recovery-services)的步驟 6 中使用的憑據登錄到 Microsoft Azure。
3. 登錄後，選擇 Azure 訂閱以及相關資源組和保存庫。

    > [!NOTE]
    > 註冊後，無法靈活地更改恢復服務保存庫。
    > 更改恢復服務保存庫需要將佈建服務器與當前保存庫分離，並且將停止在佈建服務器下複製所有受保護的虛擬機器。 要瞭解更多資訊，請參閱[管理 VMware VM 災害復原的佈建服務器](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)。

4. 關於**安裝協力廠商軟體**：

    |狀況   |要依循的步驟  |
    |---------|---------|
    |我可以手動下載和安裝 MySQL 嗎？     |  是。 下載 MySQL 應用程式，將其放在資料夾**C：\Temp_ASRSetup**中，然後手動安裝。 接受條款並選擇 **"下載並安裝**"後，門戶顯示*已安裝*。 您可以繼續進行下一個步驟。       |
    |是否可以避免線上下載 MySQL？     |   是。 請將您的 MySQL 安裝程式應用程式放在 **C:\Temp\ASRSetup** 資料夾中。 接受這些條款，選擇 **"下載並安裝**"，門戶使用您添加的安裝程式來安裝應用程式。 安裝完成後，繼續執行下一步。    |
    |我想通過 Azure 網站恢復下載並安裝 MySQL。    |  接受授權合約，然後選擇 **"下載並安裝**"。 安裝完成後，繼續執行下一步。       |

5. 在**驗證設備配置**時，在繼續之前將驗證先決條件。
6. 在**配置 vCenter 伺服器/vSphere ESXi 伺服器上**，輸入要複製的 VM 所在的 vCenter 伺服器或 vSphere 主機的 FQDN 或 IP 位址。 輸入伺服器所接聽的連接埠。 輸入要用於保存庫中 VMware 伺服器的易記名稱。
7. 輸入供設定伺服器用來連線至 VMware 伺服器的認證。 Site Recovery 會使用這些認證來自動探索可用於複寫的 VMware VM。 選擇 **"繼續添加** > **Continue**"。 在這裡輸入的認證會儲存在本機。
8. 在 **"配置虛擬機器憑據"** 上，輸入虛擬機器的使用者名和密碼，以在複製期間自動安裝移動服務。 針對 **Windows** 電腦，此帳戶必須具備您要複寫之電腦的本機系統管理員權限。 針對 **Linux**，請提供根帳戶的詳細資料。
9. 選取 [完成設定]**** 以完成註冊。
10. 註冊完成後，打開 Azure 門戶並驗證佈建服務器和 VMware 伺服器是否列在**恢復服務保存庫** > **管理** > **網站恢復基礎結構** > **佈建服務器**。

## <a name="upgrade-the-configuration-server"></a>升級設定伺服器

要將佈建服務器升級到最新版本，請參閱[管理 VMware VM 災害復原的佈建服務器](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)。 有關如何升級所有網站恢復元件的說明，請參閱[網站恢復 中的服務更新](service-updates-how-to.md)。

## <a name="manage-the-configuration-server"></a>管理組態伺服器

為避免正在進行的複製中斷，請確保佈建服務器的 IP 位址在將佈建服務器註冊到保存庫後不會更改。 要瞭解有關常見佈建服務器管理工作的資訊，請參閱[管理 VMware VM 災害復原的佈建服務器](vmware-azure-manage-configuration-server.md)。

## <a name="troubleshoot-deployment-issues"></a>為部署問題進行疑難排解

請參閱我們的[故障排除文章](vmware-azure-troubleshoot-configuration-server.md)，以解決部署&連接問題。

## <a name="faqs"></a>常見問題集

* 通過 OVF 部署的佈建服務器上提供的許可證的有效期是多少？ 如果我不重新啟動許可證，會發生什麼情況？

    OVA 範本提供的許可證是有效期為 180 天的評估許可證。 過期之前，您需要啟動許可證。 否則，它可能導致佈建服務器頻繁關閉，並造成複製活動的障礙。 有關詳細資訊，請參閱管理[VMware VM 災害復原的佈建服務器](vmware-azure-manage-configuration-server.md#update-windows-license)。

* 我可以使用佈建服務器安裝用於不同用途的 VM 嗎？

    否。 將 VM 用於佈建服務器的唯一用途。 確保您遵循[先決條件](#prerequisites)中提及的所有規範，以便高效管理災害復原。
* 是否可以將已在設定伺服器中註冊的保存庫與新建立的保存庫交換？

    否。 在佈建服務器註冊保存庫後，無法更改它。
* 我是否可以使用相同的佈建服務器來保護物理和虛擬機器？

    是。 同一佈建服務器可用於複製物理和虛擬機器。 但是，物理電腦只能故障回 VMware VM。
* 佈建服務器的目的是什麼，在哪裡使用？

    要瞭解有關佈建服務器及其功能的更多資訊，請參閱[VMware 到 Azure 複製體系結構](vmware-azure-architecture.md)。
* 在哪裡可以找到最新版本的佈建服務器？

    如需透過入口網站升級組態伺服器的步驟，請參閱[升級組態伺服器](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)。 有關如何升級所有網站恢復元件的說明，請參閱[網站恢復 中的服務更新](https://aka.ms/asr_how_to_upgrade)。
* 哪裡可以下載設定伺服器的複雜密碼？

    要下載密碼，請參閱管理[VMware VM 災害復原的佈建服務器](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)。
* 我可以變更複雜密碼嗎？

    否。 不要更改佈建服務器的密碼。 密碼短語的更改會破壞受保護電腦的複製，並導致嚴重的運行狀況狀態。
* 哪裡可以下載保存庫註冊金鑰？

    在**恢復服務保存庫中**，選擇 **"管理** > **網站恢復基礎結構** > **佈建服務器**"。 在 [伺服器]**** 中，選取 [下載註冊金鑰]**** 以下載保存庫認證檔案。
* 是否可以克隆現有佈建服務器並將其用於複製業務流程？

    否。 不支援使用克隆的佈建服務器元件。 克隆橫向擴展進程伺服器也是不受支援的方案。 克隆網站恢復元件會影響正在進行的複製。

* 我可以更改佈建服務器的 IP 嗎？

    否。 不要更改佈建服務器的 IP 位址。 確保分配給佈建服務器的所有 IP 都是靜態 IP，而不是 DHCP IP。
* 是否可以在 Azure 上設置佈建服務器？

    在具有 V-Center 的直接視線的本地環境中設置佈建服務器，並最大限度地減少資料傳輸延遲。 您可以對設定伺服器建立排程的備份，以實現[容錯回復目的](vmware-azure-manage-configuration-server.md#failback-requirements)。

* 是否可以更改佈建服務器或橫向擴展進程伺服器上的緩存驅動程式？

    否，設置完成後無法更改緩存驅動程式。

有關佈建服務器上的更多常見問題，請參閱[佈建服務器常見問題](vmware-azure-common-questions.md#configuration-server)。

## <a name="next-steps"></a>後續步驟

為 [VMware VM](vmware-azure-tutorial.md) 設定以 Azure 作為目標的災害復原。
