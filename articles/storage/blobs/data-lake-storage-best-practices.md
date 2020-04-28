---
title: 使用 Azure Data Lake Storage Gen2 的最佳做法 | Microsoft Docs
description: 了解將 Azure Data Lake Storage Gen2 (先前稱為 Azure Data Lake Store) 用在資料擷取、資料安全性和效能方面的最佳做法
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: ac4e126c7ecbd1fc781db74e5b19635b273bbb34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "72299668"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>使用 Azure Data Lake Storage Gen2 的最佳做法

在本文中，您會了解使用 Azure Data Lake Storage Gen2 的最佳做法和考量。 本文提供 Data Lake Storage Gen2 的安全性、效能、恢復功能及監視作業等相關資訊。 尚未使用 Data Lake Storage Gen2 之前，若要在 Azure HDInsight 這類服務中處理如此龐大的資料，過程會十分繁瑣。 您必須將資料分給多個 Blob 儲存體帳戶，才能達到該規模的 PB 儲存量和最佳效能。 Data Lake Storage Gen2 支援5TB 高的個別檔案大小，而且已移除大部分的效能限制。 不過，為了讓您可取得 Data Lake Storage Gen2 的最佳效能，本文中仍包含了一些考量。

## <a name="security-considerations"></a>安全性考量

Azure Data Lake Storage Gen2 針對 Azure Active Directory (Azure AD) 使用者、群組和服務主體提供了 POSIX 存取控制。 您可以對現有檔案和目錄設定這些存取控制。 存取控制也可用來建立能自動套用至新檔案或目錄的預設權限。 如需有關 Data Lake Storage Gen2 ACL 的更多詳細資料，可在[Azure Data Lake Storage Gen2 中的存取控制](storage-data-lake-storage-access-control.md)中取得。

### <a name="use-security-groups-versus-individual-users"></a>使用安全性群組與個別使用者

在 Data Lake Storage Gen2 中使用大量資料時，服務主體很可能會用來允許服務（例如 Azure HDInsight）使用資料。 不過，也可能會有個別使用者需要存取資料的情況。 強烈建議您在所有情況中使用 Azure Active Directory [安全性群組](../common/storage-auth-aad.md)，而不是將個別使用者指派至資目錄和檔案。

指派權限給安全性群組之後，在群組中新增或移除使用者皆不需要更新 Data Lake Storage Gen2。 這也有助於確保您不會超過存取控制清單 (ACL) 的存取控制項目上限。 目前，該數字為 32 (包括四個一律與每個檔案和目錄相關聯的 POSIX 樣式 ACL)：擁有使用者、擁有群組、遮罩和其他。 每個目錄可以有兩種 ACL：存取 ACL 和預設 ACL，共 64 個存取控制項目。 如需有關這些 ACL 的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="security-for-groups"></a>群組的安全性

當您或您的使用者需要在啟用階層命名空間的情況下，存取儲存體帳戶中的資料時，最好是使用 Azure Active Directory 安全性群組。 某些建議開始使用的群組可能是容器根目錄的**ReadOnlyUsers**、 **WriteAccessUsers**和**FullAccessUsers** ，甚至是個別的金鑰子目錄。 如果預期之後可能會新增任何其他使用者的群組 (但未經過識別)，您可以考慮建立空的安全性群組，讓其可存取特定資料夾。 使用安全性群組可確保您在將新權限指派給數千個檔案時，不需要很長的處理時間。

### <a name="security-for-service-principals"></a>服務主體的安全性

Azure Databricks 這類服務通常會使用 Azure Active Directory 服務主體來存取 Data Lake Storage Gen2 中的資料。 對於許多客戶而言，單一 Azure Active Directory 服務主體可能就已足夠，而且可以在 Data Lake Storage Gen2 容器的根目錄擁有完整許可權。 但其他客戶可能需要多個叢集與不同的服務主體，一個叢集有資料的完整存取，而另一個叢集只有讀取存取權。 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>啟用 Data Lake Storage Gen2 防火牆與 Azure 服務存取權

Data Lake Storage Gen2 支援開啟防火牆，以及限制僅有 Azure 服務具有存取權，建議使用此功能來限制外部攻擊的媒介。 您可以透過**防火牆** > 的 [**啟用防火牆（開啟）** > ] [**允許存取 Azure 服務**] 選項，在 Azure 入口網站的儲存體帳戶上啟用防火牆。

