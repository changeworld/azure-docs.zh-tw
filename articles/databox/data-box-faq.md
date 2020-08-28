---
title: Microsoft Azure 資料箱常見問題集 | 資料中的 Microsoft Docs
description: 包含 Azure 資料箱的常見問題和解答，這是可讓您將大量資料傳輸到 Azure 的雲端解決方案。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: 7fbc69100a2cc5c23150fcc0c19e6c38ba6d438d
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051163"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure 資料箱：常見問題集

Microsoft Azure 資料箱混合式解決方案可讓您以快速、實惠且可靠的方式，使用傳輸裝置將數 TB 的資料傳送到 Azure。 此常見問題集包含您在 Azure 入口網站中使用資料箱時可能遇到的問題與其解答。 

問題和解答可分為下列幾個類別：

- 關於此服務
- 訂購裝置
- 設定和連線 
- 追蹤狀態
- 複製資料 
- 寄送裝置
- 確認和上傳資料 
- 監管鏈支援

## <a name="about-the-service"></a>關於此服務

### <a name="q-what-is-azure-data-box-service"></a>Q. 什麼是 Azure 資料箱服務？ 
A.  Azure 資料箱服務是針對離線資料擷取而設計的。 此服務管理一系列不同儲存容量的產品，這些產品都是為了資料傳輸而量身訂做。 

### <a name="q-what-is-azure-data-box"></a>Q. 什麼是 Azure 資料箱？
A. Azure 資料箱可讓您快速、實惠且安全地將數 TB 的資料輸入 Azure 中。 您可以透過 Azure 入口網站訂購資料箱裝置。 Microsoft 會透過區域性貨運公司，將有 80 TB 可用容量的存放裝置寄送給您。 

一旦收到裝置之後，您就可以快速地使用本機 Web UI 來設定它。 將您伺服器的資料複製到裝置，或從裝置複製到伺服器，並將裝置寄回 Azure。 針對匯入順序，在 Azure 資料中心內，您的資料會自動從裝置上傳至 Azure。 Azure 入口網站中的資料箱服務會以端對端的方式追蹤這整個程序。

### <a name="q-when-should-i-use-data-box"></a>Q. 何時應使用資料箱？
A. 如果您有 40-500 TB 的資料要傳輸至 Azure 或從 Azure 傳輸，您將會受益于使用資料箱。 若資料大小 < 40 TB，請使用資料箱磁碟；若資料大小 > 500 TB，請註冊 [Data Box Heavy](data-box-heavy-overview.md)。

### <a name="q-what-is-the-price-of-data-box"></a>Q. 資料箱的價格為何？
A. 資料箱僅收取少許費用，提供 10 天使用。 當您在 Azure 入口網站中建立訂單時，選取產品型號會顯示該裝置的費用。 Azure 儲存體的標準出貨費用和費用也適用。 匯出訂單會遵循類似于匯入訂單的定價模型，但可能會收取額外的輸出費用。 

