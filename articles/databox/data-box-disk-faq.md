---
title: Microsoft Azure 資料箱磁碟常見問題集 | 資料中的 Microsoft Docs
description: 包含 Azure 資料箱磁碟的常見問題和解答，這是可讓您將大量資料傳輸至 Azure 的雲端解決方案
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: f2231b74034ba6cea672a7bbf68f506fce423d45
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826135"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure 資料箱磁碟：常見問題集

Microsoft Azure 資料箱磁碟雲端解決方案可讓您以快速、實惠和可靠的方式，將數 TB 的資料傳送至 Azure。 此常見問題集包含您在 Azure 入口網站中使用資料箱磁碟時可能遇到的問題及其解答。 

問題和解答可分為下列幾個類別：

- 關於此服務
- 設定和連線 
- 追蹤狀態
- 移轉資料 
- 確認和上傳資料 


## <a name="about-the-service"></a>關於此服務

### <a name="q-what-is-azure-data-box-service"></a>Q. 什麼是 Azure 資料箱服務？ 
A.  Azure 資料箱服務是針對離線資料擷取而設計的。 此服務可管理依設計供不同儲存體容量的資料傳輸使用的各種產品。 

### <a name="q-what-are-azure-data-box-disks"></a>Q. 什麼是 Azure 資料箱磁碟？
A. Azure 資料箱磁碟可讓您快速、實惠且安全地將數 TB 的資料輸入和輸出 Azure。 Microsoft 會為您提供 1 到 5 個磁碟，儲存體容量上限為 35 TB。 您可以透過 Azure 入口網站中的資料箱服務輕鬆設定、連接和解除鎖定這些磁碟。  

磁碟會使用 Microsoft BitLocker 磁碟機加密進行加密，而您的加密金鑰會在 Azure 入口網站上受到管理。 然後，您可以從客戶的伺服器複製資料。 在資料中心，Microsoft 會使用快速的私人網路上傳連結，將您的資料從磁碟機移轉至雲端，並上傳至 Azure。

### <a name="q-when-should-i-use-data-box-disks"></a>Q. 何時應使用資料箱磁碟？
A. 如果您有不超過 40 TB 的資料要傳輸至 Azure，則使用資料箱磁碟將有其效益。

