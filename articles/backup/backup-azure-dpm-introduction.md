---
title: 準備 DPM 服務器來備份工作負載
description: 在本文中，您將瞭解如何使用 Azure 備份服務來準備 System Center Data Protection Manager (DPM) 備份至 Azure。
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 0089c3d86eb36b82287570ecdfd6e8c782e6fb8a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90975456"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>準備使用 System Center DPM 將工作負載備份到 Azure

本文說明如何準備使用 Azure 備份服務將 System Center Data Protection Manager (DPM) 備份到 Azure。

本文提供：

- 使用 Azure 備份部署 DPM 的概觀。
- 搭配使用 Azure 備份與 DPM 的必要條件和限制。
- 準備 Azure 的步驟，包括設定復原服務備份保存庫，並選擇性地針對保存庫修改 Azure 儲存體的類型。
- 準備 DPM 伺服器的步驟，包括下載保存庫認證、安裝 Azure 備份代理程式，以及在保存庫中註冊 DPM 伺服器。
- 常見錯誤的疑難排解秘訣。

## <a name="why-back-up-dpm-to-azure"></a>為何要將 DPM 備份至 Azure？

[System CENTER DPM](/system-center/dpm/dpm-overview) 會備份檔案和應用程式資料。 DPM 與 Azure 備份的互動方式如下：

- 在**實體伺服器或內部部署 VM 上執行的 DPM** -除了磁片和磁帶備份之外，您還可以將資料備份到 Azure 中的備份保存庫。
- **在 AZURE vm 上執行的 DPM** -從 System Center 2012 R2 Update 3 或更新版本，您可以在 azure vm 上部署 dpm。 您可以將資料備份到連結至 VM 的 Azure 磁碟，或使用 Azure 備份將資料備份到備份保存庫。

將 DPM 伺服器備份至 Azure 的商業優勢包括：

- 針對內部部署 DPM，Azure 備份提供了長期部署至磁帶的替代方式。
- 對於在 Azure VM 上執行的 DPM，Azure 備份可讓您卸載 Azure 磁碟中的儲存體。 在備份保存庫中儲存較舊的資料，可讓您將新資料儲存至磁碟，進而相應增加業務。

## <a name="prerequisites-and-limitations"></a>先決條件和限制

**設定** | **需求**
--- | ---
Azure VM 上的 DPM | System Center 2012 R2 (含 DPM 2012 R2 更新彙總套件 3 或更新版本)。
實體伺服器上的 DPM | System Center 2012 SP1 或更新版本；System Center 2012 R2。
Hyper-V VM 上的 DPM | System Center 2012 SP1 或更新版本；System Center 2012 R2。
VMware VM 上的 DPM | System Center 2012 R2 (含更新彙總套件 5 或更新版本)。
單元 | DPM 服務器應安裝 Windows PowerShell 和 .NET Framework 4.5。
支援的應用程式 | [了解](/system-center/dpm/dpm-protection-matrix) DPM 可備份的項目。
支援的檔案類型 | 以下是可使用 Azure 備份來備份的檔案類型：<br> <li>加密 (只) 完整備份<li> 支援的壓縮 (增量備份)  <li> 支援的稀疏 (增量備份) <li> 壓縮和稀疏 (視為稀疏) 
不支援的檔案類型 | <li>區分大小寫的檔案系統上的伺服器<li> 永久連結 (略過) <li>  (跳過的重新分析點) <li> 已略過加密和壓縮 () <li> 已略過加密和稀疏 () <li> 壓縮資料流<li> 剖析資料流程
本機儲存體 | 您想要備份的每部機器都必須有本機可用儲存空間，其大小至少為要備份之資料大小的5%。 例如，備份 100GB 的資料時，在臨時位置中至少需要 5 GB 的可用空間。
保存庫儲存體 | 您可以備份至 Azure 備份保存庫的資料量沒有限制，但是資料來源的大小 (例如虛擬機器或資料庫) 不應超過 54400 GB。
Azure ExpressRoute | 您可以透過具有公用對等互連的 Azure ExpressRoute 備份資料 (適用于舊線路) 和 Microsoft 對等互連。 不支援透過私人對等互連進行備份。<br/><br/> **使用公用對等互連**：確定存取下列網域/位址：<br/><br/>- `http://www.msftncsi.com/ncsi.txt` <br/><br/>- `microsoft.com` <br/><br/>-`.WindowsAzure.com`<br/><br/>-`.microsoftonline.com`<br/><br/>-`.windows.net`<br/><br/> **使用 Microsoft 對等互連時**，請選取下列服務/區域和相關的群組值：<br/><br/>-Azure Active Directory (12076:5060) <br/><br/>-根據復原服務保存庫的位置 (Microsoft Azure 區域) <br/><br/>-根據復原服務保存庫的位置 Azure 儲存體 () <br/><br/>如需詳細資訊，請參閱 [ExpressRoute 路由需求](../expressroute/expressroute-routing.md)。<br/><br/>**注意**：新電路的公用對等互連已被取代。
Azure 備份代理程式 | 如果 DPM 執行於 System Center 2012 SP1 上，請為 DPM SP1 安裝彙總套件 2 或更新版本。 這是代理程式安裝的必要條件。<br/><br/> 本文將說明如何部署最新版的 Azure 備份代理程式，也就是 Microsoft Azure 復原服務 (MARS) 代理程式。 如果您已部署舊版，請更新為最新版本，以確保備份可如預期運作。