如需詳細資訊，請移至 [Azure 資料箱定價](https://azure.microsoft.com/pricing/details/storage/databox/) 和輸出 [費用](https://azure.microsoft.com/pricing/details/bandwidth/)。 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>Q. 使用資料箱一次最多可以傳輸多少資料量？
A. 資料箱擁有 100 TB 的原始容量，而可用容量則為 80 TB。 您可以使用資料箱傳輸最多 80 TB 的資料。 若要傳輸更多資料，您必須訂購更多裝置。

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>Q. 如何確認在我的區域中是否提供資料箱？ 
A.  如需哪些國家/地區提供資料箱的資訊，請移至[區域可用性](data-box-overview.md#region-availability)。  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>Q. 在哪些區域中可以使用資料箱來儲存資料？
A. 美國、西歐、北歐、法國、英國、日本、澳大利亞和加拿大內的所有區域都支援資料箱。 如需詳細資訊，請移至[區域可用性](data-box-overview.md#region-availability)。

### <a name="q-how-can-i-import-source-data-at-my-location-in-a-particular-country-to-an-azure-region-in-a-different-countryregion-or-export-data-from-an-azure-region-in-one-country-to-a-different-countryregion"></a>Q. 如何將特定國家/地區位置的來源資料匯入至不同國家/地區的 Azure 區域，或將資料從一個國家/地區的 Azure 區域匯出到不同的國家/地區？

資料箱僅支援在與目的地相同的國家/地區內內嵌或輸出資料，而且不會跨越任何國際框線。 唯一的例外狀況是歐盟 (EU) 中的訂單，資料箱可以在任何歐盟國家/地區運送。

例如，在匯入案例中，如果您有想要移至 Azure WestUS 儲存體帳戶的加拿大來源資料，您可以透過下列方式達成：

1. 在 [Cananda] 中選擇儲存體帳戶，以在加拿大訂購資料箱。 裝置會從加拿大的 Azure 資料中心寄送至加拿大) 在訂單建立期間提供的運送位址 (。

2. 將內部部署資料複製到資料箱之後，請將裝置退回至加拿大的 Azure 資料中心。 然後，資料箱上的資料會上傳至在訂單建立期間所選擇的加拿大 Azure 區域中的目的地儲存體帳戶。

3. 然後，您可以使用 AzCopy 之類的工具，將資料複製到 WestUS 中的儲存體帳戶。 此步驟會產生不包含在資料箱計費中的 [標準儲存體](https://azure.microsoft.com/pricing/details/storage/) 和 [頻寬費用](https://azure.microsoft.com/pricing/details/bandwidth/) 。

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>Q. 如果遇到任何資料箱方面的問題，該與誰連絡？
A. 如果您遇到任何資料箱方面的問題，請連絡 [Microsoft 支援服務](data-box-disk-contact-microsoft-support.md)。

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>Q. 我遺失了資料箱。 裝置遺失是否需要費用？
A. 是。 裝置遺失或損壞都需要費用。 [定價頁面](https://azure.microsoft.com/pricing/details/storage/databox/)及[產品服務條款](https://www.microsoft.com/licensing/product-licensing/products)均涵蓋這筆費用。


## <a name="order-device"></a>訂購裝置

### <a name="q-how-do-i-get-data-box"></a>Q. 如何取得資料箱？ 
A.  若要取得 Azure 資料箱，請登入 Azure 入口網站並建立資料箱訂單。 請提供您的連絡資訊和通知詳細資料。 在您下單後，根據可用性，資料箱會在 10 天內寄送給您。 如需詳細資訊，請移至[訂購資料箱](data-box-deploy-ordered.md)。

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>Q. 我無法在 Azure 入口網站中建立資料箱訂單。 為什麼會這樣？
A. 如果您無法建立資料箱訂單，問題可能與您的訂用帳戶類型或存取權限有關。 

請檢查您的訂用帳戶。 資料箱僅適用於 Enterprise 合約 (EA) 和雲端解決方案提供者 (CSP) 訂用帳戶供應項目。 如果您的訂用帳戶不屬於上述任何一種類型，請連絡 Microsoft 支援服務以升級您的訂用帳戶。

如果您的訂用帳戶有支援的供應項目類型，請檢查您的訂用帳戶存取層級。 您必須是訂用帳戶中的參與者或擁有者，才能建立訂單。

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>Q. 我訂購了一些資料箱裝置。 我無法再建立任何額外訂單。 為什麼會這樣？
A. 針對每個商務服務界限 (特定的國家/地區和區域的組合) 中的每個訂用帳戶，最多允許 5 個有效訂單。 如果您需要訂購額外的裝置，請連絡 Microsoft 支援服務以提高訂用帳戶的限制。

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>Q. 當我嘗試建立訂單時，我收到無法提供資料箱服務的通知。 這代表什麼？
A. 這表示您選取的國家/地區和區域組合無法提供資料箱服務。 變更這個組合可能可以讓您使用資料箱服務。 如需可提供服務的區域清單，請移至[資料箱的區域可用性](data-box-overview.md#region-availability)。

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>Q. 我幾天前已下訂了資料箱訂單。 何時才會收到資料箱？
A. 當您下單時，我們會檢查裝置是否可供您的訂單使用。 如果裝置可用，我們將會在 10 天內寄出。 可以想見的是可能會有需求高峰期間。 在此情況下，您的訂單將會排入佇列，而且您可以在 Azure 入口網站中追蹤狀態變更。 如果您的訂單在 90 天後仍未完成，訂單將會自動取消。

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>Q. 我已將資料箱填滿資料，而需要再訂購一個資料箱。 有快速下訂單的方法嗎？
A. 您可以複製您的上一筆訂單。 複製會建立與之前相同的訂單，因此您只需編輯訂單詳細資料即可，而不需要輸入地址、連絡人和通知詳細資料。 只允許匯入順序的複製。

## <a name="configure-and-connect"></a>設定和連線

### <a name="q-how-do-i-unlock-the-data-box"></a>Q. 如何將資料箱解除鎖定？ 
A.  在 Azure 入口網站中，移至您的資料箱訂單，並瀏覽至 [裝置詳細資料]。 複製解除鎖定密碼。 使用此密碼，登入資料箱上的本機 Web UI。 如需詳細資訊，請移至[教學課程：打開包裝、接上纜線、連接您的 Azure 資料箱](data-box-deploy-set-up.md)。

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>Q. 是否可使用 Linux 主機電腦連線到資料箱，並將資料複製到該處？
A.  是。 您可以使用資料箱連線至 SMB 與 NFS 用戶端。 如需詳細資訊，請移至主機電腦[支援的作業系統](data-box-system-requirements.md)清單。

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. 我的資料箱已派送，但現在我想要取消此訂單。 為何 [取消] 按鈕無法使用？
A.  您只能在資料箱訂購之後，並在訂單處理之前取消訂單。 一旦資料箱訂單處理完畢，您就無法取消訂單。 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>Q. 我是否可以將資料箱同時連線到多部主機電腦來傳輸資料？
A. 是。 您可以將多個資料箱連線到多部主機電腦以傳輸資料，而且可以平行執行多個複製作業。 如需詳細資訊，請移至[教學課程：將資料複製到 Azure 資料箱](data-box-deploy-copy-data.md)。

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>Q. 是否可以連線到資料箱上的兩個 10 GbE 介面來傳輸資料？
A. 是。 在資料箱上可以連線這兩個 10 GbE 介面來同時複製資料。 如需有關如何複製資料的詳細資訊，請移至[教學課程：將資料複製到 Azure 資料箱](data-box-deploy-copy-data.md)。

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>Q. 前操作面板上的系統錯誤 LED 指示燈亮起。 我該怎麼辦？
A. 如果系統錯誤 LED 指示燈亮起，表示您的系統健康情況不良。 [連絡 Microsoft 支援服務](data-box-disk-contact-microsoft-support.md) 以進行後續步驟。

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>Q. 我無法在 Azure 入口網站中存取資料箱解除鎖定密碼。 為什麼會這樣？
A. 如果您無法在 Azure 入口網站中存取解除鎖定密碼，請檢查您的訂用帳戶與儲存體帳戶的權限。 請確定您有資源群組層級的參與者或擁有者權限。 如果沒有，則您至少需要擁有資料箱操作員角色權限，才能看到存取認證。

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>Q. 資料箱是否支援連接埠通道設定？ 是否支援 MPIO？
A. 我們在資料箱上不支援連接埠通道設定、多重路徑 IO (MPIO) 設定或 vLAN 設定。

## <a name="track-status"></a>追蹤狀態

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>Q. 從我下單到將裝置送回的這段期間，要如何追蹤資料箱的狀態？ 
A.  您可以在 Azure 入口網站中追蹤資料箱訂單的狀態。 當您建立訂單時，系統也會提示您提供通知電子郵件。 如果您有提供的話，您將會透過電子郵件獲知訂單的所有狀態變更。 請取得關於如何[設定通知電子郵件](data-box-portal-ui-admin.md#edit-notification-details)的資訊。

### <a name="q-how-do-i-return-the-device"></a>Q. 如何送回裝置？ 
A.  Microsoft 會在電子墨水顯示器上顯示出貨標籤。 如果出貨標籤沒有顯示在電子墨水顯示器上，請移至 [概觀] > [下載出貨標籤]。 下載並列印標籤，將標籤插入裝置上的塑膠貨籤中，然後將裝置投遞到貨運公司地點。 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>Q. 我收到電子郵件通知我的裝置已經送達 Azure 資料中心。 我要如何知道資料是否正在上傳？
A. 您可以移至 Azure 入口網站中的資料箱訂單，然後移至 [概觀]。 如果已開始將資料上傳至 Azure，您將會在右窗格中看到複製進度。 

## <a name="migrate-data"></a>移轉資料

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>Q. 資料箱可處理的資料大小上限為何？  
A.  資料箱的可用儲存容量為 80 TB。 您可以使用單一資料箱裝置來儲存大小介於 40 TB - 80 TB 這段範圍的資料。 針對較大的資料大小 (最多 500 TB)，您可以訂購多個資料箱裝置。 針對超過 500 TB 的資料大小，請註冊 Data Box Heavy。  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>Q. 資料箱所支援的區塊 Blob 與分頁 Blob 大小上限為何？ 
A.  大小上限由 Azure 儲存體限制所控管。 區塊 Blob 上限大約是 4.768 TiB，而分頁 Blob 大小上限為 8 TiB。 如需詳細資訊，請參閱 [Blob 儲存體的延展性和效能目標](../storage/blobs/scalability-targets.md)。

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. 如何得知我的資料在傳輸期間安全無虞？ 
A. 我們實作了多種安全性功能，以確保您的資料箱在傳輸期間安全無虞。 其中包括防竄改封條、硬體和軟體竄改偵測、裝置解除鎖定密碼。 如需詳細資訊，請移至 [Azure 資料箱安全性和資料保護](data-box-security.md)。

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>Q. 如何將資料複製到資料箱？ 
A.  如果您使用 SMB 用戶端，可以使用 SMB 複製工具 (例如 Robocopy、Diskboss 或甚至是 Windows 檔案總管的拖放功能) 將資料複製到裝置上。 

如果您使用 NFS 用戶端，可以使用 [rsync](https://rsync.samba.org/)、[FreeFileSync](https://www.freefilesync.org/)、[Unison](https://www.cis.upenn.edu/~bcpierce/unison/) 或 [Ultracopier](https://ultracopier.first-world.info/)。 

如需詳細資訊，請移至[教學課程：將資料複製到 Azure 資料箱](data-box-deploy-copy-data.md)。

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. 是否有任何秘訣可加快複製資料的速度？
A.  若要加快複製程序：

- 使用多個資料複製串流。 例如，採用 Robocopy 時，請使用多執行緒選項。 如需與使用之確切命令有關的詳細資訊，請移至[教學課程：將資料複製到 Azure 資料箱並確認](data-box-deploy-copy-data.md)。
- 使用多個工作階段。
- 請務必將資料放置在資料箱所連接的本機電腦上，而不要透過網路共用進行複製 (此方式可能會受限於網路速度)。
- 對用來複製資料的電腦建立效能的基準。 請下載並使用 [Bluestop FIO 工具](https://ci.appveyor.com/project/axboe/fio)，以建立伺服器硬體效能的基準。 選取最新的 x86 或 x64 組建，選取 [成品] 索引標籤，然後下載 MSI。

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM's disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>Q. 是否可對資料箱使用多個儲存體帳戶？
A.  是。 資料箱可支援最多 10 個儲存體帳戶、一般用途、傳統或 Blob 儲存體。 經常性存取和非經常性存取 Blob 均受支援。 


## <a name="ship-device"></a>寄送裝置

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>Q. 我已收到裝置，但裝置似乎已經損毀。 我該怎麼辦？
A. 如果您收到的裝置已損毀，或是有遭到竄改的跡象，請勿使用該裝置。 請[連絡 Microsoft 支援服務](data-box-disk-contact-microsoft-support.md)並儘快送回裝置。 您也可以建立新的資料箱訂單來更換裝置。 在此情況下，將不會對您收取更換裝置的費用。

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>Q. 我可以自取我的資料箱訂單嗎？ 我可以透過我選擇的貨運公司寄回資料箱嗎？
A. 是。 Microsoft 也提供自我管理的運送。 當您對資料箱下訂單時，您可以選擇自我管理的運送選項。 如需詳細資訊，請參閱 [自我管理資料箱的運送](data-box-portal-customer-managed-shipping.md)。

### <a name="q-will-my-data-box-devices-cross-countryregion-borders-during-shipping"></a>Q. 我的資料箱服務在運送期間可以跨國家/區域邊界使用嗎？
A. 所有的資料箱裝置都是在與目的地相同的國家/區域內寄送，並不會跨越任何跨國界。 唯一的例外狀況是位於歐盟 (EU) 的訂單，該地區的裝置可以在任何歐盟國家/區域之間運送。 這適用於資料箱及 Data Box Heavy 裝置。

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>Q. 我在美國東部訂購了一個資料箱，但收到的裝置卻是從美國西部的某個位置出貨。 我應該將裝置送歸何處？
A. 我們會設法讓您盡快收到資料箱裝置。 我們雖會優先從最接近您儲存體帳戶位置的資料中心出貨，但會從有可用庫存的任何 Azure 資料中心寄送裝置。 您的資料箱應送歸出貨標籤中所顯示的同一個出貨位置。

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>Q. 電子墨水顯示器並未顯示寄回用的出貨標籤。 我該怎麼辦？
A. 如果電子墨水顯示器並未顯示寄回用的出貨標籤，請執行下列步驟：
- 將先前寄送時的舊出貨標籤與任何貼紙移除。
- 移至 Azure 入口網站中的訂單。 移至 [概觀]，然後**下載出貨標籤**。 如需詳細資訊，請移至[下載出貨標籤](data-box-portal-admin.md#download-shipping-label)。
- 列印出貨標籤，然後將它插入裝置上隨附的乾淨塑膠保護套。 
- 確認出貨標籤清晰可見。 

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. 我的資料在傳輸期間會受到怎樣的保護？ 
A.  在傳輸期間，資料箱上的下列功能可協助保護資料。
 - 資料箱磁碟使用 AES 256 位元加密進行加密。 
 - 裝置已鎖定，且需要解除鎖定密碼才能進入並存取資料。
如需詳細資訊，請移至[資料箱安全性功能](data-box-security.md)。  

### <a name="q-i-have-finished-prepare-to-ship-for-my-import-order-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>Q. 我已完成匯入順序的準備，並關閉裝置。 是否還可以將更多資料新增到資料箱中？
A. 是。 您可以將裝置開機，並新增更多資料。 一旦您完成資料複製，將需要再次執行**準備寄送**。

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>Q. 我收到我的裝置，但無法開機？ 如何將裝置送回？
A. 如果您的裝置無法開機，請移至您在 Azure 入口網站中的訂單。 下載出貨標籤並將該標籤貼在裝置上。 如需詳細資訊，請移至[下載出貨標籤](data-box-portal-admin.md#download-shipping-label)。

## <a name="verify-and-upload"></a>確認和上傳

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>Q. 在送回資料箱後，多久可以在 Azure 中存取我的資料？ 
A.  當 [資料複製] 的訂單狀態顯示為 [完成] 時，您應該就能立即存取您的資料。

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. 我的資料在上傳後會存放於 Azure 的何處？
A.  當您將資料複製到資料箱時，視資料為區塊 Blob、分頁 Blob 或 Azure 檔案而定，資料會上傳到 Azure 儲存體帳戶中的下列其中一個路徑。
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  或者，您可以在 Azure 入口網站中移至您 Azure 儲存體帳戶，並從該處瀏覽。

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. 我剛剛發現我的容器並未遵循 Azure 命名需求。 我的資料會不會無法上傳至 Azure？
A.  如果容器名稱包含大寫字母，這些名稱會自動轉換為小寫。 如果名稱在其他方面不符合規範 (特殊字元、其他語言等等)，則上傳會失敗。 如需有關命名共用、容器、檔案之最佳做法的詳細資訊，請移至：
- [命名和參考共用](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [區塊 Blob 與分頁 Blob 慣例](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)。

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>Q. 我要如何驗證複製到資料箱上的資料？
A.  在資料複製完成之後，當您執行**準備寄送**時，便會驗證資料。 在驗證過程中，資料箱會產生一份檔案清單與總和檢查碼。 您可以下載檔案清單，並針對來源資料中的檔案進行確認。 如需詳細資訊，請移至[準備寄送](data-box-deploy-picked-up.md#prepare-to-ship)。

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>Q. 在我送回資料箱之後，我的資料將會如何？
A.  當資料複製到 Azure 之後，資料箱磁碟中的資料將會根據 NIST SP 800-88 修訂 1 指導方針安全地清除。 如需詳細資訊，請移至[從資料箱清除資料](data-box-deploy-picked-up.md#erasure-of-data-from-data-box)。

## <a name="audit-report"></a>稽核報告

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Azure 資料箱服務如何協助支援客戶監管鏈程序？
A.  Azure 資料箱服務原生提供的報表可讓您用於監管鏈文件。 稽核和複製記錄會顯示於您在 Azure 中的儲存體帳戶內，且在訂單完成之後，您可以在 Azure 入口網站中[下載訂單記錄](data-box-portal-admin.md#download-order-history)。


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>哪種報表類型可提供用來支援監管鏈？
A.  下列報表類型可用來支援監管鏈：

- UPS 的運輸物流。
- 開啟電源和使用者共用存取的記錄。
- 具有64位迴圈冗余的 BOM 或資訊清單檔會檢查 (CRC-64) ，或將每個檔案成功內嵌至資料箱的總和檢查碼。
- 無法上傳到 Azure 儲存體帳戶的檔案報表。
- 在資料複製到 Azure 儲存體帳戶後，對資料箱裝置的清理 (根據 NIST 800 88R1 標準)。

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>是否提供貨運公司追蹤記錄 (來自 UPS)？ 
A.  貨運公司追蹤記錄可從資料箱訂單記錄中擷取。 當您的裝置送回到 Azure 資料中心，並清除裝置上的資料之後，就會將此報表提供給您。 若立即需要，您也可以直接移至貨運公司的網站，使用訂單追蹤號碼來取得追蹤資訊。

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>我是否可以親自將資料箱送到 Azure 資料中心？ 
A.  否。 如果您選擇 Microsoft 管理的出貨，則無法傳輸資料。 目前 Azure 資料中心不接受由客戶或是 UPS 以外的貨運公司寄送資料箱。

如果您選擇自行管理的運送，則可以從 Azure 資料中心挑選或卸載資料箱。


## <a name="next-steps"></a>後續步驟

- 檢閱[資料箱系統需求](data-box-system-requirements.md)。
- 了解[資料箱限制](data-box-limits.md)。
- 在 Azure 入口網站中快速部署 [Azure 資料箱](data-box-quickstart-portal.md)。
