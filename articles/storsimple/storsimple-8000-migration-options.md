---
title: 來自 StorSimple 5000-7000 系列設備的資料移轉選項
description: 概略說明從 StorSimple 5000-7000 系列移轉資料的選項。
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 44a05ecb273bdf7582300c3b6a9110e2ada0994c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471817"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>從 StorSimple 5000-7000 系列移轉資料的選項 

> [!IMPORTANT]
> 2019 年 7 月 9 日，StorSimple 5000/7000 系列將到達支援 （EOS） 狀態結束。 建議 StorSimple 5000/7000 系列的客戶遷移至文件中所述的其中一個替代方案。

StorSimple 5000-7000 系列即將在 2019 年 7 月[終止支援](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series)。 執行 StorSimple 5000-7000 系列的客戶可以選擇升級至其他 Azure 第一方混合式服務。 本文說明可用來移轉資料的 Azure 混合式選項。 

## <a name="migration-options"></a>移轉選項

使用 StorSimple 5000-7000 系列的客戶有 Azure 或第三方選項。

### <a name="azure-options"></a>Azure 選項

#### <a name="upgrade-to-storsimple-8000-series"></a>升級至 StorSimple 8000 系列

升級至 StorSimple 8000 系列，並繼續使用 StorSimple 平台。  客戶若選擇此升級路徑，即必須將其 5000-7000 系列裝置取代為 8000 系列。 資料可透過移轉工具從 5000-7000 系列裝置進行移轉。 移轉順利完成後，StorSimple 8000 系列裝置會繼續將資料分層至 Azure Blob 儲存體。 

如需如何使用 StorSimple 8000 系列移轉資料的詳細資訊，請移至[將資料從 StorSimple 5000-7000 系列移轉至 8000 系列裝置](storsimple-8000-migrate-from-5000-7000.md)。

#### <a name="migrate-to-azure-file-sync"></a>遷移至 Azure 檔案同步

這個全新的移轉選項可讓客戶將其組織的檔案共用儲存在 Azure 檔案服務中。 這些檔案共用隨後可集中管理，以便使用 Azure 檔案同步 (AFS) 進行內部部署存取。 AFS 可部署在 Windows Server 主機上。 其後，可以透過主機複本或使用移轉工具來執行實際資料移轉。

如需如何將資料移轉至 Azure 檔案同步的詳細資訊，請移至[將資料從 StorSimple 5000-7000 系列移轉至 Azure 檔案同步](storsimple-5000-7000-afs-migration.md)。

### <a name="third-party-options"></a>第三方選項

#### <a name="migrate-to-panzura-freedom-nas"></a>移轉至 Panzura Freedom NAS

StorSimple 5000-7000 客戶可以選擇移轉至 Panzura Freedom NAS 以將其資料保存在 Azure。 Panzura Freedom 解決方案提供一個跨資料中心、辦公室、公用與私人雲端的 NAS 解決方案。 此解決方案可針對 NFS、SMB 和行動用戶端啟用本機、混合式及雲端內資料工作流程。 

