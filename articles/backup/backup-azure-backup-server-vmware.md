---
title: 使用 Azure 備份伺服器來備份 VMware VM
description: 在本文中，您將瞭解如何使用 Azure 備份伺服器來備份在 VMware vCenter/ESXi 伺服器上執行的 VMware Vm。
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: f498a7b7d2faf9ff857b504043233c46c843a961
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826934"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>使用 Azure 備份伺服器來備份 VMware VM

本文說明如何使用 Azure 備份伺服器，將在 VMware ESXi 主機/vCenter 伺服器上執行的 VMware VM 備份到 Azure。

本文說明如何：

- 設定安全通道，讓 Azure 備份伺服器可以透過 HTTPS 與 VMware 伺服器通訊。
- 設定 VMware 帳戶，使 Azure 備份伺服器能用來存取 VMware 伺服器。
- 將帳戶認證新增至 Azure 備份。
- 將 vCenter 或 ESXi 伺服器新增至 Azure 備份伺服器。
- 設定包含您要備份之 VMware VM 的保護群組、指定備份設定和排程備份。

## <a name="before-you-start"></a>開始之前

- 確認您正在執行支援備份的 vCenter/ESXi 版本。 請參閱 [這裡](./backup-mabs-protection-matrix.md)的支援矩陣。
- 確定您已設定 Azure 備份伺服器。 如果還沒，請在開始之前[進行設定](backup-azure-microsoft-azure-backup.md)。 您應該執行具有最新更新的 Azure 備份伺服器。
- 確認下列網路連接埠已開放：
  - MABS 與 vCenter 之間的 TCP 443
  - MABS 與 ESXi 主機之間的 TCP 443 與 TCP 902

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>建立 vCenter Server 的安全連線

根據預設，Azure 備份伺服器會透過 HTTPS 與 VMware 伺服器通訊。 若要設定 HTTPS 連線，請下載 VMware 憑證授權單位 (CA) 憑證，並將它匯入至 Azure 備份伺服器。

### <a name="before-you-begin"></a>開始之前

