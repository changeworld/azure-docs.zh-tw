---
title: 使用 MARS 代理程式備份 Windows 電腦
description: 使用 Azure 備份 Microsoft Recovery Services （MARS）代理程式來備份 Windows 機器。
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 990929cc95d1c25117873ca39415d33370456b91
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025532"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>使用 Azure 備份 MARS 代理程式備份 Windows 機器

本文說明如何使用[Azure 備份](backup-overview.md)服務和 MICROSOFT AZURE 復原服務（MARS）代理程式（也稱為 Azure 備份代理程式）來備份 Windows 電腦。

在本文中，您將了解如何：

> [!div class="checklist"]
>
> * 確認必要條件，並建立復原服務保存庫。
> * 下載並設定 MARS 代理程式
> * 建立備份原則和排程。
> * 執行隨選備份。

## <a name="about-the-mars-agent"></a>關於 MARS 代理程式

Azure 備份使用 MARS 代理程式，將檔、資料夾和系統狀態從內部部署機器和 Azure Vm 備份至 Azure 中的備份復原服務保存庫。 您可以執行代理程式，如下所示：

* 直接在內部部署 Windows 電腦上執行代理程式，讓它們可以直接備份到 Azure 中的備份復原服務保存庫。
* 在執行 Windows （並存于 Azure VM 備份延伸模組）的 Azure Vm 上執行代理程式，以備份 VM 上的特定檔案和資料夾。
* 在 Microsoft Azure 備份伺服器 (MABS) 或 System Center Data Protection Manager (DPM) 伺服器上執行代理程式。 在此案例中，機器和工作負載會備份至 MABS/DPM，然後 MABS/DPM 會使用 MARS 代理程式備份至 Azure 中的保存庫。
您可以備份的內容取決於安裝的代理程式。