開始之前，您必須具有已啟用 Azure 備份功能的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 請閱讀 [Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>修改儲存體設定

您可以選擇異地備援儲存體或本地備援儲存體。

- 根據預設，保存庫具有異地備援儲存體。
- 如果保存庫是主要備份，請讓選項繼續設定為異地備援儲存體。 如果您想要更便宜但不持久的選項，請使用下列程序來設定本地備援儲存體。
- 深入瞭解 [Azure 儲存體](../storage/common/storage-redundancy.md)，以及 [異地冗余](../storage/common/storage-redundancy.md#geo-redundant-storage)、 [本機冗余](../storage/common/storage-redundancy.md#locally-redundant-storage) 和 [區域冗余](../storage/common/storage-redundancy.md#zone-redundant-storage) 的儲存體選項。
- 在初次備份之前應先修改儲存體設定。 如果您已備份某項目，請先修改儲存體設定，再將該項目備份到保存庫。

若要編輯儲存體複寫設定︰

1. 開啟保存庫儀表板。

2. 在 [ **管理**] 中，選取 [ **備份基礎結構**]。

3. 在 [備份組態]**** 功能表中，選取保存庫的儲存體選項。

    ![備份保存庫的清單](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>下載保存庫認證

您在保存庫中註冊 DPM 伺服器時需使用保存庫認證。

- 保存庫認證檔是入口網站針對每個備份保存庫所產生的憑證。
- 入口網站接著會將公開金鑰上傳至「存取控制服務」(ACS)。
- 在機器註冊工作流程進行期間，憑證的私密金鑰會提供給使用者以進行機器驗證。
- 根據驗證結果，Azure 備份服務會將資料傳送至已識別的保存庫。

### <a name="best-practices-for-vault-credentials"></a>保存庫認證的最佳做法

若要取得認證，請從 Azure 入口網站的安全通道下載保存庫認證檔案：

- 保存庫認證僅在註冊工作流程期間使用。
- 確保保存庫認證檔安全無虞且不會遭到破解，是您自己的負責。
  - 若失去認證的控制權，則可使用保存庫認證來向保存庫註冊其他機器。
  - 不過，備份資料會使用屬於您的複雜密碼進行加密，所以現有的備份資料不會遭到洩漏。
- 確定檔案儲存在可從 DPM 服務器存取的位置。 如果它儲存在檔案共用/SMB 中，請檢查存取權限。
- 保存庫認證將於 48 小時後過期。 您可以視需要下載新的保存庫認證，次數不限。 不過，在註冊工作流程進行期間，您只能使用最新的保存庫認證檔案。
- Azure 備份服務不會知道憑證的私密金鑰，且私密金鑰無法在入口網站或服務中取得。

將保存庫認證檔下載至本機電腦，如下所示：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 開啟要用來註冊 DPM 伺服器的保存庫。
3. 在 [ **設定**] 中，選取 [ **屬性**]。

    ![開啟保存庫功能表](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. 在 [**屬性**  >  **備份認證**] 中，選取 [**下載**]。 入口網站會使用保存庫名稱和目前日期的組合來產生保存庫認證檔，並使其可供下載。

    ![下載認證](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. 選取 [儲存] 以將 **保存** 庫認證下載至資料夾，或 **另存** 新檔並指定位置。 產生檔案需要一分鐘的時間。

## <a name="install-the-backup-agent"></a>安裝備份代理程式

Azure 備份所備份的每部電腦都必須安裝備份代理程式，也就是 Microsoft Azure 復原服務 (MARS) 代理程式。 將代理程式安裝在 DPM 伺服器上，如下所示：

1. 開啟 DPM 伺服器要註冊到的保存庫。
2. 在 [ **設定**] 中，選取 [ **屬性**]。

    ![開啟保存庫設定](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. 在 [屬性]**** 頁面上，下載 Azure 備份代理程式。

    ![下載](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. 下載之後，請執行 MARSAgentInstaller.exe， 將代理程式安裝在 DPM 機器上。
5. 選取代理程式的安裝資料夾和快取資料夾。 快取位置的可用空間至少必須是備份資料的 5%。
6. 若您使用 Proxy 伺服器連接至網際網路，請在 [ **Proxy 組態** ] 畫面上，輸入 Proxy 伺服器詳細資料。 若您使用已驗證的 Proxy，請在此畫面中輸入使用者名稱和密碼的詳細資料。
7. Azure 備份代理程式會安裝 .NET Framework 4.5 和 Windows PowerShell (若尚未安裝) 以完成安裝。
8. 安裝代理程式之後，請**關閉**視窗。

    ![關閉](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>在保存庫中註冊 DPM 伺服器

1. 在 [DPM 系統管理員主控台] > **管理**] 中，選取 [ **線上**]。 選取 [註冊]。 [註冊伺服器精靈] 隨即開啟。
2. 在 [Proxy 組態]**** 中，視需要指定 Proxy 設定。

    ![Proxy 組態](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. 在 [備份保存庫]**** 中，瀏覽至您已下載的保存庫認證檔，並加以選取。

    ![保存庫認證](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. 在 [節流設定]**** 中，您可以選擇性地為備份啟用頻寬節流。 您可以為指定的工作時數和天數設定速度限制。

    ![節流設定](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. 在 [復原資料夾設定]**** 中，指定在資料復原期間可使用的位置。

    - Azure 備份會使用此位置作為復原資料的暫存區域。
    - 完成資料復原後，Azure 備份會清除此區域中的資料。
    - 位置必須有足夠的空間來容納您預期要平行復原的專案。

    ![復原資料夾設定](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. 在 [ **加密] 設定**中，產生或提供複雜密碼。

    - 複雜密碼可用來加密雲端的備份。
    - 指定最少 16 個字元。
    - 將檔案儲存在安全的位置，在復原時將會用到。

    ![加密](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > 您擁有加密複雜密碼，但 Microsoft 無法看到它。
    > 如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。

7. 選取 [ **註冊** ] 以向保存庫註冊 DPM 服務器。

將伺服器成功註冊至保存庫之後，您就可以開始備份至 Microsoft Azure。 您必須在 DPM 主控台中設定保護群組，以將工作負載備份至 Azure。 [瞭解如何](/system-center/dpm/create-dpm-protection-groups) 部署保護群組。

## <a name="troubleshoot-vault-credentials"></a>對保存庫認證進行疑難排解

### <a name="expiration-error"></a>到期錯誤

保存庫認證檔在從入口網站下載) 之後， (只有48小時才會生效。 如果您在此畫面中遇到任何錯誤 (例如，「提供的保存庫認證檔案已過期」 ) ，請登入 Azure 入口網站並再次下載保存庫認證檔。

### <a name="access-error"></a>存取錯誤

確定保存庫認證檔可在安裝應用程式可存取的位置中使用。 如果您遇到存取權相關的錯誤，請將保存庫認證檔複製至此電腦中的暫存位置並重試作業。

### <a name="invalid-credentials-error"></a>認證無效錯誤

如果您遇到不正確保存庫認證錯誤 (例如「提供的保存庫認證無效」 ) 檔案可能已損毀或沒有與復原服務相關聯的最新認證。

- 從入口網站下載新的保存庫認證檔後重試作業。
- 當您在 Azure 入口網站中選取 [下載保存 **庫認證** ] 選項時，通常會出現此錯誤，快速連續兩次。 在此情況下，僅第二個保存庫認證檔為有效。