- 如果您不想要使用 HTTPS，則可以 [停用所有 VMware 伺服器的 HTTPs 憑證驗證](backup-azure-backup-server-vmware.md#disable-https-certificate-validation)。
- 通常您會使用 vSphere Web 用戶端，從 Azure 備份伺服器電腦上的瀏覽器連線到 vCenter/ESXi 伺服器。 當您第一次執行此動作時，連接並不安全，而且會顯示下列各項。
- 請務必了解 Azure 備份伺服器處理備份的方式。
  - 作為第一個步驟，Azure 備份伺服器會將資料備份到本機磁碟儲存體上。 Azure 備份伺服器使用儲存體集區，這是一組磁碟，而 Azure 備份伺服器可在磁碟上的磁碟區儲存受保護資料的磁碟復原點。 儲存體集區可直接連結儲存體 (DAS)、光纖通道 SAN，或 iSCSI 存放裝置或 SAN。 請務必確定您有足夠的儲存空間來進行 VMware VM 資料的本機備份。
  - 接著，Azure 備份伺服器會從本機磁碟儲存體備份至 Azure。
  - [取得說明](/system-center/dpm/create-dpm-protection-groups#figure-out-how-much-storage-space-you-need)以了解您需要多少儲存空間。 該資訊適用於 DPM，但也可用於 Azure 備份伺服器。

### <a name="set-up-the-certificate"></a>設定憑證

請依下列方式設定安全通道：

1. 在 Azure 備份伺服器上的瀏覽器中，輸入 vSphere Web 用戶端 URL。 如果登入頁面未出現，請確認連線和瀏覽器 Proxy 設定。

    ![vSphere Web 用戶端](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. 在 vSphere Web 用戶端登入頁面上，選取 [ **下載受信任的根 CA 憑證**]。

    ![下載信任的根 CA 憑證](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. 系統會下載名為 **download** 的檔案。 依您的瀏覽器而定，您會收到詢問您要開啟還是儲存檔案的訊息。

    ![下載 CA 憑證](./media/backup-azure-backup-server-vmware/download-certs.png)

4. 使用 .zip 副檔名，將檔案儲存在 Azure 備份伺服器電腦上。

5. 以滑鼠右鍵按一下 [ **download.zip**  >  **全部解壓縮**]。 .zip 檔案會將其內容解壓縮到 **certs** 資料夾，其中包含：
   - 根憑證檔案，其副檔名的開頭是編號序列，例如 .0 和 .1。
   - CRL 檔案，其副檔名的開頭是序列，例如 .r0 和 .r1。 與憑證相關聯的 CRL 檔案。

    ![已下載的憑證](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. 在 **certs** 資料夾中，以滑鼠右鍵按一下根憑證檔案 > [重新命名]****。

    ![將根憑證重新命名](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. 將根憑證的副檔名變更為 .crt 並確認。 檔案圖示會變更為代表根憑證的圖示。

8. 以滑鼠右鍵按一下根憑證，然後從快顯功能表中選取 [安裝憑證]****。

9. 在 [ **憑證匯入嚮導]** 中，選取 [ **本機電腦** ] 作為憑證的目的地，然後選取 **[下一步]**。 如果系統詢問您是否要允許對電腦進行變更，請確認變更。

    ![精靈歡迎畫面](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. 在 [ **憑證存放區** ] 頁面上，選取 [ **將所有憑證放入以下的存放區**]，然後選取 **[流覽]** 以選擇憑證存放區。

    ![憑證存放區](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. 在 [ **選取證書存儲**] 中，選取 [ **信任的根憑證授權** 單位] 作為憑證的目的地資料夾，然後選取 **[確定]**。

    ![憑證目的資料夾](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. 在 **[完成憑證匯入嚮導]** 中，確認資料夾，然後選取 **[完成]**。

    ![確認憑證位於適當的資料夾](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. 在確認憑證匯入之後，請登入 vCenter Server 以確認您的連線是否安全。

### <a name="disable-https-certificate-validation"></a>停用 HTTPS 憑證驗證

如果您的組織內有安全的界限，而且不想要在 VMware 伺服器與 Azure 備份伺服器電腦之間使用 HTTPS 通訊協定，請停用 HTTPS，如下所示：

1. 複製下列文字並貼至 .txt 檔案。

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. 使用名稱 **DisableSecureAuthentication.reg**，將檔案儲存在 Azure 備份伺服器電腦上。

3. 按兩下檔案以啟動登錄項目。

## <a name="create-a-vmware-role"></a>建立 VMware 角色

Azure 備份伺服器需要具有存取 v-Center Server/ESXi 主機權限的使用者帳戶。 建立具有特定權限的 VMware 角色，然後將使用者帳戶與該角色建立關聯。

1. 登入 vCenter Server (或 ESXi 主機，如果您並非使用 vCenter Server)。
2. 在 [導覽 **器** ] 面板中，選取 [ **管理**]。

    ![系統管理](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. 在 [**管理**  >  **角色**] 中，選取 [新增角色] 圖示 (+ 符號) 。

    ![新增角色](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. 在 [**建立角色**  >  **角色名稱**] 中，輸入*BackupAdminRole*。 角色名稱可以是您喜歡的名稱，但它應該是可辨識該角色的用途。

5. 選取下表中摘要說明的許可權，然後選取 **[確定]**。  新的角色會出現在 [角色]**** 面板上的清單中。
   - 選取父標籤旁的圖示，以展開父許可權並查看子許可權。
   - 若要選取 VirtualMachine 權限，您必須深入父子式階層中的好幾層。
   - 您不需要選取父代權限內的所有子權限。

    ![父子式權限階層](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>角色權限

下表會針對您所建立的使用者帳戶，擷取您需要指派的權限：

| vCenter 6.5 使用者帳戶的權限                          | vCenter 6.7 使用者帳戶的權限                            |
|----------------------------------------------------------------------------|----------------------------------------------------------------------------|
| 資料存放區叢集。設定資料存放區叢集                           | 資料存放區叢集。設定資料存放區叢集                           |
| Datastore.AllocateSpace                                                    | Datastore.AllocateSpace                                                    |
| 資料存放區。瀏覽資料存放區                                                 | 資料存放區。瀏覽資料存放區                                                 |
| 資料存放區。低層級檔案作業                                        | 資料存放區。低層級檔案作業                                        |
| 全域。停用方法                                                     | 全域。停用方法                                                     |
| 全域。啟用方法                                                      | 全域。啟用方法                                                      |
| 全域。授權                                                            | 全域。授權                                                            |
| 全域。記錄事件                                                           | 全域。記錄事件                                                           |
| 全域。管理自訂屬性                                            | 全域。管理自訂屬性                                            |
| 全域。設定自訂屬性                                                | 全域。設定自訂屬性                                                |
| 本機。本機作業。建立虛擬機器                               | 本機。本機作業。建立虛擬機器                               |
| 網路。指派網路                                                     | 網路。指派網路                                                     |
| 資源。 將虛擬機器指派給資源集區                          | 資源。 將虛擬機器指派給資源集區                          |
| vApp。新增虛擬機器                                                   | vApp。新增虛擬機器                                                   |
| vApp。指派資源集區                                                  | vApp。指派資源集區                                                  |
| vApp。取消註冊                                                            | vApp。取消註冊                                                            |
| VirtualMachine.Configuration. 新增或移除裝置                         | VirtualMachine.Configuration. 新增或移除裝置                         |
| 虛擬機器。設定。磁碟租用                                   | 虛擬機器。設定。取得磁碟租用                           |
| 虛擬機器。設定。新增磁碟                                 | 虛擬機器。設定。新增磁碟                                 |
| 虛擬機器。設定。進階                                     | 虛擬機器。設定。進階設定                       |
| 虛擬機器。設定。磁碟變更追蹤                         | 虛擬機器。設定。切換磁碟變更追蹤                  |
| 虛擬機器。設定。裝載 USB 裝置                              | 虛擬機器。設定。設定裝載 USB 裝置                    |
| 虛擬機器。設定。延伸虛擬磁碟                          | 虛擬機器。設定。延伸虛擬磁碟                          |
| 虛擬機器。設定。查詢未知的檔案                          | 虛擬機器。設定。查詢未知的檔案                          |
| 虛擬機器。設定。交換檔放置                           | 虛擬機器。設定。變更交換檔放置                    |
| 虛擬機器。客體作業。客體作業程式執行         | 虛擬機器。客體作業。客體作業程式執行         |
| 虛擬機器。客體作業。客體作業修改             | 虛擬機器。客體作業。客體作業修改             |
| 虛擬機器。客體作業。客體作業查詢                   | 虛擬機器。客體作業。客體作業查詢                   |
| 虛擬機器。互動。裝置連線                            | 虛擬機器。互動。裝置連線                            |
| 虛擬機器。互動。由 VIX API 進行的客體作業系統管理 | 虛擬機器。互動。由 VIX API 進行的客體作業系統管理 |
| 虛擬機器。互動。關閉電源                                    | 虛擬機器。互動。關閉電源                                    |
| 虛擬機器。清查。建立新的                                      | 虛擬機器。清查。建立新的                                      |
| 虛擬機器。清查。移除                                          | 虛擬機器。清查。移除                                          |
| 虛擬機器。清查。註冊                                        | 虛擬機器。清查。註冊                                        |
| 虛擬機器。佈建。允許磁碟存取                            | 虛擬機器。佈建。允許磁碟存取                            |
| 虛擬機器。佈建。允許檔案存取                            | 虛擬機器。佈建。允許檔案存取                            |
| 虛擬機器。佈建。允許唯讀磁碟存取                  | 虛擬機器。佈建。允許唯讀磁碟存取                  |
| 虛擬機器。佈建。允許虛擬機器下載               | 虛擬機器。佈建。允許虛擬機器下載               |
| 虛擬機器。快照集管理。 建立快照集                      | 虛擬機器。快照集管理。 建立快照集                      |
| 虛擬機器。快照集管理。移除快照集                       | 虛擬機器。快照集管理。移除快照集                       |
| 虛擬機器。快照集管理。還原為快照集                    | 虛擬機器。快照集管理。還原為快照集                    |

> [!NOTE]
> 下列表格列出 vCenter 6.0 與 vCenter 5.5 使用者帳戶的權限。

| vCenter 6.0 使用者帳戶的權限 | vCenter 5.5 使用者帳戶的權限 |
| --- | --- |
| Datastore.AllocateSpace | Network.Assign |
| 全域。管理自訂屬性 | Datastore.AllocateSpace |
| 全域。設定自訂屬性 | VirtualMachine.Config.ChangeTracking |
| 本機。本機作業。建立虛擬機器 | VirtualMachine.State.RemoveSnapshot |
| 網路。 指派網路 | VirtualMachine.State.CreateSnapshot |
| 資源。 將虛擬機器指派給資源集區 | VirtualMachine.Provisioning.DiskRandomRead |
| 虛擬機器。設定。新增磁碟 | VirtualMachine.Interact.PowerOff |
| 虛擬機器。設定。進階 | VirtualMachine.Inventory.Create |
| 虛擬機器。設定。磁碟變更追蹤 | VirtualMachine.Config.AddNewDisk |
| 虛擬機器。設定。裝載 USB 裝置 | VirtualMachine.Config.HostUSBDevice |
| 虛擬機器。設定。查詢未知的檔案 | VirtualMachine.Config.AdvancedConfig |
| 虛擬機器。設定。交換檔放置 | VirtualMachine.Config.SwapPlacement |
| 虛擬機器。互動。關閉電源 | Global.ManageCustomFields |
| 虛擬機器。清查。 新建 |   |
| 虛擬機器。佈建。允許磁碟 |   |
| 虛擬機器。佈建。 允許唯讀磁碟存取 |   |
| 虛擬機器。快照集管理。建立快照集 |   |
| 虛擬機器。快照集管理。移除快照集 |   |

## <a name="create-a-vmware-account"></a>建立 VMware 帳戶

1. 在 vCenter Server 導覽 **器** ] 面板中，選取 [ **使用者和群組**]。 如果您不是使用 vCenter Server，請在適當的 ESXi 主機上建立帳戶。

    ![[使用者和群組] 選項](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    [ **VCenter 使用者和群組** ] 面板隨即出現。

2. 在 [ **VCenter 使用者和群組** ] 面板中，選取 [ **使用者** ] 索引標籤，然後選取 [新增使用者] 圖示 (+ 符號) 。

    ![[vCenter 使用者和群組] 面板](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. 在 [新增使用者]**** 對話方塊中，新增使用者資訊 > [確定]****。 在此程序中，使用者名稱是 BackupAdmin。

    ![[新增使用者] 對話方塊](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. 若要將使用者帳戶與角色產生關聯，請在 [導覽 **器** ] 面板中選取 [ **全域許可權**]。 在 [ **通用許可權** ] 面板中，選取 [ **管理** ] 索引標籤，然後選取 [新增] 圖示 (+ 符號) 。

    ![[通用權限] 面板](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. 在 [ **通用許可權根目錄-新增許可權**] 中，選取 [ **新增** ] 以選擇使用者或群組。

    ![選擇使用者或群組](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. 在 [選取使用者/群組]**** 中，選擇 [BackupAdmin]**** > [新增]****。 在 [使用者]**** 中，使用者帳戶會使用「網域\使用者名稱」** 格式。 如果您想要使用不同的網域，請從 [網域]**** 清單中選擇。 選取 **[確定]** ，將選取的使用者新增至 [ **新增許可權** ] 對話方塊。

    ![新增 BackupAdmin 使用者](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. 在 [指派的角色]**** 中，從下拉式清單中選取 [BackupAdminRole]**** > [確定]****。

    ![將使用者指派給角色](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

在 [通用權限]**** 面板的 [管理]**** 索引標籤上，新的使用者帳戶和相關聯的角色便會出現在清單中。

## <a name="add-the-account-on-azure-backup-server"></a>在 Azure 備份伺服器上新增帳戶

1. 開啟 Azure 備份伺服器。 如果您在桌面上找不到此圖示，請從應用程式清單開啟 Microsoft Azure 備份。

    ![Azure 備份伺服器圖示](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. 在 Azure 備份伺服器主控台中，選取 [**管理**  >   **生產伺服器**  >  **管理 VMware**]。

    ![Azure 備份伺服器主控台](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. 在 [ **管理認證** ] 對話方塊中，選取 [ **新增**]。

    ![[管理認證] 對話方塊](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. 在 [新增 **認證**] 中，輸入新認證的名稱和描述，並指定您在 VMware 伺服器上定義的使用者名稱和密碼。 我們會使用「Contoso Vcenter 認證」** 這個名稱來識別此程序中的認證。 如果 VMware 伺服器和 Azure 備份伺服器不在相同網域中，請在使用者名稱中指定網域。

    ![Azure 備份伺服器的 [新增認證] 對話方塊](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. 選取 **[新增]** 以新增認證。

    ![新增認證](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>新增 vCenter Server

將 vCenter Server 新增至 Azure 備份伺服器。

1. 在 Azure 備份伺服器主控台中，選取 [**管理**  >  **生產伺服器**  >  **新增**]。

    ![開啟「生產伺服器新增精靈」](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. 在 [**生產伺服器新增嚮導]** 中  >  **選取 [生產伺服器類型**] 頁面，選取 [ **VMware 伺服器**]，然後選取 **[下一步]**

    ![生產伺服器新增精靈](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. 在 [選取電腦]** [伺服器名稱/IP 位址]**  **** 中，指定 VMware 伺服器的 FQDN 或 IP 位址。 如果所有 ESXi 伺服器均由相同的 vCenter 管理，您可以指定 vCenter 名稱。 否則，請新增 ESXi 主機。

    ![指定 VMware 伺服器](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. 在 [SSL 連接埠]**** 中，輸入用來與 VMware 伺服器通訊的連接埠。 預設連接埠為 443，但如果您的 VMware 伺服器接聽其他的連接埠，您可以將它變更。

5. 在 [指定認證]**** 中，選取您稍早建立的認證。

    ![指定認證](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. 選取 [ **新增** ]，將 VMware 伺服器新增至 [伺服器] 清單。 接著，選取 [下一步]  。

    ![新增 VMWare 伺服器和認證](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. 在 [ **摘要** ] 頁面中，選取 [ **新增** ]，將 VMware 伺服器新增至 Azure 備份伺服器。 新伺服器會立即新增，不需要 VMware 伺服器上的代理程式。

    ![將 VMware 伺服器新增至 Azure 備份伺服器](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. 在 [完成]**** 頁面上驗證設定。

   ![[完成] 頁面](./media/backup-azure-backup-server-vmware/summary-screen.png)

如果您有多個未受 vCenter Server 管理的 ESXi 主機，或是有多個 vCenter Server 執行個體，您需要重新執行精靈以新增伺服器。

## <a name="configure-a-protection-group"></a>設定保護群組

新增 VMware VM 以進行備份。 保護群組會收集多個 VM，並將相同的資料保留和備份設定套用至群組中的所有 VM。

1. 在 Azure 備份伺服器主控台中，選取 [ **保護**]，> [ **新增**]。

    ![開啟「建立新保護群組」精靈](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. 在 [ **建立新保護組** 嚮導] 歡迎頁面中，選取 **[下一步]**。

    ![[建立新保護群組] 精靈對話方塊](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. 在 [ **選擇保護群組類型** ] 頁面上，選取 [ **伺服器** ]，然後選取 **[下一步]**。 [選取群組成員]**** 頁面隨即出現。

1. 在 [ **選擇群組成員**] 中，選取您要備份的 vm (或 vm 資料夾) 。 接著，選取 [下一步]  。

    - 當您選取資料夾時，也會選取該資料夾內的 VM 或資料夾以進行備份。 您可以將不想備份的資料夾或 VM 取消選取。
1. 如果 VM 或資料夾已經過備份，您就無法加以選取。 這可確保不會為 VM 建立重複的復原點。

    ![選擇群組成員](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. 在 [選取資料保護方法]**** 頁面上，輸入保護群組的名稱和保護設定。 若要備份至 Azure，請將短期保護設定為 [磁碟]****，並啟用線上保護。 接著，選取 [下一步]  。

    ![選擇資料保護方式](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. 在 [指定短期目標]**** 中，指定備份至磁碟之資料要保留的時間長度。
   - 在 [保留範圍]**** 中，指定磁碟復原點應保留的天數。
   - 在 [同步處理頻率]**** 中，指定擷取磁碟復原點的頻率。
       - 如果您不想設定備份間隔，您可以在 **復原點之前** 檢查，讓備份在每個復原點排定之前執行。
       - 短期備份是完整備份且不會累加。
       - 選取 [ **修改** ] 以變更短期備份發生的時間/日期。

         ![指定短期目標](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. 在 [檢閱磁碟配置]**** 中，檢閱提供給 VM 備份的磁碟空間。 針對 VM。

   - 建議的磁碟配置是根據您指定的保留範圍、工作負載的類型和所保護資料的大小。 進行任何必要的變更，然後選取 **[下一步]**。
   - **資料大小：** 保護群組中的資料大小。
   - **磁碟空間：** 保護群組的建議磁碟空間數量。 如果您想要修改此設定，您配置的總空間應該稍微大於您預估每個資料來源將成長的數量。
   - **共置資料：** 如果您開啟共置功能，受保護的多個資料來源可以對應至單一複本和復原點磁碟區。 不支援所有工作負載的共置。
   - **自動成長：** 如果您開啟此設定，則如果受保護群組中的資料成長到超過初始配置，Azure 備份伺服器會嘗試增加25% 的磁片大小。
   - **儲存集區詳細資料：** 顯示儲存體集區的狀態，包括總計和剩餘的磁碟大小。

    ![檢閱磁碟配置](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. 在 **[選擇複本的建立方式** ] 頁面中，指定您要如何進行初始備份，然後選取 **[下一步]**。
   - 預設值是 [自動透過網路]**** 和 [立即]****。
   - 如果您使用預設值，建議您指定離峰時間。 選擇 [稍後]**** 並指定日期與時間。
   - 對於大量資料或較差的網路狀況，請考慮使用卸除式媒體來離線複寫資料。

    ![選擇複本的建立方式](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. 在 [一致性檢查選項]**** 中，選取如何及何時自動執行一致性檢查。 接著，選取 [下一步]  。
      - 當複本資料變得不一致時，或依據設定的排程，您可以執行一致性檢查。
      - 如果您不想設定自動一致性檢查，可以執行手動檢查。 若要這樣做，以滑鼠右鍵按一下保護群組 > [執行一致性檢查]****。

1. 在 [指定線上保護資料]**** 頁面中，選取要備份的 VM 或 VM 資料夾。 您可以個別選取成員，或選取 [全 **選** ] 來選擇所有成員。 接著，選取 [下一步]  。

    ![指定線上保護資料](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. 在 [指定線上備份排程]**** 頁面上，指定要從本機儲存體將資料備份至 Azure 的頻率。

    - 資料的雲端復原點將會根據排程來產生。 接著，選取 [下一步]  。
    - 產生復原點之後，它會傳輸到 Azure 中的復原服務保存庫。

    ![指定線上備份排程](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. 在 [指定線上保留原則]**** 頁面上，指定從每日/每週/每月/每年備份至 Azure 所建立的資料復原點要保留的時間長度。 然後選取 **[下一步]**。

    - 您可以在 Azure 中保留資料的時間長度沒有限制。
    - 唯一的限制是每個受保護的執行個體不能有超過 9999 個復原點。 在此範例中，受保護的執行個體是 VMware 伺服器。

    ![指定線上保留期原則](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. 在 [ **摘要** ] 頁面上，檢查設定，然後選取 [ **建立群組**]。

    ![保護群組成員和設定的摘要](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-parallel-backups"></a>VMWare 平行備份

>[!NOTE]
> 這項功能適用于 MABS V3 UR1。

使用舊版的 MABS 時，只會在保護群組上執行平行備份。 使用 MABS V3 UR1，單一保護群組中的所有 VMWare Vm 備份都是平行的，可加快 VM 備份的速度。 所有 VMWare 差異複寫作業都會以平行方式執行。 依預設，要平行執行的工作數目會設定為8。

您可以使用登錄機碼來修改作業數目，如下所示 (預設不存在，您必須將它新增) ：

機**碼路徑**：`Software\Microsoft\Microsoft Data Protection Manager\Configuration\ MaxParallelIncrementalJobs\VMWare`<BR>
索引**鍵類型**： DWORD (32 位) 值。

> [!NOTE]
> 您可以將作業數修改為較高的值。 如果您將作業數目設定為1，則會循序執行複寫作業。 若要將數目增加至較高的值，您必須考慮 VMWare 的效能。 請考慮使用中的資源數目，以及 VMWare vSphere Server 所需的其他使用方式，並判斷要以平行方式執行的差異複寫作業數目。 此外，此變更只會影響新建立的保護群組。 針對現有的保護群組，您必須暫時將其他 VM 新增至保護群組。 這應該會據以更新保護群組設定。 在程式完成之後，您可以從保護群組中移除此 VM。

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

若要備份 vSphere 6.7，請執行下列動作：

- 在 DPM Server 上啟用 TLS 1.2

>[!NOTE]
>VMWare 6.7 已將 TLS 啟用為通訊協定。

- 設定登錄機碼，如下所示：

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

## <a name="exclude-disk-from-vmware-vm-backup"></a>從 VMware VM 備份中排除磁碟

> [!NOTE]
> 這項功能適用于 MABS V3 UR1。

使用 MABS V3 UR1，您可以從 VMware VM 備份中排除特定磁片。 設定腳本 **ExcludeDisk.ps1** 位於 `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin folder` 。

若要設定磁碟排除，請依照下列步驟執行：

### <a name="identify-the-vmware-vm-and-disk-details-to-be-excluded"></a>識別要排除的 VMWare VM 與磁碟詳細資料

  1. 在 VMware 主控台上，移至您要排除磁片的 VM 設定。
  2. 選取您要排除的磁碟，並記下該磁碟的路徑。

        例如，若要從 TestVM4 排除硬碟 2，硬碟 2 的路徑是 **[datastore1] TestVM4/TestVM4\_1.vmdk**。

        ![要排除的硬碟](./media/backup-azure-backup-server-vmware/test-vm.png)

### <a name="configure-mabs-server"></a>設定 MABS 伺服器

流覽至已設定 VMware VM 進行保護的 MABS 伺服器，以設定磁片排除。

  1. 取得受 MABS 伺服器保護之 VMware 主機的詳細資料。

        ```powershell
        $psInfo = get-DPMProductionServer
        $psInfo
        ```

        ```output
        ServerName   ClusterName     Domain            ServerProtectionState
        ----------   -----------     ------            ---------------------
        Vcentervm1                   Contoso.COM       NoDatasourcesProtected
        ```

  2. 選取 VMware 主機並列出該 VMware 主機的 VM 保護。

        ```powershell
        $vmDsInfo = get-DPMDatasource -ProductionServer $psInfo[0] -Inquire
        $vmDsInfo
        ```

        ```output
        Computer     Name     ObjectType
        --------     ----     ----------
        Vcentervm1  TestVM2      VMware
        Vcentervm1  TestVM1      VMware
        Vcentervm1  TestVM4      VMware
        ```

  3. 選取您要排除磁碟的 VM。

        ```powershell
        $vmDsInfo[2]
        ```

        ```output
        Computer     Name      ObjectType
        --------     ----      ----------
        Vcentervm1   TestVM4   VMware
        ```

  4. 若要排除磁片，請流覽至 `Bin` 資料夾，然後使用下列參數執行 *ExcludeDisk.ps1* 腳本：

        > [!NOTE]
        > 執行此命令之前，請先停止 MABS 伺服器上的 DPMRA 服務。 否則，腳本會傳回成功，但不會更新排除清單。 在停止服務之前，請確定沒有進行中的作業。

     **若要新增/移除要排除的磁碟，請執行下列命令：**

      ```powershell
      ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-Add|Remove] "[Datastore] vmdk/vmdk.vmdk"
      ```

     **範例**：

     若要新增 TestVM4 的磁片排除，請執行下列命令：

       ```powershell
      C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Add "[datastore1] TestVM4/TestVM4\_1.vmdk"
       ```

      ```output
       Creating C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\excludedisk.xml
       Disk : [datastore1] TestVM4/TestVM4\_1.vmdk, has been added to disk exclusion list.
      ```

  5. 確認已為排除新增磁片。

     **若要檢視特定 VM 的現有排除，請執行下列命令：**

        ```powershell
        ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-view]
        ```

     **範例**

        ```powershell
        C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -view
        ```

        ```output
        <VirtualMachine>
        <UUID>52b2b1b6-5a74-1359-a0a5-1c3627c7b96a</UUID>
        <ExcludeDisk>[datastore1] TestVM4/TestVM4\_1.vmdk</ExcludeDisk>
        </VirtualMachine>
        ```

     設定此 VM 的保護之後，將不會在保護期間列出排除的磁片。

        > [!NOTE]
        > 如果您要針對已受保護的 VM 執行這些步驟，您必須在新增要排除的磁片之後，手動執行一致性檢查。

### <a name="remove-the-disk-from-exclusion"></a>從排除移除磁碟

若要移除排除的磁片，請執行下列命令：

```powershell
C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Remove "[datastore1] TestVM4/TestVM4\_1.vmdk"
```

## <a name="next-steps"></a>後續步驟

如需針對設定備份時的問題進行疑難排解，請檢閱 [Azure 備份伺服器的疑難排解指南](./backup-azure-mabs-troubleshoot.md)。