> [!NOTE]
> 備份 Azure Vm 的主要方法是使用 VM 上的 Azure 備份延伸模組。 此步驟會備份整個 VM。 如果您想要備份 VM 上的特定檔案和資料夾，您可能會想要在擴充功能旁安裝和使用 MARS 代理程式。 [深入了解](backup-architecture.md#architecture-built-in-azure-vm-backup)。

![備份程序步驟](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>開始之前

* [深入瞭解](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)Azure 備份使用 MARS 代理程式來備份 Windows 電腦。
* [瞭解](backup-architecture.md#architecture-back-up-to-dpmmabs)在次要 MABS 或 DPM 服務器上執行 MARS 代理程式的備份架構。
* 請[參閱](backup-support-matrix-mars-agent.md)支援的內容，以及可使用 MARS 代理程式進行備份的內容。
* 確認您想要備份之電腦上的網際網路存取權。
* 若要將伺服器或用戶端備份至 Azure，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立 [免費帳戶](https://azure.microsoft.com/free/) 。

### <a name="verify-internet-access"></a>確認網際網路存取

如果您的電腦具有有限的網際網路存取權，請確定電腦或 proxy 上的防火牆設定允許這些 Url 和 IP 位址：

#### <a name="urls"></a>URL

* www\.msftncsi.com
* *.Microsoft.com
* *.WindowsAzure.com
* *.microsoftonline.com
* *.windows.net

#### <a name="ip-addresses"></a>IP 位址

* 20.190.128.0/18
* 40.126.0.0/18

對上述所有 Url 和 IP 位址的存取，都會使用埠443上的 HTTPS 通訊協定。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫會儲存您在一段時間內建立的所有備份和復原點，並包含套用至備份電腦的備份原則。 請依照下列方式建立保存庫：

1. 使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

2. 搜尋並選取 [復原服務保存庫]。

    ![建立復原服務保存庫的步驟 1](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. 在 [復原**服務保存庫**] 功能表上，按一下 [ **+ 新增**]。

    ![建立復原服務保存庫的步驟 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. 在 [名稱]中，輸入易記名稱來識別保存庫。

   * 必須是 Azure 訂用帳戶中唯一的名稱。
   * 它可以包含 2 到 50 個字元。
   * 該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。

5. 選取要在其中建立保存庫的 Azure 訂用帳戶、資源群組和地理區域。 備份資料會傳送至保存庫。 接著，按一下 [建立]。

    ![建立復原服務保存庫的步驟 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * 建立保存庫可能需要一些時間。
   * 請監視入口網站右上方區域中的狀態通知。 如果幾分鐘後您看不到保存庫，**請按一下 [** 重新整理]。

     ![按一下 [重新整理] 按鈕。](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>設定儲存體備援

Azure 備份會自動處理保存庫的儲存體。 您必須指定該儲存體的複寫方式。

1. 從 [復原服務保存庫] 刀鋒視窗，按一下 [新增保存庫]。 在 [**設定**] 區段下，按一下 [**屬性**]。
2. 在 [**屬性**] 的 [**備份**設定] 底下，按一下 [**更新**]。

3. 選取 [儲存體] 複寫類型，然後**按一下 [儲存]** 。

      ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* 如果您使用 Azure 做為主要的備份儲存體端點，我們建議您繼續使用預設的**異地多餘**設定。
* 如果您未使用 Azure 做為主要的備份儲存體端點，則選擇 [本地備援]，以減少 Azure 儲存體成本。
* 深入瞭解[地理](../storage/common/storage-redundancy-grs.md)和[本機](../storage/common/storage-redundancy-lrs.md)冗余。

## <a name="download-the-mars-agent"></a>下載 MARS 代理程式

下載 MARS 代理程式，以便在您要備份的電腦上進行安裝。

* 如果您已在任何電腦上安裝代理程式，請確定您執行的是最新版本。
* 最新版本可在入口網站中取得，或使用[直接下載](https://aka.ms/azurebackup_agent)

1. 在保存庫的 [**消費者入門**] 底下，按一下 [**備份**]。

    ![開啟備份目標刀鋒視窗](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. 在 [**您的工作負載在何處執行？** ] 中，選取 [**內部部署**]。 您應該選取此選項，即使您想要在 Azure VM 上安裝 MARS 代理程式也一樣。
3. 在 [**您要備份什麼？** ] 中，選取 [檔案**和資料夾**] 和/或 [**系統狀態**]。 還有一些其他選項可供使用，但只有在您執行次要備份伺服器時才支援。 按一下 [**準備基礎結構**]。

      ![設定檔案和資料夾](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. 在 [**準備基礎結構**] 的 [**安裝復原服務代理程式**] 下，下載 MARS 代理程式。

    ![準備基礎結構](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. 在下載快顯功能表中，按一下 [儲存]。 根據預設，**MARSagentinstaller.exe** 檔案會儲存至 [下載] 資料夾。

6. 現在，請檢查**已下載或使用最新的復原服務代理程式**，然後下載保存庫認證。

    ![下載保存庫認證](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. 按一下 [檔案]。 檔案會下載到您的下載資料夾。 您無法開啟保存庫認證檔案。

## <a name="install-and-register-the-agent"></a>安裝和註冊代理程式

1. 在您要備份的機器上執行**marsagentinstaller.exe。**
2. 在 MARS 代理程式安裝精靈 >**安裝設定** 中，指定您要安裝代理程式的位置，以及要用於快取的位置。 然後按一下 [下一步]。
   * Azure 備份會先使用快取來儲存資料快照集，然後再將其傳送至 Azure。
   * 快取位置的可用空間應該等於您要備份之資料大小的5%。

    ![MARS wizard 安裝設定](./media/backup-configure-vault/mars1.png)

3. 在 [ **Proxy**設定] 中，指定在 Windows 電腦上執行的代理程式將如何連線到網際網路。 然後按一下 [下一步]。

   * 如果您使用的是自訂 proxy，請指定 proxy 設定，並視需要提供認證。
   * 請記住，代理程式需要存取[這些 url](#verify-internet-access)。

     ![MARS wizard 網際網路存取](./media/backup-configure-vault/mars2.png)

4. 在 [**安裝**] 中，檢查必要條件檢查，然後按一下 [**安裝**]。
5. 安裝代理程式之後，按一下 [**繼續註冊**]。
6. 在 **註冊伺服器**中， > 保存**庫識別**，流覽並選取您下載的認證檔案。 然後按一下 [下一步]。

    ![註冊-保存庫認證](./media/backup-configure-vault/register1.png)

7. 在 [**加密設定**] 中，指定將用來加密和解密電腦備份的複雜密碼。

    * 將加密複雜密碼存放在安全的位置。
    * 如果您遺失或忘記複雜密碼，Microsoft 將無法協助復原備份資料。 將檔案存放在安全的位置。 您需要它來還原備份。

8. 按一下 [完成]。 現已安裝代理程式，且已向保存庫註冊您的電腦。 您已準備好可以設定及排程備份。

## <a name="create-a-backup-policy"></a>建立備份原則

備份原則會指定何時製作資料的快照集來建立復原點，以及要保留復原點的時間長度。

* 您可以使用 MARS 代理程式來設定備份原則。
* Azure 備份不會自動將日光節約時間（DST）列入考慮。 這可能會造成實際時間和排程備份時間之間的某些差異。

建立原則，如下所示：
1. 下載並註冊 MARS 代理程式之後，請啟動代理程式主控台。 您可以透過在您的電腦中搜尋 **Microsoft Azure 備份**來找出備份。  
2. 在 [**動作**] 中，按一下 [**排程備份**]。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-first-backup.png)
3. 在 排程備份 中 >**開始**使用，按**下一步**。
4. 在 [**選取要備份的專案**] 中，按一下 [**新增專案**]。

    ![選取要備份的專案](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. 在 [**選取專案**] 中，選取您想要備份的內容，然後按一下 **[確定]** 。

    ![選取要備份的專案](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. 在 [**選取要備份的專案**] 頁面中，按 **[下一步]** 。
7. 在 [**指定備份排程**] 頁面中，指定您要進行每日或每週備份的時間。 然後按一下 [下一步]。

    - 執行備份時，會建立復原點。
    - 在您的環境中建立的復原點數目取決於您的備份排程。


8. 您可以排程每日備份（一天最多三次）。 例如，螢幕擷取畫面顯示兩個每日備份，一個在午夜，另一個在下午6:00。

    ![每日排程](./media/backup-configure-vault/day-schedule.png)


9. 您也可以執行每週備份。 例如，螢幕擷取畫面顯示每個替代星期日 & 星期三上午9:30 和 1:00 AM 進行的備份。

    ![每週排程](./media/backup-configure-vault/week-schedule.png)


10. 在 [**選取保留原則**] 頁面上，指定儲存資料歷程記錄複本的方式。 然後按一下 [下一步]。

    - 保留設定會指定應該儲存的復原點，以及儲存的時間長度。
    - 例如，當您設定每日保留設定時，您會指出在指定的每日保留期間，最新的復原點會保留指定的天數。 或者，另一個範例是，您可以指定每月的保留原則，表示每個月30日所建立的復原點應該儲存12個月。
    - 每日和每週復原點保留通常會符合備份排程。 這表示當根據排程觸發備份時，備份所建立的復原點會儲存在每日或每週保留原則中指出的持續時間。
    - 例如，在下列螢幕擷取畫面中：

        -   午夜和下午6:00 的每日備份會保留七天。
        -   在星期六午夜和下午6:00 所進行的備份會保留四周。
        -   在當月的上一周的午夜和下午6:00 進行的備份會保留12個月。
        -   3月最後一周的星期六所進行的備份會保留10年。

        ![保留範例](./media/backup-configure-vault/retention-example.png)


11. 在 **[選擇初始備份類型**] 中，決定您是否要透過網路進行初始備份，或使用離線備份（如需離線備份的詳細資訊，請參閱這[篇文章](offline-backup-azure-data-box.md)）。 若要透過網路進行初始備份，請選取 **[自動透過網路**]，然後按 **[下一步]** 。

    ![初始備份類型](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. 在 [**確認**] 中，檢查資訊，然後按一下 **[完成]** 。

    ![確認備份類型](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. 當精靈建立好備份排程時，請按一下 [關閉]。

    ![確認修改備份進程](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

您必須在安裝代理程式的每部電腦上建立原則。

### <a name="perform-the-initial-backup-offline"></a>離線執行初始備份

您可以透過網路或離線，自動執行初始備份。 如果您有大量資料需要傳送大量的網路頻寬，則初始備份的離線植入會很有用。 您會執行離線轉移，如下所示：

1. 您要將備份資料寫入至預備位置。
2. 您可以使用 AzureOfflineBackupDiskPrep 工具，將資料從預備位置複製到一或多個 SATA 磁片。
3. 此工具會建立 Azure 匯入作業。 [深入瞭解](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)Azure 匯入和匯出。
4. 您會將 SATA 磁片傳送至 Azure 資料中心。
5. 在資料中心，磁片資料會複製到 Azure 儲存體帳戶。
6. Azure 備份會將資料從儲存體帳戶複製到保存庫，並排定增量備份。

[深入瞭解](offline-backup-azure-data-box.md)離線植入。

### <a name="enable-network-throttling"></a>啟用網路節流

您可以藉由啟用網路節流，控制 MARS 代理程式使用網路頻寬的方式。 如果您需要在工作時間內備份資料，但想要控制用於備份和還原活動的頻寬量，則節流會很有説明。

* Azure 備份網路節流會使用本機作業系統上的[服務品質（QoS）](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) 。
* Windows Server 2012 和更新版本都提供備份的網路節流。 作業系統應執行最新的 service pack。

啟用網路節流，如下所示：

1. 在 MARS 代理程式中，按一下 [**變更屬性**]。
2. 在 [**節流**] 索引標籤上，勾選 [**啟用備份作業的網際網路頻寬使用節流**設定]。

    ![網路節流](./media/backup-configure-vault/throttling-dialog.png)
3. 指定工作期間允許的頻寬和非工作時間。 頻寬值從 512 Kbps 開始，最高可達 1023 MBps。 然後按一下 [確定]。

## <a name="run-an-on-demand-backup"></a>執行隨選備份

1. 在 MARS 代理程式中，按一下 [**立即備份**]。

    ![Windows Server 立即備份](./media/backup-configure-vault/backup-now.png)

2. 如果 MARS 代理程式版本是2.0.9169.0 或更新版本，則可以設定自訂保留。 在 [**保留備份截止**時間] 區段中，從顯示的行事曆選擇日期：

   ![保留備份行事曆](./media/backup-configure-vault/mars-ondemand.png)

3. 在 [**確認**] 中，檢查設定，然後按一下 [**備份**]。
4. 按一下 [關閉] 即可關閉精靈。 如果您在備份完成之前執行此動作，則嚮導會繼續在背景中執行。
5. 完成初始備份之後，備份主控台中會顯示 [作業已完成] 狀態。

## <a name="on-demand-backup-policy-retention-behavior"></a>隨選備份原則保留行為

>[!NOTE]
>僅適用于2.0.9169.0 之前的 MARS 代理程式版本
>

* 如需詳細資訊，請參閱[建立備份原則](backup-configure-vault.md#create-a-backup-policy)的步驟8

| 備份排程選項 | 備份的資料會保留多久？
| -- | --
| 排程備份間隔： * 天 | **預設保留**：相當於「每日備份的保留天數」 <br/><br/> **例外**狀況：如果長期保留的每日排程備份組（周、月、年）失敗，則會在這項失敗的排程備份被視為長期保留時，立即觸發隨選備份。 否則，下一個排定的備份會被視為長期保留。<br/><br/> **範例**：如果（假設）在星期四上午8:00 建立的排程備份失敗，且相同的備份會被視為每週/每月/每年保留，則在下次排定的備份（例如）星期五之前觸發的第一個隨選備份，會自動標記為每週/每月/每年保留8:00，並適用于星期四 8:00 am 備份。
| 排程備份間隔： * 每週 | **預設保留期**：1天 <br/> 下一天會刪除每週備份原則所建立之資料來源的隨選備份，即使它們是資料來源的最新備份。 <br/><br/> **例外**狀況：如果長期保留的每週排程備份組（周、月、年）失敗，則會在這項失敗的排程備份被視為長期保留時，立即觸發隨選備份。 否則，下一個排定的備份會被視為長期保留。 <br/><br/> **範例**：如果（假設）在星期四上午8:00 建立的排程備份失敗，且相同的備份被視為每月/每年保留，則在下一個排定的備份（例如）星期四，8:00 am 會自動標記為適用于星期四 8:00 am 備份的每月/每年保留期

## <a name="next-steps"></a>後續步驟

[瞭解如何還原](backup-azure-restore-windows-server.md)檔案。
