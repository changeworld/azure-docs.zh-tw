---
title: 使用 DPM 和 MABS 的 Azure 資料箱進行離線備份
description: 您可以使用 Azure 資料箱從 DPM 與 MABS 離線植入初始備份資料。
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 2fd8a137abf8b76d1587894bfa3fe8447e0d646b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91271489"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>使用 DPM 和 MABS (Preview) 的 Azure 資料箱進行離線植入

> [!NOTE]
> 這項功能適用於 Data Protection Manager (DPM) 2019 UR2 和更新版本。<br><br>
> 這項功能目前為預覽狀態，適用于 Microsoft Azure 備份 Server (MABS) 。 如果您有興趣 Azure 資料箱使用 MABS 進行離線植入，請前往我們 [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) 。

本文說明如何使用 Azure 資料箱將初始備份資料從 DPM 離線並 MABS 至 Azure 復原服務保存庫。

您可以使用 [Azure 資料箱](https://docs.microsoft.com/azure/databox/data-box-overview) ，在離線的情況下將大型初始 DPM/MABS 備份植入 (，而不需要使用網路) 復原服務保存庫。 此程序可節省在透過高延遲網路線上移動大量備份資料時使會取用的時間和網路頻寬。 此功能目前為預覽狀態。

相較於[基於 Azure 匯入/匯出服務的離線備份](backup-azure-backup-server-import-export.md)，基於 Azure 資料箱的離線備份能提供兩個不同的優點：

- 您不需要購買自己的 Azure 相容磁片和連接器。 Azure 資料箱會隨附與所選[資料箱 SKU](https://azure.microsoft.com/services/databox/data/) 相關聯的磁碟。

- Azure 備份 (MARS 代理程式) 可以直接將備份資料寫入到支援的 Azure 資料箱 SKU 上。 這項功能讓您不必為初始備份資料佈建預備位置。 您也不需要透過公用程式來格式化資料，並將該資料複製到磁碟上。

## <a name="supported-platforms"></a>支援的平台

以下是支援的平台：

- Windows Server 2019 64 位元 (Standard、Datacenter、Essentials)
- Windows Server 2016 64 位元 (Standard、Datacenter、Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>備份資料大小和支援的資料箱 SKU

以下是支援的資料箱 SKU：

| 每一伺服器的 MARS) 壓縮之後 (的備份資料大小 \* | 支援的 Azure 資料箱 SKU |
| --- | --- |
| \<= 7.2 TB | [Azure 資料箱磁碟](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7.2 TB 和 <= 80 TB\*\* | [Azure 資料箱 (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\*一般的壓縮比率會有 10% 到 20% 的變動 <br>
\*\*如果您預計單一資料來源的初始備份資料會超過 80 TB，請與 [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) 連絡。

> [!IMPORTANT]
> 單一資料來源中的初始備份資料必須包含在單一 Azure 資料箱或 Azure 資料箱磁片中，且不能在相同或不同 Sku 的多個裝置之間共用。 但 Azure 資料箱則可包含多個資料來源的初始備份。

## <a name="before-you-begin"></a>開始之前

在 DPM/MABS 上執行的 MARS 代理程式應升級為 [最新版本](https://aka.ms/azurebackup_agent) ， (2.0.9171.0 或更新版本的) 。

確認下列事項：

### <a name="azure-subscription-and-required-permissions"></a>Azure 訂用帳戶和必要權限

- 有效的 Azure 訂用帳戶。
- 打算執行離線備份原則的使用者必須是 Azure 訂用帳戶的擁有者。
- 您必須在相同的訂用帳戶中，才能使用資料箱作業和需要植入資料的復原服務保存庫。
    > [!NOTE]
    > 我們建議目標儲存體帳戶和復原服務保存庫位於相同的區域。 不過，這不是必要的。

### <a name="order-and-receive-the-data-box-device"></a>訂購和接收資料箱裝置

請先確定所需的資料箱裝置處於已 *傳遞* 狀態，再觸發離線備份。 請參閱[備份資料大小和支援的資料箱 SKU](#backup-data-size-and-supported-data-box-skus) 以訂購最適合您需求的 SKU。 請遵循[這篇文章](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered)中的步驟，訂購並接收您的資料箱裝置。

> [!IMPORTANT]
> 請勿選取**帳戶種類**的*BlobStorage* 。 DPM/MABS 伺服器需要支援頁面 Blob 的帳戶，但在選取 *BlobStorage* 時不支援此選項。 在建立 Azure 資料箱作業的目標儲存體帳戶時，選取  **儲存體 v2 (一般用途 V2) ** 作為 **帳戶類型** 。

![設定 Azure 資料箱](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>安裝 Azure 資料箱裝置

當您收到 Azure 資料箱裝置之後，視您已訂購的 Azure 資料箱 SKU 而定，請執行下列適當章節中的步驟，為 DPM/MABS 伺服器設定並準備資料箱裝置，以識別並傳送初始備份資料。

### <a name="setup-azure-data-box-disk"></a>設定 Azure 資料箱磁碟

如果您已訂購一或多個 Azure 資料箱磁碟 (每個磁碟高達 8 TB)，請遵循[此處](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)所述的步驟，將您的資料箱磁碟開箱、連接及解除鎖定。

> [!NOTE]
> DPM/MABS 伺服器可能沒有 USB 埠。 在這種情況下，您可以將 Azure 資料箱磁碟連接到另一個伺服器/用戶端，並將裝置的根目錄公開為網路共用。

## <a name="setup-azure-data-box"></a>設定 Azure 資料箱

如果您訂購的 Azure 資料箱 (高達 100 TB 的) ，請依照 [此處](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) 所述的步驟來設定您的資料箱。

### <a name="mount-your-azure-data-box-as-local-system"></a>將您的 Azure 資料箱掛接為本機系統

DPM/MABS 伺服器會在系統內容中運作，因此需要將相同層級的許可權提供給 Azure 資料箱連接的掛接路徑。 請遵循下列步驟，以確保您可以使用 NFS 通訊協定，將資料箱裝置掛接為本機系統。

1. 在 DPM/MABS 伺服器上啟用 NFS 用戶端功能。
指定替代來源：*WIM:D:\Sources\Install.wim:4*
2. 將 **PSExec** 從下載 [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) 到 DPM/MABS 伺服器。
3. 開啟已提升權限的命令提示字元，然後執行下列命令，其中要以包含 *PSExec.exe* 的目錄作為目前的目錄。

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. 以上述命令的結果開啟的命令視窗是在本機系統內容中。 請使用此命令視窗來執行步驟，以將 Azure 分頁 Blob 共用掛接為 Windows Server 上的網路磁碟機。
5. 遵循 [此處](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) 的步驟，透過 NFS 將 DPM/MABS 伺服器連線至資料箱裝置，並在本機系統命令提示字元上執行下列命令，以掛接 Azure 分頁 blob 共用：

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. 掛接好之後，請檢查您是否可以從伺服器存取 X:。 如果可以，請繼續進行本文的下一節。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>將初始備份資料傳輸至 Azure 資料箱裝置

1. 在您的 DPM/MABS 伺服器上，依照下列步驟 [建立新的保護群組](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups)。 如果您要將線上保護新增至現有的保護群組，請以滑鼠右鍵按一下現有的保護群組，然後選取 [ **新增線上保護** ]，然後從 **步驟 8**開始。
2. 在 [選取群組成員] 頁面上，指定您想要備份的電腦和來源。
3. 在 [選取資料保護方法] 頁面上，指定短期與長期備份的處理方式。 請務必選取 [我想要線上保護]。

   ![建立新的保護群組](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. 在 [選取短期目標] 頁面上，指定在磁碟上儲存短期備份的方式。
5. 在 [檢閱磁碟配置] 頁面上，檢閱針對此保護群組配置的存放集區磁碟空間。
6. 在 [選擇複本建立方法] 頁面上，選取 [透過網路自動執行]。
7. 在 [選擇一致性檢查選項] 頁面上，選取自動執行一致性檢查的方式。
8. 在 [ **指定線上保護資料** ] 頁面上，選取您要啟用線上保護的成員。

    ![指定線上保護資料](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. 在 [指定線上備份排程] 頁面上，指定 Azure 增量備份應該執行的頻率。
10. 在 [指定線上保留原則] 頁面上，指定 Azure 如何保留每日/每週/每月/每年備份所建立的復原點。
11. 在嚮導的 [ **選擇線上** 複寫] 畫面上，選擇 [ **使用 Microsoft 擁有的磁片傳輸** ] 選項，然後選取 **[下一步]**。

    ![選擇初始線上複寫](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > 因為此功能處於預覽狀態，所以無法 **使用 Microsoft 擁有的磁片** 來選取傳輸的選項。 [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com)如果您想要將此功能用於 MABS v3，請與我們聯繫。

12. 出現提示時，使用具有 Azure 訂用帳戶擁有者存取權的使用者認證來登入 Azure。 成功登入之後，會顯示下列畫面：

    ![成功登入後](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     DPM/MABS 伺服器接著會在訂用帳戶中提取處於已 *傳遞* 狀態的資料箱工作。

     > [!NOTE]
     > 第一次登入時所花費的時間比平常長。 Azure PowerShell 模組會安裝在背景，而且也會註冊 Azure AD 應用程式。
     >
     >  - 已安裝下列 PowerShell 模組：<br>
          - AzureRM.Profile     *5.8.3*<br>
          - AzureRM.Resources   *6.7.3*<br>
          - AzureRM.Storage     *5.2.0*<br>
          - Azure.Storage       *4.6.1*<br>
     >  - Azure AD 應用程式會註冊為 *AzureOfflineBackup_\<object GUID of the user>* 。

13. 選取您已解除封裝、連線並解除鎖定資料箱磁片的正確資料箱訂單。 選取 [下一步]  。

    ![選取資料箱](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. 在 [偵測 **資料箱** ] 畫面上，輸入您的資料箱裝置路徑，然後選取 [偵測 **裝置**]。

    ![輸入網路路徑](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > 提供 Azure 資料箱磁碟根目錄的網路路徑。 此目錄必須包含名稱為 *PageBlob* 的目錄，如下所示：
    >
    > ![USB 磁碟機](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > 例如，如果磁片的路徑是 `\\mydomain\myserver\disk1\` 且 *disk1* 包含名為 *PageBlob*的目錄，則 DPM/MABS Server wizard 上提供的路徑為 `\\mydomain\myserver\disk1\` 。
    > 如果您[設定 Azure 資料箱 100 TB 裝置](https://docs.microsoft.com/azure/backup/offline-backup-azure-data-box#setup-azure-data-box)，請提供下列路徑作為裝置的網路路徑 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`。

15. 選取 [下一步]  。 在 [ **摘要** ] 頁面上，檢查您的設定，然後選取 [ **建立群組**]。

    ![偵測資料箱](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    下列畫面可確認您已成功建立保護群組。

    ![已建立保護群組](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. 選取上方畫面上的 [ **關閉** ]。

    如此一來，資料的初始複寫就會發生在 DPM/MABS 磁片上。 當它完成保護時，[**保護**] 頁面上的 [群組狀態] 會顯示 [保護狀態 **]** 。

17. 若要起始離線備份複製到 Azure 資料箱裝置，請在 **保護群組**上按一下滑鼠右鍵，然後選擇 [ **建立復原點** ] 選項。 然後選擇 [線上保護] 選項。

    ![建立復原點](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![復原點](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   DPM/MABS 伺服器會開始將您選取的資料備份到 Azure 資料箱裝置。 這可能需要數小時到數天的時間，視 DPM/MABS 伺服器與 Azure 資料箱磁碟之間的資料大小和連線速度而定。

   您可以在 [監視] 窗格中監視作業的狀態。 完成資料備份之後，您會看到類似下面的畫面：

   ![系統管理員主控台](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>備份後步驟

一旦成功將資料備份到 Azure 資料箱磁碟後，請遵循下列步驟。

- 遵循[本文](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)中的步驟，將 Azure 資料箱磁碟寄送至 Azure。 如果您使用 Azure 資料箱 100 TB 裝置，請遵循[這些步驟](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)將 Azure 資料箱寄送到 Azure。
- 在 Azure 入口網站中[監視資料箱作業](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify)。 Azure 資料箱作業 *完成*之後，DPM/MABS 伺服器會在下一次排程備份時，自動將資料從儲存體帳戶移至復原服務保存庫。 如果成功建立復原點，其便會將備份作業標示為 [作業完成]。

  > [!NOTE]
  > DPM/MABS 伺服器會在保護群組建立期間，于排程的時間觸發備份。 不過，這些作業會標示為 [等候 Azure 資料箱作業完成]，直到作業完成為止。

- 在 DPM/MABS 伺服器成功建立對應于初始備份的復原點之後，您可以刪除儲存體帳戶 (或與 Azure 資料箱作業相關聯) 特定內容。

## <a name="troubleshooting"></a>疑難排解

DPM 伺服器上的 Microsoft Azure 備份 (MAB) 代理程式會在您的租用戶中為您建立 Azure AD 應用程式。 此應用程式需要憑證，才能在設定離線植入原則時建立和上傳驗證。

我們會使用 Azure PowerShell 來建立憑證，並將其上傳至 Azure AD 應用程式。

### <a name="issue"></a>問題

設定離線備份時，由於 Azure PowerShell Cmdlet 中的已知程式碼缺失，您無法將多個憑證新增至 MAB 代理程式所建立的相同 Azure AD 應用程式。 如果您已針對相同或不同的伺服器設定離線植入原則，這會對您造成影響。

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>確認問題是否由這個特定根本原因所造成

若要確定失敗是因為上述[問題](#issue)所導致，請執行下列其中一個步驟：

#### <a name="step-1"></a>步驟 1

在設定離線備份時，檢查 DPM/MABS 主控台中是否出現下列錯誤訊息：

![Azure 復原服務代理程式](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>步驟 2

1. 開啟安裝路徑中的 **Temp** 資料夾 (預設的 temp 資料夾路徑是 *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*。尋找 *CBUICurr* 檔案，然後開啟此檔案。
2. 在 *CBUICurr* 檔案中，捲動至最後一行，並檢查失敗是否由於「無法在客戶的帳戶中建立 Azure AD 應用程式認證。 例外狀況：不允許更新為具有 KeyId 的現有認證」 \<some guid> 。

### <a name="workaround"></a>因應措施

若要解決此問題，請執行下列步驟並重試原則設定。

1. 使用不同的帳戶登入出現在 DPM/MABS 伺服器 UI 上的 Azure 登入頁面，該帳戶具有在建立匯入匯出作業的訂用帳戶上具有系統管理員存取權。
2. 如果沒有任何其他伺服器已設定離線植入，且沒有其他任何伺服器相依於 `AzureOfflineBackup_<Azure User Id>` 應用程式，則從 Azure 入口網站 > [Azure Active Directory] > [應用程式註冊] 刪除此應用程式。

   > [!NOTE]
   > 檢查應用程式是否沒有設定 `AzureOfflineBackup_<Azure User Id>` 任何其他離線植入，也沒有其他伺服器相依于此應用程式。 移至 [公開金鑰] 區段下的 [ **設定] > 索引鍵** 。 它不應該加入任何其他的 **公開金鑰** 。 如需參考，請參閱下列螢幕擷取畫面：
   >
   > ![公開金鑰](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>步驟 3

在您嘗試設定離線備份的 DPM/MABS 伺服器上，執行下列動作：

1. 開啟 [管理電腦憑證應用程式] > [個人] 索引標籤，然後尋找名稱為 `CB_AzureADCertforOfflineSeeding_<ResourceId>` 的憑證。
2. 選取上面的憑證，以滑鼠右鍵按一下 [ **所有** 工作]，然後以 .Cer 格式 **匯出** ，但不含私密金鑰。
3. 移至**第 2 點**中所提到的 Azure 離線備份應用程式。 在 [設定] > [金鑰] > [上傳公開金鑰] 中，上傳上述步驟中所匯出的憑證。

   ![上傳公開金鑰](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. 在伺服器中，在 [**執行**] 視窗中輸入**regedit**以開啟登錄。
5. 移至登錄 *Computer\HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*。 以滑鼠右鍵按一下 [CloudBackupProvider]，並新增名稱為 `AzureADAppCertThumbprint_<Azure User Id>` 的新字串值。

    >[!NOTE]
    > 若要取得 Azure 使用者識別碼，請執行下列其中一項動作：
    >
    >- 從與 Azure 連線的 PowerShell 中，執行 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` 命令。
    > - 瀏覽至名稱為 *CurrentUserId* 的登錄路徑 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup`。

6. 以滑鼠右鍵按一下上述步驟所新增的字串，然後選取 [修改]。 在 [值] 中，提供您在 **點 2** 匯出的憑證指紋，然後選取 **[確定]**。
7. 若要取得指紋的值，請按兩下憑證，然後選取 [詳細資料]，並向下滾動到看到 [指紋] 欄位為止。 選取 [ **指紋** ]，然後複製值。

   ![指紋值](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>後續步驟

- [使用自有磁碟進行離線植入 (使用 Azure 匯入/匯出服務)](backup-azure-backup-server-import-export.md)