此移轉由 Panzura 支援，而客戶可以從向 [Panzura 網站](https://panzura.com/storsimple-migration/)要求移轉支援開始著手。

#### <a name="migrate-to-cohesity"></a>遷移至 Cohesity

Cohesity 可讓您將資料從目前的 StorSimple 5000-7000 遷移至 Azure 上的 Cohesity 資料平台。 Cohesity 資料平台是一種以軟體定義的網路規模解決方案，可將檔案、備份、物件和 VM 合併到單一的雲端原生解決方案。 在移轉至資料平台後，您可以透過單一窗口，來管理和保護資料與應用程式，並將其從雲端佈建到核心位置。 在使用 Cohesity 時，請先從三個節點開始。 

瞭解有關遷移到[Cohesity 資料平臺](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html)的更多資訊。

#### <a name="migrate-to-nasuni"></a>遷移到納蘇尼

Nasuni 使 StorSimple 5000-7000 客戶能夠輕鬆遷移並將資料保存在 Azure 中。  Nasuni 是一家領先的基於 Azure 的 NAS 存儲解決方案，通過雲經濟和擴展功能為客戶提供他們對前置解決方案的期望性能和安全性。  除了高性能檔存儲外，Nasuni 和 Azure 還處理備份和 DR，同時允許您通過集中式檔存儲管理在全球共用和協作資料。 

Nasuni 擁有讓您輕鬆遷移的經驗 – 立即開始：https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>遷移到塔隆 FAST

Talon 使 StorSimple 5000-7000 客戶能夠輕鬆地繼續利用他們在 StorSimple 平臺（由無限雲資源支援的少量現場佔用空間）中所珍視的優勢，從而擁有更大的功能。  借助 Talon FAST 解決方案，客戶可以在 Azure 中遷移和保留其資料，而現在，客戶擁有更小的僅軟體現場佔用空間，並添加通用檔案鎖定、全域命名空間和多網站協作等優勢。  Talon 是領先的 Azure 生態系統解決方案，它與全球客戶合作，在不損害使用者工作流或體驗的情況下，將其本地檔案伺服器工作負載遷移到基於 Azure 的整合佔用空間中。  

詳細瞭解如何在 中https://www.talonstorage.com/alliances/microsoft-storsimple發展成為雲整合企業。


## <a name="migration---frequently-asked-questions"></a>移轉 - 常見問題集

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>Q. StorSimple 5000 和 7000 系列裝置的服務將於何時終止？ 

A. StorSimple 5000-7000 系列將在 2019 年 7 月[終止服務](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series)。 終止服務意味著在 2019 年 7 月之後，Microsoft 將無法再提供這些裝置的軟硬體支援。 強烈建議您立即開始擬定從裝置移轉資料的計劃。

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. 我已儲存在 Azure 中的資料將會如何？  

A. 將資料移轉至較新的服務後，您即可繼續在 Azure 中使用這些資料。 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q. 我已在 StorSimple 裝置的本機儲存的資料將會如何？ 

A. 本機裝置上的資料可複製到較新的服務，如移轉文件所說明。

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>Q. 如果我想要保留 StorSimple 5000/7000 系列設備，會有何後果？ 

A. 雖然服務或許可繼續運作，但 Microsoft 將無法再提供軟硬體支援。 為保有商務持續性，強烈建議您進行移轉。

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>Q. 從 StorSimple 5000-7000 系列 裝置移轉資料的選項有哪些？ 

A. StorSimple 5000-7000 系列使用者可根據其所處的情境，選擇下列移轉選項。 

 - **升級至 8000 系列**：如果您想要繼續使用 StorSimple 平台，請使用此選項。 
 - **移轉至 Azure 檔案同步**：如果您想要切換至 Azure 原生格式，請使用此選項。 您可以使用 Azure 檔案同步來集中管理檔案共用。 

您可以連絡 Microsoft 支援服務，討論此處未列出的移轉選項。

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. 是否支援移轉至其他儲存體解決方案？

A. 是。 使用資料的主機複本移轉至其他儲存體解決方案，是可行的。

### <a name="q-is-migration-supported-by-microsoft"></a>Q. Microsoft 是否支援移轉？ 

A. 從 5000 或 7000 系列移轉的作業受到完整支援。 事實上，Microsoft 建議您在開始移轉之前，應先洽詢「支援服務」。 移轉目前是協助進行的作業。 如果您想要從 StorSimple 5000-7000 系列裝置移轉資料，請[建立支援票證](storsimple-8000-contact-microsoft-support.md)。

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>Q. 這兩個移轉選項的計價模式為何？

A. 移轉的成本取決於您選擇的選項。 雖然移轉作業本身是免費的，但如果您決定要升級到 StorSimple 8000 系列，則會有硬體裝置的成本。 

同樣地，在使用 Azure 檔案同步時，將會收取該服務的訂閱費用。 在前述兩種情況下，客戶也都必須支付現行儲存體的成本。 請參考以下內容來估計價格： 
- [StorSimple 定價](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS 定價]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>Q.  完成移轉需要多少時間呢？

A. 移轉資料的所需時間，取決於資料量和選取的升級選項。 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>Q. StorSimple 8000 系列的最後支援期限為何？

A. StorSimple 8000 系列最後支援期限發佈於[此](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)。


## <a name="next-steps"></a>後續步驟
 - [將資料從 StorSimple 5000-7000 系列移轉至 8000 系列裝置](storsimple-8000-migrate-from-5000-7000.md)。
 - [將資料從 StorSimple 5000-7000 系列移轉至 Azure 檔案同步](storsimple-5000-7000-afs-migration.md)
