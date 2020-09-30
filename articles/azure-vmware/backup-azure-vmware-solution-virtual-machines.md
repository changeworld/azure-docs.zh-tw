---
title: 使用 Azure 備份伺服器備份 Azure VMware 解決方案 Vm
description: 使用 Azure 備份伺服器設定您的 Azure VMware 解決方案環境，以備份虛擬機器。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: a62bccb729cfa6aec89a3ce6de7283f5d9412428
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579714"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>使用 Azure 備份伺服器備份 Azure VMware 解決方案 Vm

在本文中，我們將逐步解說如何使用 Azure 備份伺服器，將 VMware 虛擬機器備份 (Vm) 在 Azure VMware 解決方案上執行的程式。 開始之前，請確定您已徹底完成「 [設定適用于 Azure VMware 的 Microsoft Azure 備份 Server」解決方案](set-up-backup-server-for-azure-vmware-solution.md)。

接著，我們會逐步解說所有必要的程式，如下所示：

> [!div class="checklist"] 
> * 設定安全通道，讓 Azure 備份伺服器可以透過 HTTPS 與 VMware 伺服器通訊。 
> * 將帳號憑證新增至 Azure 備份伺服器。 
> * 將 vCenter 新增至 Azure 備份伺服器。 
> * 設定包含您要備份之 VMware VM 的保護群組、指定備份設定和排程備份。

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>建立連至 vCenter server 的安全連線

根據預設，Azure 備份伺服器會透過 HTTPS 與 VMware 伺服器通訊。 若要設定 HTTPS 連線，請下載 VMware 憑證授權單位單位 (CA) 憑證，並將它匯入 Azure 備份伺服器。

### <a name="set-up-the-certificate"></a>設定憑證

1. 在瀏覽器的 Azure 備份伺服器電腦上，輸入 vSphere Web 用戶端 URL。

   > [!NOTE] 
   > 如果 VMware **消費者入門** 頁面未出現，請確認連接和瀏覽器 proxy 設定，然後再試一次。

1. 在 [VMware **消費者入門** ] 頁面上，選取 [ **下載受信任的根 CA 憑證**]。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere Web 用戶端":::

1. 將 **download.zip** 檔案儲存至 Azure 備份伺服器機，然後將其內容解壓縮至 [憑證 **] 資料夾，** 其中包含：

   - 根憑證檔案，其副檔名的開頭是編號序列，例如0和. 1。
   - 副檔名開頭為 r0 或 r1 的 CRL 檔案。

1. **在 [憑證] 資料夾中**，以滑鼠右鍵按一下根憑證檔案，然後選取 [**重新命名**]，將副檔名變更為 **.crt。**

   檔案圖示會變更為代表根憑證的圖示。

1. 以滑鼠右鍵按一下根憑證，然後選取 [ **安裝憑證**]。