若要從 Azure Databricks 存取您的儲存體帳戶，請將 Azure Databricks 部署至您的虛擬網路，然後將該虛擬網路新增至您的防火牆。 請參閱[設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

## <a name="resiliency-considerations"></a>恢復功能考量

使用 Data Lake Storage Gen2 或任何雲端服務建構系統時，您必須考量到可用性需求，以及如何回應服務可能中斷的情況。 系統可能會將問題集中到特定執行個體或甚至是整個區域；因此，務必針對這兩者進行規劃。 根據您工作負載 SLA 的復原時間目標和復原點目標，您可以針對高可用性和災害復原選擇強度較高或較低的策略。

### <a name="high-availability-and-disaster-recovery"></a>高可用性和災害復原

高可用性 (HA) 和災害復原 (DR) 有時可以結合在一起，雖然彼此有稍微不同的策略，特別是用在資料的時候。 為不受到本機硬體失敗的影響，Data Lake Storage Gen2 已在幕後執行 3 倍複寫。 此外，其他複寫選項（例如 ZRS 或切換（預覽））會改善 HA，而 GRS & RA-GRS 會改善 DR。 針對 HA 建置方案時，如果是發生服務中斷的情況，工作負載必須藉由切換至本機或新區域中各別複寫的執行個體，來盡快取得最新資料。

在 DR 策略中，為了能應付罕見的區域性嚴重失敗事件，使用 GRS 或 RA-GRS 複寫將資料複寫至不同區域也是很重要的。 您也必須考量到資料損毀這類邊緣案例的需求，您可以建立定期的快照集，以便藉此進行回復。 視資料的大小和重要性而定，您可以依據風險承受度，考慮輪流執行 1、6 和 24 小時週期的差異快照集。

針對 Data Lake Storage Gen2 的資料恢復功能，建議您透過 GRS 或 RA-GRS 對資料進行異地複寫，且需滿足您的 HA/DR 需求。 此外，您應該考量應用程式使用 Data Lake Storage Gen2 的方式，讓應用程式透過監視觸發程序或失敗嘗試的時間長度，自動容錯移轉至次要區域，或至少傳送通知給系統管理員，以手動方式介入。 請注意，您須衡量要使用容錯移轉或等候服務回到線上。

### <a name="use-distcp-for-data-movement-between-two-locations"></a>使用 Distcp 在兩個位置之間移動資料

DistCp 是分散式複製 (distributed copy) 的縮寫，是隨附於 Hadoop 的 Linux 命令列工具，可提供兩個位置之間的分散式資料移動。 兩個位置可以是 Data Lake Storage Gen2、HDFS 或 S3。 此工具會在 Hadoop 叢集 (例如，HDInsight) 上使用 MapReduce 作業，以在所有節點上相應放大。 Distcp 是公認移動巨量資料最快速的方式，且無須特殊的網路壓縮裝置。 Distcp 也可讓您選擇只更新兩個位置的差異，並可處理自動重試及計算的動態調整。 此方法用於複寫 Hive/Spark 資料表這類項目時就非常有效率，因為這些資料表的單一目錄中可能有許多大型檔案，但您只是要複製修改的資料。 基於這些理由，在巨量資料存放區之間複製資料時，最建議使用 Distcp 工具。

Apache Oozie 工作流程和 Linux Cron 作業可使用頻率或資料觸發程序來觸發複製作業。 對於需要大量複寫的工作，建議您特別為複製作業啟用個別的 HDInsight Hadoop 叢集，以便進行調整和縮放。 這可確保複製作業不會干擾重要的作業。 如果執行複寫的頻率夠頻繁，叢集甚至可能在每個作業間發生失敗。 如果容錯移轉到次要區域，請確定另一個叢集在次要區域中也是作用中的狀態，如此可在主要 Data Lake Storage Gen2 帳戶回復時，將新資料複寫回去。 如需使用 Distcp 的範例，請參閱[使用 Distcp 在 Azure 儲存體 Blob 與 Data Lake Storage Gen2 之間複製資料](../blobs/data-lake-storage-use-distcp.md)。

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>使用 Azure Data Factory 排程備份作業

Azure Data Factory 也可使用複製活動來排程備份作業，甚至可以透過複製精靈來根據頻率進行設定。 請注意，Azure Data Factory 有雲端資料移動單位 (DMU) 限制，最終會達到大型資料工作負載的輸送量/計算上限。 此外，Azure Data Factory 目前並未提供 Data Lake Storage Gen2 帳戶之間的差異更新，因此 Hive 資料表這類目錄將需要複寫整個複本。 如需有關使用 Data Factory 進行複製的詳細資訊，請參閱[資料處理站文章](../../data-factory/load-azure-data-lake-storage-gen2.md)。

## <a name="monitoring-considerations"></a>監視功能考量

Data Lake Storage Gen2 在 Azure 入口網站的 Data Lake Storage Gen2 帳戶下和 Azure 監視器中提供了計量。 Data Lake Storage Gen2 的可用性會顯示在 Azure 入口網站中。 若要取得 Data Lake Storage Gen2 帳戶最新的可用性，您必須執行您自己的綜合測試，以驗證可用性。 其他總儲存體使用率、讀取/寫入要求及輸入/輸出等計量可供監視應用程式使用，而且可在超過閾值 (例如平均延遲或每分鐘的錯誤數目) 時觸發警示。

## <a name="directory-layout-considerations"></a>目錄配置考量

將資料置入Data Lake 時，務必預先規劃資料結構，以便有效利用安全性、資料分割和處理作業。 下列建議大多適用於所有的巨量資料工作負載。 每個工作負載在資料取用方式上都有不同的需求，但以下是使用 IoT 和批次案例時常考慮使用的配置。

### <a name="iot-structure"></a>IoT 結構

在 IoT 工作負載中，可能會有大量資料要置入資料存放區，而且範圍橫跨大量產品、裝置、組織和取用者。 請務必為下游取用者預先規劃目錄配置，以利組織、保護和有效率地處理資料。 一般可考慮的範本可能會使用下列配置：

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

例如，為英國境內的飛機引擎置入遙測資料可能如以下結構所示：

    UK/Planes/BA1293/Engine1/2017/08/11/12/

將日期放在目錄結構的尾端有很重要的原因。 如果您想要針對使用者/群組鎖定特定區域或內容，那麼您可以輕鬆地透過 POSIX 權限執行此動作。 除此之外，如果必須限定某個安全性群組只能檢視 UK 資料或特定飛機，在個別權限前加入日期結構，對每小時目錄下的大量目錄來說是必要的。 另外，前面有日期結構將會使目錄數目隨著時間呈指數增加。

### <a name="batch-jobs-structure"></a>批次作業結構

簡單來說，批次處理中常用的方法是將資料置入 "in" 目錄。 然後，資料處理好後，將新的資料放入 "out" 目錄，以供下游程序使用。 此目錄結構有時會出現在需要對個別檔案進行處理的作業上，而且可能不需要大量平行處理大型資料集。 如同上述建議的 IoT 結構，好的目錄結構都使用區域和內容這類事項 (例如，組織、產品/生產者) 作為父層級目錄。 此結構有助於保護跨組織資料，以及更有效率地管理工作負載中的資料。 此外，請考慮在結構中使用日期和時間，可在處理時具有較佳的組織性、可篩選的搜尋、安全性及自動化。 日期結構的細微性層級取決於資料上傳或處理的間隔，例如每小時、每天或甚至是每個月。

有時後，檔案處理會因為資料損毀或未預期的格式而不成功。 在這種情況下，目錄結構可能就需要 **/bad** 資料夾的功用，才能將檔案移至該資料夾並進行進一步檢查。 批次作業可能也會處理這些「不良」** 檔案的報告或通知，以進行手動介入。 請參考下列的範本結構：

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

例如，一間行銷公司每天從他們位於北美洲的客戶接收消費者更新的資料擷取。 而處理前和處理後的資料可能會看起來像以下程式碼片段：

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

對於直接將批次資料處理至 Hive 或傳統 SQL 資料庫的常見案例，則不需要 **/in** 或 **/out** 資料夾，因為輸出已進入 Hive 資料表或外部資料庫的個別資料夾中。 例如，來自客戶的每日擷取會置入個別的資料夾，而 Azure Data Factory、Apache Oozie 或 Apache Airflow 這類服務的協調流程將會觸發每日 Hive 或 Spark 作業來處理資料，並寫入 Hive 資料表。