### <a name="q-what-is-the-price-of-data-box-disks"></a>Q. 資料箱磁碟的價格為何？
A. 如需資料箱磁碟價格的資訊，請移至[定價頁面](https://azure.microsoft.com/pricing/details/databox/disk/)。

### <a name="q-how-do-i-get-data-box-disks"></a>Q. 如何取得資料箱磁碟？ 
A.  若要取得 Azure 資料箱磁碟，請登入 Azure 入口網站，並建立磁碟的資料箱訂單。 請提供您的連絡資訊和通知詳細資料。 在您下訂單後，根據可用性，磁碟會在 10 天內寄送給您。

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>Q. 使用資料箱磁碟最多可在一個執行個體中傳輸多少資料量？
A. 就各有 8 TB (可用容量為 7 TB) 的 5 個磁碟而言，可用容量上限為 35 TB。 因此，您可以在一個執行個體中傳輸 35 TB 的資料。 若要傳輸更多資料，您必須訂購更多磁碟。

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>Q. 如何確認在我的區域中是否適用資料箱磁碟？ 
A.  若要查看目前何處可以使用資料箱磁碟，請前往[區域可用性](data-box-disk-overview.md#region-availability)。  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>Q. 在哪些區域中可以使用資料箱磁碟來儲存資料？
A. 美國、加拿大、澳大利亞、西歐和北歐、南韓和日本內的所有區域均支援資料箱磁碟。 只有 Azure 公用雲端區域受到支援。 Azure Government 或其他主權雲端不受支援。

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>Q. 在哪些區域中可以使用資料箱磁碟來儲存資料？
A. 美國、加拿大、澳大利亞、西歐和北歐、南韓和日本內的所有區域均支援資料箱磁碟。 只有 Azure 公用雲端區域受到支援。 Azure Government 或其他主權雲端不受支援。

### <a name="q-how-can-i-import-source-data-present-at-my-location-in-one-countryregion-to-an-azure-region-in-a-different-country"></a>Q. 如何將位於某個國家/地區位置的來源資料匯入至不同國家/地區的 Azure 區域？
A. 資料箱磁碟僅支援在與目的地相同的國家/地區內內嵌資料，而且不會跨越任何國際框線。 唯一的例外是歐盟 (EU) 中的訂單，資料箱磁片可以在任何歐盟國家/地區運送。

例如，如果您想要將位於加拿大位置的資料移至 Azure WestUS 儲存體帳戶，您可以透過下列方式達成：

### <a name="option-1"></a>選項 1： 

使用[azure 匯入/匯出服務](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)，從加拿大的來源位置將包含資料的[支援磁片](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements?toc=/azure/storage/blobs/toc.json#supported-disks)傳送到 azure WestUS 資料中心。

### <a name="option-2"></a>選項 2：

1. 選擇儲存體帳戶（例如 Cananda Central），以在加拿大訂購資料箱磁碟。 SSD 磁片 (s) 會從 Azure 資料中心寄送至 Azure 資料中心，並加拿大中部在訂單建立期間于加拿大) 提供寄送位址 (。

2. 當您將內部部署伺服器的資料複製到磁片之後，請使用 Microsoft 提供的退貨標籤，將資料返回加拿大的 Azure 資料中心。 資料箱磁碟 (s 上的資料) 然後上傳至訂單建立期間所選擇之加拿大 Azure 區域中的目的地儲存體帳戶。

3. 然後，您可以使用 AzCopy 之類的工具，將資料複製到 WestUS 中的儲存體帳戶。 此步驟會產生不包含在資料箱磁碟帳單中的 [標準儲存體](https://azure.microsoft.com/pricing/details/storage/) 和 [頻寬費用](https://azure.microsoft.com/pricing/details/bandwidth/) 。

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>Q. 如果遇到任何資料箱磁碟方面的問題，該與誰連絡？
A. 如果您遇到任何資料箱磁碟方面的問題，請[連絡 Microsoft 支援服務](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support)。

## <a name="configure-and-connect"></a>設定和連線
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>Q. 可以在訂單中指定資料箱磁碟的數目嗎？
A.  否。 您會根據磁碟的資料大小和可用性取得 8 TB 磁碟 (最多 5 個磁碟)。  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>Q. 如何將資料箱磁碟解除鎖定？ 
A.  在 Azure 入口網站中，移至您的資料箱磁碟訂單，並瀏覽至 [裝置詳細資料]。 複製通行金鑰。 從 Azure 入口網站下載及擷取您作業系統適用的資料箱磁碟解鎖工具。 在有資料要複製到磁碟的電腦上執行此工具。 請提供用來將磁碟解除鎖定的通行金鑰。 所有磁碟均可用相同的通行金鑰解除鎖定。 

如需逐步指示，請移至[解除鎖定 Windows 用戶端上的磁碟](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)或[解除鎖定 Linux 用戶端上的磁碟](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)。

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>Q. 是否可使用 Linux 主機電腦連線至資料箱磁碟，並將資料複製到該處？
A.  是。 Linux 和 Windows 用戶端皆可用來連線至資料箱磁碟，並將資料複製到其中。 如需詳細資訊，請移至主機電腦[支援的作業系統](data-box-disk-system-requirements.md)清單。

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. 我的磁碟已派送，但現在我想要取消此訂單。 為何 [取消] 按鈕無法使用？
A.  在訂購磁碟後，您只能在出貨之前取消訂單。 一旦磁碟已派送，您即無法取消訂單。 不過，您可以自費退回磁碟。 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>Q. 是否可以同時將多個資料箱磁碟連線至主機電腦以傳輸資料？
A. 是。 您可以將多個資料箱磁碟連線至相同的主機電腦以傳輸資料，並且可以平行執行多個複製作業。

## <a name="track-status"></a>追蹤狀態

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>Q. 從我下訂單到磁碟送到的這段期間，要如何追蹤磁碟的狀態？ 
A.  您可以在 Azure 入口網站中追蹤資料箱磁碟訂單的狀態。 當您建立訂單時，系統也會提示您提供通知電子郵件。 如果您有提供的話，您將會透過電子郵件獲知訂單的所有狀態變更。 請取得關於如何[設定通知電子郵件](data-box-portal-ui-admin.md#edit-notification-details)的資訊。

### <a name="q-how-do-i-return-the-disks"></a>Q. 如何送回磁碟？ 
A.  Microsoft 會在寄送包裹中提供隨附於資料箱磁碟的出貨標籤。 請將標籤黏貼於寄送包裹，並將密封的包裹投遞到貨運公司地點。 如果標籤受損或遺失，請移至 [概觀] > [下載出貨標籤]，並下載新的寄返標籤。

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>我可以自取我的資料箱磁碟訂單嗎？ 我可以透過我選擇的貨運公司來寄回磁碟嗎？
A. 是。 Microsoft 也提供僅 US Gov 區域適用的自助運送。 當您對資料箱磁碟下訂單時，您可以選擇自助運送選項。 若要自取資料箱磁碟訂單，請執行下列步驟：
    
1. 在您下訂單之後，我們就會處理訂單並準備磁碟。 當您的訂單可供取貨時，您會收到電子郵件通知。 
2. 當訂單可供取貨之後，請您前往 Azure 入口網站中的訂單，並瀏覽至 [概觀] 刀鋒視窗。 
3. 您會在 Azure 入口網站中看到含有代碼的通知。 請傳送電子郵件至 [Azure資料箱作業小組](mailto:adbops@microsoft.com)，並將代碼提供給他們。 小組會提供位置並安排取貨日期和時間。 收到電子郵件通知之後，您必須在 5 個工作天內連絡小組。

資料複製和驗證完成後，請採取下列步驟來寄回您的磁碟：

1. 一旦完成資料驗證，請拔除磁碟。 移除連接纜線。
2. 使用發泡包裝材料將所有磁碟和連接纜線包裝在一起，然後放入包裹。 如果遺漏配件，則可能酌收費用。

    - 重複使用最初出貨的包裝。 我們建議您使用安全的氣泡片來包裝磁碟。
    - 確保穩固裝入磁碟，以免在箱子內移動。
3. 在 Azure 入口網站中，移至您訂單的 [概觀] 刀鋒視窗。 您應該會看到含有代碼的通知。
4. 請使用該代碼並傳送電子郵件至 [Azure資料箱作業小組](mailto:adbops@microsoft.com)，並將代碼提供給他們。 他們會提供您交送磁碟的位置和時間。


## <a name="migrate-data"></a>移轉資料

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>Q. 資料箱磁碟可處理的資料大小上限為何？  
A.  資料箱磁碟解決方案最多可以有 5 個磁碟，可用容量上限為 35 TB。 磁碟本身為 8 TB (可用容量為 7 TB)。

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>Q. 資料箱磁碟所支援的最大區塊 Blob 和分頁 Blob 大小為何？ 
A.  大小上限由 Azure 儲存體限制所控管。 區塊 Blob 上限大約是 4.768 TiB，而分頁 Blob 大小上限為 8 TiB。 如需詳細資訊，請參閱 [Blob 儲存體的延展性和效能目標](../storage/blobs/scalability-targets.md)。

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>Q. 資料箱磁碟的資料傳輸速度為何？
A. 使用透過 USB 3.0 連線的磁碟進行測試時，磁碟效能可達 430 MB/s。 實際數字會隨使用的檔案大小而不同。 如果檔案較小，您可能會看到較低的效能。

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. 如何得知我的資料在傳輸期間安全無虞？ 
A.  資料箱磁碟會使用 BitLocker AES-128 位元加密進行加密，且通行金鑰只能在 Azure 入口網站中取得。 請使用您的帳戶認證登入 Azure 入口網站，以取得通行金鑰。 在執行資料箱磁碟解除鎖定工具時，必須提供此通行金鑰。

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>Q. 如何將資料複製到資料箱磁碟？ 
A.  使用 SMB 複製工具 (例如 Robocopy、Diskboss) 甚或 Windows 檔案總管的拖放功能，將資料複製到磁碟上。

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. 是否有任何秘訣可加快複製資料的速度？
A.  若要加快複製程序：

- 使用多個資料複製串流。 例如，採用 Robocopy 時，請使用多執行緒選項。 如需與使用之確切命令有關的詳細資訊，請移至[教學課程：將資料複製到 Azure 資料箱磁碟並確認](data-box-disk-deploy-copy-data.md#copy-data-to-disks)。
- 使用多個工作階段。
- 不透過網路共用進行複製 (此方式可能會受限於網路速度)，而是一律將資料放置在磁碟所連線到的本機電腦上。
- 確定您的整個複製程序均使用 USB 3.0 或更新版本。 請下載並使用 [USBView 工具](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview)，以識別連線至電腦的 USB 控制器和 USB 裝置。
- 對用來複製資料的電腦建立效能的基準。 請下載並使用 [Bluestop FIO 工具](https://ci.appveyor.com/project/axboe/fio)，以建立伺服器硬體效能的基準。 選取最新的 x86 或 x64 組建，選取 [成品] 索引標籤，然後下載 MSI。

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>Q. 如果來源資料具有小型檔案 (KB 或 MB)，應如何加快資料傳輸速度？
A.  若要加快複製程序：

- 在快速的儲存體上建立本機 VHDx，或在 HDD/SSD (速度較慢) 上建立空白的 VHD。
- 將其掛接到 VM。
- 將檔案複製到 VM 的磁碟。

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>Q. 是否可對資料箱磁碟使用多個儲存體帳戶？
A.  否。 目前僅支援對資料箱磁碟使用一個儲存體帳戶 (一般或傳統)。 經常性存取和非經常性存取 Blob 均受支援。 目前僅支援美國、西歐及北歐等區域位於 Azure 公用雲端中的儲存體帳戶。

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>Q. 可供我的資料使用的工具箱磁碟工具組是什麼？
A. 資料箱磁碟隨附的工具組包含三項工具：
 - **資料箱磁碟解除鎖定工具**：使用此工具可將 Microsoft 寄送的加密磁碟解除鎖定。 使用此工具將磁碟解除鎖定時，您必須提供可從 Azure 入口網站中的資料箱磁碟訂單取得的通行金鑰。 
 - **資料箱磁碟驗證工具**：使用此工具可根據 Azure 命名慣例來驗證大小、格式和 Blob 名稱。 它也會為複製的資料產生總和檢查碼，後續可用來驗證上傳至 Azure 的資料。
 - **資料箱磁碟分割複製工具**：如果您使用多個磁碟，且其中包含必須分割並複製到所有磁碟上的大型資料集，請使用此工具。 此工具目前適用於 Windows。 受控磁碟不支援此工具。 這項工具在複製資料時，也會進行驗證，因此您使用此工具時可以略過驗證步驟。

工具組則同時適用於 Windows 和 Linux。 您可以從下列位置下載工具組：
- [下載適用於 Windows 的資料箱磁碟工具組](https://aka.ms/databoxdisktoolswin) 
- [下載適用於 Linux 的資料箱磁碟工具組](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>Q. 是否可以使用資料箱磁碟將資料傳輸至 Azure 檔案儲存體，然後再搭配 Azure 檔案同步來使用資料？ 
A. 資料箱磁碟支援 Azure 檔案儲存體，但將不適合使用 Azure 檔案同步。如果檔案資料會搭配 Azure 檔案同步來使用，則中繼資料不會保留下來。


## <a name="verify-and-upload"></a>確認和上傳

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>Q. 在送回磁碟後，多久可以存取我在 Azure 中的資料？ 
A.  當複製資料的訂單狀態顯示為完成時，您就應該能夠立即存取您的資料。

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. 我的資料在上傳後會存放於 Azure 的何處？
A.  當您在磁碟上的 *BlockBlob* 和 *PageBlob* 資料夾下複製資料後，系統會在 Azure 儲存體帳戶中為 *BlockBlob* 和 *PageBlob* 資料夾下的每個子資料夾建立容器。 如果您直接複製 *BlockBlob* 和 *PageBlob* 資料夾下的檔案，這些檔案將會位於 Azure 儲存體帳戶下的預設容器 *$root* 中。 當您將資料複製到 *AzureFile* 資料夾下的資料夾中，隨即會建立檔案共用。

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. 我剛剛發現我的容器並未遵循 Azure 命名需求。 我的資料會不會無法上傳至 Azure？
A. 如果容器名稱包含大寫字母，這些字母會自動轉換為小寫。 如果名稱在其他方面不符合規範 (特殊字元、其他語言等等)，則會上傳失敗。 如需詳細資訊，請移至 [Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>Q. 如何驗證我複製到多個資料箱磁碟上的資料？
A.  資料複製完成後，您可以執行 DataBoxDiskImport 資料夾中提供的 `DataBoxDiskValidation.cmd`，以產生驗證的總和檢查碼。 如果您有多個磁碟，您必須為每個磁碟開啟一個命令視窗，並執行此命令。 請記住，此作業可能十分耗時 (數小時)，視資料大小而定。

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>Q. 在我送回磁碟之後，我的資料將會如何？
A.  當資料複製到 Azure 之後，磁碟中的資料將會根據 NIST SP 800-88 修訂 1 指導方針安全地清除。  

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. 我的資料在傳輸期間會受到怎樣的保護？ 
A.  資料箱磁碟會使用 AES-128 Microsoft BitLocker 加密進行加密。 必須使用單一通行金鑰才能將所有磁碟解除鎖定並存取資料。

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>Q. 如果我將更多資料新增至資料箱磁碟，是否需要重新執行總和檢查碼驗證？
A. 是。 如果您決定要驗證資料 (建議您這麼做)，則必須在新增更多資料磁碟後重新執行驗證。

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>Q. 我所有的磁碟都已用來傳輸資料，且需要訂購更多磁碟。 有快速下訂單的方法嗎？
A. 您可以複製您的上一筆訂單。 複製會建立與之前相同的訂單，因此您只需編輯訂單詳細資料即可，而不需要輸入地址、連絡人和通知詳細資料。

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>Q. 我已將資料複製到 ManagedDisk 資料夾。 卻沒有看到任何受控磁碟使用為受控磁碟指定的資源群組。 我的資料是否上傳至 Azure，以及該如何尋找？
A. 是。 您的資料已上傳至 Azure，但如果您沒有看到任何受控磁碟使用指定的資源群組，可能是因為該資料無效。 如果分頁 Blob、區塊 Blob、Azure 檔案儲存體及受控磁碟無效，則全都會移至下列資料夾：
 - 分頁 Blob 會移至開頭為 *databoxdisk-invalid-pb-* 的區塊 Blob 容器。
 - Azure 檔案儲存體會移至開頭為 *databoxdisk-invalid-af-* 的區塊 Blob 容器。
 - 受控磁碟會移至開頭為 *databoxdisk-invalid-md-* 的區塊 Blob 容器。

## <a name="next-steps"></a>後續步驟

- 檢閱[資料箱磁碟系統需求](data-box-disk-system-requirements.md)。
- 了解[資料箱磁碟限制](data-box-disk-limits.md)。
- 在 Azure 入口網站中快速部署 [Azure 資料箱磁碟](data-box-disk-quickstart-portal.md)。