1. 在 [ **憑證匯入] 嚮導**中，選取 [ **本機電腦** ] 作為憑證的目的地，然後選取 **[下一步]**。

   ![Wizard 歡迎頁面](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > 如果系統要求您，請確認您想要允許對電腦進行變更。

1. 選取 **[將所有憑證放入以下的存放區**]，然後選取 [ **流覽]** 以選擇憑證存放區。

   ![憑證存放區](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. 選取 [ **信任的根憑證授權** 單位] 作為目的地資料夾，然後選取 **[確定]**。

1. 檢查設定，然後選取 **[完成]** 以開始匯入憑證。

   ![確認憑證位於適當的資料夾](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. 確認憑證匯入之後，請登入 vCenter server 以確認您的連線是安全的。

### <a name="enable-tls-12-on-azure-backup-server"></a>在 Azure 備份伺服器上啟用 TLS 1。2

VMware 6.7 已將 TLS 啟用為通訊協定。 

1. 複製下列登錄設定，並將它們貼到 [記事本] 中。 然後將檔案儲存為 TLS。REG （沒有副檔名 .txt）。

   ```text
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. 以滑鼠右鍵按一下 TLS。REG 檔案，然後選取 [ **合併** ] 或 [ **開啟** ]，將設定新增至登錄。

## <a name="add-the-provisioning-ip-address"></a>新增布建 IP 位址 

在預覽期間，Azure VMware 解決方案不會從虛擬網路中部署的虛擬機器解析 ESX 主機。 您必須執行額外的步驟，以在 Azure 備份伺服器虛擬機器上新增主機檔案專案。

### <a name="identify-the-ip-address-for-esxi-hosts"></a>識別 ESXi 主機的 IP 位址

1. 開啟瀏覽器，然後登入 vCenter Url。 

   > [!TIP]
   > 您可以在 [連線 [至私人雲端的本機 VCenter]](tutorial-access-private-cloud.md#connect-to-the-local-vcenter-of-your-private-cloud)中找到 url。

1. 在 vSphere 用戶端中，選取您打算啟用備份的叢集。

   :::image type="content" source="media/azure-vmware-solution-backup/vsphere-client-select-host.png" alt-text="vSphere Web 用戶端":::

1. 選取 [**設定**  >  **網路**  >  **VMKernel 介面卡**]。 在裝置清單下，找出已啟用布 **建角色的** 網路介面卡。 記下 **IP 位址** 和 ESXi 主機名稱。

   :::image type="content" source="media/azure-vmware-solution-backup/vmkernel-adapters-provisioning-enabled.png" alt-text="vSphere Web 用戶端":::

1. 針對您打算啟用備份的每個叢集下的每個 ESXi 主機，重複上述步驟。

### <a name="update-the-host-file-on-azure-backup-server"></a>更新 Azure 備份伺服器上的主機檔案

1. 以系統管理員身分開啟 [記事本]。

1. 選取 **[**  >  **開啟**檔案]，然後搜尋 c:\Windows\System32\Drivers\etc\hosts。

1. 新增每個 ESXi 主機的專案，以及您在上一節中所識別的 IP 位址。

1. 儲存您的變更，並關閉 [記事本]。

## <a name="add-the-account-on-azure-backup-server"></a>在 Azure 備份伺服器上新增帳戶

1. 開啟 Azure 備份伺服器，然後在 Azure 備份伺服器主控台中，選取 [**管理**  >  **生產伺服器**  >  **管理 VMware**]。

   ![Azure 備份伺服器主控台](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. 在 [ **管理認證** ] 對話方塊中，選取 [ **新增**]。

   ![在 [管理認證] 對話方塊中，選取 [新增]。](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. 在 [新增認證]**** 對話方塊中，輸入新認證的名稱和描述。 指定您在 VMware 伺服器上定義的使用者名稱和密碼。

   > [!NOTE] 
   > 如果 VMware 伺服器和 Azure 備份伺服器不在相同網域中，請在 [ **使用者名稱** ] 方塊中指定網域。

   ![Azure 備份伺服器的 [新增認證] 對話方塊](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. 選取 **[新增]** 以新增認證。

   ![螢幕擷取畫面顯示 [Azure 備份伺服器管理認證] 對話方塊，其中顯示新的認證。](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>將 vCenter server 新增至 Azure 備份伺服器

1. 在 Azure 備份伺服器主控台中，選取 [**管理**  >  **生產伺服器**  >  **新增**]。

   ![開啟「生產伺服器新增精靈」](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. 選取 [ **VMware 伺服器**]，然後選取 **[下一步]**。

   ![生產伺服器新增精靈](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. 指定 vCenter 的 IP 位址。

   ![指定 VMware 伺服器](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. 在 [ **SSL 埠** ] 方塊中，輸入用來與 vCenter 通訊的埠。

   > [!TIP] 
   > 埠443是預設埠，但如果您的 vCenter 接聽不同的埠，您可以變更它。

1. 在 [ **指定認證** ] 方塊中，選取您在上一節中建立的認證。

1. 選取 [ **新增** ] 將 vCenter 新增至 [伺服器] 清單，然後選取 **[下一步]**。

   ![新增 VMware 伺服器和認證](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. 在 [ **摘要** ] 頁面上，選取 [ **新增** ]，將 vCenter 新增至 Azure 備份伺服器。

   新的伺服器會立即新增。 vCenter 不需要代理程式。

   ![將 VMware 伺服器新增至 Azure 備份伺服器](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. 在 [ **完成]** 頁面上，檢查設定，然後選取 [ **關閉**]。

   ![[完成] 頁面](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   您應該會看到 [ **實際執行伺服器** ] 底下列出的 vCenter 伺服器，其類型為 [ **VMware 伺服器** ] 和 [ **代理程式狀態** **]**。 如果您看到 **代理程式狀態** 為 [ **未知**]， **請選取 [** 重新整理]。

## <a name="configure-a-protection-group"></a>設定保護群組

保護群組會收集多個 VM，並將相同的資料保留和備份設定套用至群組中的所有 VM。

1. 在 Azure 備份伺服器主控台中，選取 [**保護**  >  **新**的]。

   ![開啟「建立新保護群組」精靈](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. 在 [ **建立新保護組** 嚮導歡迎使用] 頁面上，選取 **[下一步]**。

   ![[建立新保護群組] 精靈對話方塊](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. 在 [選取保護群組類型]**** 頁面上，選取 [伺服器]****，然後選取 [下一步]****。 [ **選取群組成員** ] 頁面隨即出現。

1. 在 [ **選擇群組成員** ] 頁面上，選取您要備份的 vm (或 vm 資料夾) ，然後選取 **[下一步]**。

   > [!NOTE]
   > 當您選取資料夾或 Vm 時，也會選取該資料夾內的資料夾進行備份。 您可以將不想備份的資料夾或 VM 取消選取。 如果 VM 或資料夾已備份，您就無法選取它，以確保不會為 VM 建立重複的復原點。

   ![選擇群組成員](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. 在 [ **選擇資料保護方式** ] 頁面上，輸入保護群組和保護設定的名稱。 

1. 將短期保護設定為 [ **磁片**]，啟用 [線上保護]，然後選取 **[下一步]**。

   ![選擇資料保護方式](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. 指定要將資料備份到磁片的時間長度。

   - **保留範圍**：保留磁片復原點的天數。
   - **快速完整備份**：取得磁片復原點的頻率。 若要變更短期備份發生的時間或日期，請選取 [ **修改**]。

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="vSphere Web 用戶端":::

1. 在 [ **審核磁碟儲存體配置** ] 頁面上，檢查為 VM 備份提供的磁碟空間。

   - 建議的磁碟配置是根據您指定的保留範圍、工作負載的類型和所保護資料的大小。 進行任何必要的變更，然後選取 **[下一步]**。
   - **資料大小：** 保護群組中的資料大小。
   - **磁碟空間：** 建議的保護群組磁碟空間量。 如果您想要修改此設定，請配置比您預估每個資料來源成長的數量稍微大一點的總空間。
   - **存放集區詳細資料：** 顯示存放集區的狀態，包括總計和剩餘的磁片大小。

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="vSphere Web 用戶端":::

   > [!NOTE]
   > 在某些情況下，報告的資料大小高於實際的 VM 大小。 我們注意到此問題，目前正在進行調查。

1. 在 [ **選擇複本的建立方式** ] 頁面上，指出您要如何進行初始備份，然後選取 **[下一步]**。

   - 預設值是 [自動透過網路]**** 和 [立即]****。 如果您使用預設值，請指定離峰時間。 如果您選擇 [ **稍後**]，請指定日期和時間。
   - 對於大量資料或較差的網路狀況，請考慮使用卸除式媒體來離線複寫資料。

   ![選擇複本的建立方式](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. 如需 **一致性檢查選項**，請選取自動執行一致性檢查的方式和時機，然後選取 **[下一步]**。

   - 當複本資料變得不一致時，或依據設定的排程，您可以執行一致性檢查。
   - 如果您不想設定自動一致性檢查，您可以用滑鼠右鍵按一下保護群組 **執行一致性檢查**，以執行手動檢查。

1. 在 [ **指定線上保護資料** ] 頁面上，選取您要備份的 VM 或 vm 資料夾，然後選取 **[下一步]**。 

   > [!TIP]
   > 您可以個別選取成員，或選擇 [全 **選** ] 來選擇所有成員。

   ![指定線上保護資料](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. 在 [ **指定線上備份排程** ] 頁面上，指出您要將資料從本機儲存體備份到 Azure 的頻率，然後選取 **[下一步]**。 

   - 要根據排程產生之資料的雲端復原點。 
   - 產生復原點之後，它就會傳輸至 Azure 中的復原服務保存庫。

   ![指定線上備份排程](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. 在 [ **指定線上保留原則** ] 頁面上，指出您要保留從每日、每週、每月或每年備份建立的復原點到 Azure 的時間長度，然後選取 **[下一步]**。

   - 您可以在 Azure 中保留資料的時間長度沒有限制。
   - 唯一的限制是每個受保護的實例不能有超過9999個復原點。 在此範例中，受保護的執行個體是 VMware 伺服器。

   ![指定線上保留期原則](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. 在 [ **摘要** ] 頁面上，檢查設定，然後選取 [ **建立群組**]。

   ![保護群組成員和設定的摘要](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>使用 Azure 備份伺服器主控台監視

設定保護群組以備份 Azure VMware 解決方案 Vm 之後，您可以使用 Azure 備份伺服器主控台來監視備份作業和警示的狀態。 以下是您可以監視的內容。

- 在 [**監視**中] 窗格的 [**警示**] 索引標籤上，您可以監視保護群組的錯誤、警告和一般資訊、特定受保護的電腦，或依訊息的嚴重性。 您可以查看作用中和非作用中警示，並設定電子郵件通知。
- 在 [**監視**中] 窗格的 [**工作**] 索引標籤上，您可以針對特定受保護的資料來源或保護群組，查看 Azure 備份伺服器起始的作業。 您可以追蹤工作進度，或檢查工作所耗用的資源。
- 在 [ **保護** ] 工作區中，您可以檢查保護群組中磁片區和共用的狀態。 您也可以檢查設定設定，例如復原設定、磁片配置和備份排程。
- 在 [ **管理** ] 工作區中，您可以查看 [ **磁片]、[線上**] 和 [ **代理** 程式] 索引標籤，以檢查存放集區中的磁片狀態、向 AZURE 註冊，以及部署的 DPM 代理程式狀態。

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="vSphere Web 用戶端":::

## <a name="restore-vmware-virtual-machines"></a>還原 VMware 虛擬機器

在 Azure 備份伺服器系統管理員主控台中，有兩種方式可以尋找可復原的資料。 您可以搜尋或流覽。 當您復原資料時，您可能會想要將資料或 VM 還原至相同的位置。 基於這個理由，Azure 備份伺服器支援適用于 VMware VM 備份的三個復原選項：

- **原始位置復原 (OLR) **：使用 OLR 將受保護的 VM 還原至其原始位置。 您只能將 VM 還原至其原始位置，因為備份之後未新增或刪除任何磁片。 如果已新增或刪除磁片，您必須使用替代位置復原。
- **替代位置復原 (ALR) **：缺少原始 vm，或您不想要擾亂原始 vm 時，請將 vm 復原到替代位置。 若要將 VM 復原到替代位置，您必須提供 ESXi 主機的位置、資源集區、資料夾，以及儲存體資料存放區和路徑。 為了協助區分還原的 VM 與原始 VM，Azure 備份伺服器會將「-已復原」附加至 VM 的名稱。
- **個別檔案位置復原 (ILR) **：如果受保護的 Vm 是 WINDOWS Server VM，則可以使用 AZURE 備份伺服器的 ILR 功能來復原 vm 內的個別檔案或資料夾。 若要復原個別檔案，請參閱本文稍後的程序。 從 VM 還原個別檔案僅適用于 Windows VM 和磁片復原點。

### <a name="restore-a-recovery-point"></a>還原復原點

1. 在 [Azure 備份伺服器系統管理員主控台中，選取 [ **修復** ] 視圖。 

1. 使用 [瀏覽] 窗格，瀏覽或篩選以尋找您想要復原的 VM。 選取 VM 或資料夾之後，窗格的 [ **復原點** ] 會顯示可用的復原點。

   ![可用的復原點](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. 在 [ **復原點** ] 窗格中，使用行事曆和下拉式功能表來選取取得復原點的日期。 粗體的行事曆日期具備可用的復原點。 或者，您可以在 VM 上按一下滑鼠右鍵，然後選取 [ **顯示所有復原點** ]，然後從清單中選取復原點。

   > [!NOTE] 
   > 針對短期保護，請選取以磁片為基礎的復原點，以加快復原速度。 短期復原點到期後，您只會看到要復原的 **線上** 復原點。

1. 從線上復原點復原之前，請確定預備位置包含足夠的可用空間，以容納您要復原之 VM 的完整未壓縮大小。 您可以執行 [ **設定訂閱設定] 嚮導**來查看或變更預備位置。

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="vSphere Web 用戶端":::

1. 選取 [復原] 以開啟 [**復原****嚮導]**。

   ![復原嚮導，請參閱復原選取頁面](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. 選取 **[下一步]** 移至 [ **指定修復選項** ] 畫面。 再次選取 **[下一步]** 以移至 [ **選取復原類型** ] 畫面。 

   > [!NOTE]
   > VMware 工作負載不支援啟用網路頻寬節流。

1. 在 [ **選擇復原類型** ] 頁面上，選擇要復原到原始實例或新的位置，然後選取 **[下一步]**。

   - 如果您選擇 [復原到原始執行個體]，就不需在精靈中進行任何更多選擇。 系統會使用原始執行個體的資料。
   - 如果您選擇 [在任何主機上依虛擬機器復原]，則可在 [指定目的地] 畫面上，提供 **ESXi 主機**、**資源集區**、**資料夾**和**路徑**的資訊。

   ![選擇復原類型頁面](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. 在 [ **摘要** ] 頁面上，檢查您的設定，然後選取 [復原] 以開始 **復原** 程式。 

   [復原狀態] 畫面會顯示復原作業的進度。

### <a name="restore-an-individual-file-from-a-vm"></a>從 VM 還原個別檔案

您可以從受保護的 VM 復原點還原個別檔案。 這項功能僅適用於 Windows Server VM。 還原個別檔案與還原整個 VM 類似，不同之處在于您可以在啟動復原程式之前，先流覽至 VMDK 並尋找所需的檔案。 

> [!NOTE]
> 從 VM 還原個別檔案僅適用于 Windows VM 和磁片復原點。

1. 在 [Azure 備份伺服器系統管理員主控台中，選取 [ **修復** ] 視圖。

1. 使用 [瀏覽] 窗格，瀏覽或篩選以尋找您想要復原的 VM。 選取 VM 或資料夾之後，窗格的 [ **復原點** ] 會顯示可用的復原點。

   ![可用的復原點](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. 在 [ **復原點** ] 窗格中，使用行事曆選取包含所需復原點的日期。 根據備份原則的設定方式而定，日期可以有一個以上的復原點。 

1. 在您選取復原點的拍攝日之後，請務必選擇正確的復原 **時間**。 

   > [!NOTE]
   > 如果選取的日期有多個復原點，在 [復原時間] 下拉式功能表中選取您的復原點。 

   選擇復原點之後，可復原的專案清單會出現在 [ **路徑** ] 窗格中。

1. 若要尋找您想要復原的檔案，請在 [ **路徑** ] 窗格中，按兩下 [可復原的 **專案** ] 欄中的專案，將它開啟。 然後選取您想要復原的檔案或資料夾。 若要選取多個專案，請在選取每個專案時，選取 **Ctrl** 鍵。 使用 [ **路徑** ] 窗格來搜尋 [可復原的 **專案** ] 欄位中出現的檔案或資料夾清單。
    
   > [!NOTE]
   > **下列搜尋清單** 不會搜尋子資料夾。 若要搜尋所有子資料夾，請按兩下該資料夾。 使用 [向上] 按鈕，從子資料夾移至上層資料夾。 您可以選取多個項目 (檔案和資料夾)，但它們必須位於同一個上層資料夾。 您無法從相同復原工作中的多個資料夾復原專案。

   ![檢閱復原選項](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. 當您選取要復原的專案時，請在 [系統管理員主控台工具] 功能區中選取 [復原]，以開啟 [**復原****嚮導]**。 在 [復原 **] 嚮導**中，[ **審核修復選項** ] 畫面會顯示所選取要復原的專案。

1. 在 [ **指定修復選項** ] 畫面上，執行下列其中一項：

   - 選取 [ **修改** ] 以啟用網路頻寬節流設定。 在 [ **節流** ] 對話方塊中，選取 [ **啟用網路頻寬使用節流** 設定] 以開啟它。 啟用之後，進行**設定**和**工作排程**的設定。
   - 選取 **[下一步]** 讓網路節流保持停用。

1. 在 [ **選取復原類型** ] 畫面上，選取 **[下一步]**。 您只能將檔案或資料夾復原到網路資料夾。

1. 在 [ **指定目的地** ] 畫面上，選取 **[流覽]** 以尋找檔案或資料夾的網路位置。 Azure 備份伺服器會建立所有已復原專案都會複製到其中的資料夾。 資料夾名稱的前置詞 MABS_day-month-year。 當您選取已復原檔案或資料夾的位置時，會提供該位置的詳細資料，例如 **目的地**、 **目的地路徑**和 **可用空間**。

   ![指定復原檔案的位置](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. 在 [指定復原選項] 畫面上，選擇要套用的安全性設定。 您可以選擇修改網路頻寬使用節流設定，但預設會停用節流設定。 此外，也不會啟用 **SAN** 復原和 **通知** 。

1. 在 [ **摘要** ] 畫面上，檢查您的設定，然後選取 [復原] 以開始 **復原** 程式。 [復原狀態] 畫面會顯示復原作業的進度。

## <a name="next-steps"></a>後續步驟

如需針對設定備份時的問題進行疑難排解，請檢閱 Azure 備份伺服器的疑難排解指南。

> [!div class="nextstepaction"]
> [Azure 備份伺服器的疑難排解指南](../backup/backup-azure-mabs-troubleshoot.md)
