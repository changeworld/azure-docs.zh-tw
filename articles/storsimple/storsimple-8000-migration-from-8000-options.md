---
title: StorSimple 8000 系列裝置的資料移轉選項
description: 概要說明從 StorSimple 8000 系列遷移資料的選項。
services: storsimple
author: twooley
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: twooley
ms.openlocfilehash: 92ab570b3e06d67d7986a8103e9622e95eb57e7e
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783904"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>從 StorSimple 8000 系列遷移資料的選項

> [!IMPORTANT]
> 2022年12月31日起，StorSimple 8000 系列將 (EOS) 狀態終止支援。 我們建議 StorSimple 8000 系列客戶遷移至檔中所述的其中一個替代方案。

StorSimple 8000 系列即將于2022年12月 [終止支援](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) 。 執行 StorSimple 8000 系列的客戶可選擇升級至其他 Azure 第一方混合式服務。 本文說明可用來移轉資料的 Azure 混合式選項。

## <a name="migration-options"></a>移轉選項

使用 StorSimple 8000 系列的客戶有 Azure 或協力廠商的選項。

### <a name="azure-options"></a>Azure 選項

#### <a name="migrate-to-azure-file-sync"></a>遷移至 Azure 檔案同步

這項全新的遷移選項可讓客戶將其組織的檔案共用儲存在 Azure 檔案儲存體中。 這些檔案共用隨後可集中管理，以便使用 Azure 檔案同步 (AFS) 進行內部部署存取。 AFS 可部署在 Windows Server 主機上。 其後，可以透過主機複本或使用移轉工具來執行實際資料移轉。

如需有關如何將資料移轉至 Azure 檔案同步的詳細資訊，請移至 [StorSimple 8100 和8600遷移以 Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)。

### <a name="third-party-options"></a>第三方選項

#### <a name="migrate-to-panzura-freedom-nas"></a>移轉至 Panzura Freedom NAS

StorSimple 5000-7000 系列和 StorSimple 8000 系列客戶可以選擇遷移至 Panzura freedom 的自由 NAS，以將其資料保留在 Azure 中。 Panzura freedom 自由解決方案提供的 NAS 解決方案可橫跨資料中心、辦公室、公用和私人雲端。 此解決方案可針對 NFS、SMB 和行動用戶端啟用本機、混合式及雲端內資料工作流程。

此移轉由 Panzura 支援，而客戶可以從向 [Panzura 網站](https://panzura.com/migrate-storsimple-panzura/)要求移轉支援開始著手。

#### <a name="migrate-to-nasuni"></a>遷移至 Nasuni

透過 Nasuni，您可以輕鬆地將整個 StorSimple 環境移至穩定、安全、高效能的檔案服務平臺。 Nasuni 可提供內部部署檔案儲存體的安全性和效能，並將其與 Azure 的擴充性和持久性結合在一起。  作為領先的 Azure 獨立軟體廠商 (ISV) ，Nasuni 提供將 StorSimple 資料移至現代化平臺所需的所有工具，可讓您在多個位置之間共用和共同作業。

立即開始使用： [Nasuni 網站](https://info.nasuni.com/storsimple8000-webinar)。

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
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

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>Q. StorSimple 8000 系列裝置何時到達服務的結尾？

A. StorSimple 8000 系列將于2022年12月 [結束支援](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) 。 終止支援表示 Microsoft 在2022年12月之後，將無法再提供這些裝置的硬體和軟體支援。 強烈建議您立即開始擬定從裝置移轉資料的計劃。

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. 我已儲存在 Azure 中的資料將會如何？  

A. 將資料移轉至較新的服務後，您即可繼續在 Azure 中使用這些資料。

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q. 我已在 StorSimple 裝置的本機儲存的資料將會如何？

A. 本機裝置上的資料可複製到較新的服務，如移轉文件所說明。

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>Q. 如果我想要保留我的 StorSimple 8000 系列設備，會發生什麼事？

A. 雖然服務或許可繼續運作，但 Microsoft 將無法再提供軟硬體支援。 為保有商務持續性，強烈建議您進行移轉。

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>Q. 有哪些選項可用來從 StorSimple 8000 系列裝置遷移資料？

A. 根據其案例，StorSimple 8000 系列使用者有下列遷移選項：

* **移轉至 Azure 檔案同步**：如果您想要切換至 Azure 原生格式，請使用此選項。 您可以使用 Azure 檔案同步來集中管理檔案共用。

* **其他選項**：您可以聯絡 Microsoft 支援服務，討論此處未列出的遷移選項。

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. 是否支援移轉至其他儲存體解決方案？

A. 是。 使用資料的主機複本移轉至其他儲存體解決方案，是可行的。

### <a name="q-is-migration-supported-by-microsoft"></a>Q. Microsoft 是否支援移轉？

A. 從8000系列遷移是一項完全支援的操作。 事實上，Microsoft 建議您在開始移轉之前，應先洽詢「支援服務」。 移轉目前是協助進行的作業。 如果您想要從 StorSimple 8000 系列裝置遷移資料，請 [洽詢 storsimple 支援](mailto:storsimp@microsoft.com)。

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>Q. 遷移至 Azure 檔案同步的定價模型為何？

A. 使用 Azure 檔案同步時，可能會收取服務的訂用帳戶費用。 客戶也需要支付持續的儲存成本。 請參閱適用于估價的 [AFS 定價]( https://azure.microsoft.com/pricing/details/storage/files/) 。

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>Q. 完成移轉需要多少時間呢？

A. 移轉資料的所需時間，取決於資料量和選取的升級選項。

## <a name="next-steps"></a>後續步驟

* [將 StorSimple 8000 系列的資料移轉至 Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
