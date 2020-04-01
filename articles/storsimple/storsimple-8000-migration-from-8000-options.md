---
title: 從 StorSimple 8000 系列裝置的資料移轉選項
description: 概述了從 StorSimple 8000 系列遷移數據的選項。
services: storsimple
author: priestlg
ms.service: storsimple
ms.topic: article
ms.date: 03/25/2020
ms.author: v-grpr
ms.openlocfilehash: f712486c8035bbc913fbd229759f3415a1005449
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438316"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>從 StorSimple 8000 系列移轉資料的選項

> [!IMPORTANT]
> 2022 年 12 月 31 日,StorSimple 8000 系列將到達支援 (EOS) 狀態結束。 我們建議 StorSimple 8000 系列客戶遷移到文檔中描述的備選方案之一。

StorSimple 8000 系列在 2022 年 12 月即將[結束支援](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)。 運行 StorSimple 8000 系列的客戶可以選擇升級到其他 Azure 第一方混合服務。 本文說明可用來移轉資料的 Azure 混合式選項。

## <a name="migration-options"></a>移轉選項

使用 StorSimple 8000 系列的客戶具有 Azure 或第三方選項。

### <a name="azure-options"></a>Azure 選項

#### <a name="migrate-to-azure-file-sync"></a>遷移至 Azure 檔案同步

此全新的遷移選項使客戶能夠將組織的檔案共用存儲在 Azure 檔案中。 這些檔案共用隨後可集中管理，以便使用 Azure 檔案同步 (AFS) 進行內部部署存取。 AFS 可部署在 Windows Server 主機上。 其後，可以透過主機複本或使用移轉工具來執行實際資料移轉。

有關如何將資料移到 Azure 檔同步的詳細資訊,請造[訪 StorSimple 8100 和 8600 移至 Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)。

<!-- 03/25/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
### Third-party options

#### Migrate to Panzura Freedom NAS

StorSimple 5000-7000 customers can choose to migrate to Panzura Freedom NAS to keep their data in Azure. Panzura Freedom solution provides a NAS solution that spans datacenters, offices, public and private clouds. The solution enables local, hybrid, and in-cloud data workflows for NFS, SMB, and mobile clients. 

This migration is supported by Panzura and customers can get started by requesting migration support from the [Panzura website](https://panzura.com/storsimple-migration/).

#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>移轉 - 常見問題集

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>Q. StorSimple 8000 系列設備何時達到服務結束?

A. StorSimple 8000 系列在 2022 年 12 月達到[支援結束](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)。 支援的結束意味著 Microsoft 在 2022 年 12 月之後將不再能夠為這些設備的硬體和軟體提供支援。 強烈建議您立即開始擬定從裝置移轉資料的計劃。

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. 我已儲存在 Azure 中的資料將會如何？  

A. 將資料移轉至較新的服務後，您即可繼續在 Azure 中使用這些資料。

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q. 我已在 StorSimple 裝置的本機儲存的資料將會如何？

A. 本機裝置上的資料可複製到較新的服務，如移轉文件所說明。

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>Q. 如果我想保留 StorSimple 8000 系列設備,會發生什麼情況?

A. 雖然服務或許可繼續運作，但 Microsoft 將無法再提供軟硬體支援。 為保有商務持續性，強烈建議您進行移轉。

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>Q. 有哪些選項可用於從 StorSimple 8000 系列設備遷移數據?

A. 根據其方案,StorSimple 8000 系列使用者具有以下遷移選項:

* **移轉至 Azure 檔案同步**：如果您想要切換至 Azure 原生格式，請使用此選項。 您可以使用 Azure 檔案同步來集中管理檔案共用。

* **其他選項**:您可以聯繫 Microsoft 支援部門,討論此處未列出的遷移選項。

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. 是否支援移轉至其他儲存體解決方案？

A. 是。 使用資料的主機複本移轉至其他儲存體解決方案，是可行的。

### <a name="q-is-migration-supported-by-microsoft"></a>Q. Microsoft 是否支援移轉？

A. 從 8000 系列遷移是完全支援的操作。 事實上，Microsoft 建議您在開始移轉之前，應先洽詢「支援服務」。 移轉目前是協助進行的作業。 如果要從 StorSimple 8000 系列裝置移轉資料,[請與 StorSimple 支援 。](mailto:storsimp@microsoft.com)

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>Q. 遷移到 Azure 檔同步的定價模型是什麼?

A. 使用 Azure 檔同步時,可能會收取服務的訂閱費用。 客戶還必須支付持續的存儲成本。 有關估計,請參閱[AFS 定價]( https://azure.microsoft.com/pricing/details/storage/files/)。

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>Q. 完成移轉需要多少時間呢？

A. 移轉資料的所需時間，取決於資料量和選取的升級選項。

## <a name="next-steps"></a>後續步驟

* [將資料從 StorSimple 8000 系列移轉到 Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
