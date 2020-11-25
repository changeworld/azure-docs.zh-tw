---
title: 不可變的 blob 儲存體-Azure 儲存體
description: Azure 儲存體為 Blob 物件儲存體提供 WORM (一次寫入，多次讀取) 支援，可讓使用者在指定的間隔內以不可清除、不可修改的狀態儲存資料。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: acb2ebb0d7ce70c6b5963a8a6c3e392091e4bb1e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010056"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>使用不可變儲存體儲存業務關鍵 Blob 資料

Azure Blob 儲存體的固定儲存體可讓使用者以 WORM (一次寫入，多次讀取) 狀態儲存業務關鍵資料。 此狀態讓資料在使用者指定的間隔內不可清除，也不可修改。 在保留間隔的持續時間內可以建立和讀取 Blob，但無法加以修改或刪除。 固定儲存體適用于所有 Azure 區域中的一般用途 v1、一般用途 v2、BlobStorage 和 BlockBlobStorage 帳戶。

如需如何使用 Azure 入口網站、PowerShell 或 Azure CLI 來設定和清除合法保存或建立以時間為基礎的保留原則的詳細資訊，請參閱 [設定和管理 Blob 儲存體的永久性原則](storage-blob-immutability-policies-manage.md)。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="about-immutable-blob-storage"></a>關於不可變的 Blob 儲存體

固定儲存體可協助醫療保健組織、金融機構以及相關產業， &mdash; 特別是代理人轉銷商組織 &mdash; 可以安全地儲存資料。 您也可以在任何案例中運用固定的儲存體，以保護重要資料免于修改或刪除。

典型應用包括：

- **法規遵循**：Azure Blob 儲存體的固定儲存體可協助組織處理 SEC 17a-4(f)、CFTC 1.31(d)、FINRA 和其他法規。 Cohasset 技術白皮書的相關詳細資料，可透過 [Microsoft 服務信任入口網站](https://aka.ms/AzureWormStorage)下載這些法規需求。 [Azure 信任中心](https://www.microsoft.com/trustcenter/compliance/compliance-overview)包含我們的合規性認證的詳細資訊。

- **安全檔保留**： Azure Blob 儲存體的固定儲存體可確保任何使用者都無法修改或刪除資料，包括具有帳戶系統管理許可權的使用者。

- **合法保存**： Azure Blob 儲存體的固定儲存體可讓使用者在所需的持續期間內儲存訴訟或業務使用的機密資訊，直到移除保存為止。 這項功能不受限於合法的使用案例，但也可視為以事件為基礎的保存或企業鎖定，需要根據事件觸發程式或公司原則來保護資料。

不可變的儲存體支援下列功能：

- 以 **[時間為基礎的保留原則支援](#time-based-retention-policies)**：使用者可以設定原則，以在指定的間隔內儲存資料。 設定以時間為基礎的保留原則時，可以建立和讀取 blob，但無法修改或刪除 blob。 保留期限到期後，就可以刪除 blob 但不會覆寫 blob。

- **[合法保存原則支援](#legal-holds)**：若不知道保留間隔，使用者可以設定合法保存來儲存不可變的資料，直到清除合法保存為止。  設定合法保存原則之後，就可以建立和讀取 blob，但無法修改或刪除 blob。 每個合法保存都會與使用者定義的英數位元標籤相關聯 (例如，用來做為識別碼字串的 ) 案例識別碼、事件名稱等。 

- **所有 Blob 層都支援：** WORM 原則與 Azure Blob 儲存層無關，而且會套用至所有層：經常性存取層、非經常性存取層和封存存取層。 使用者可以將資料轉換到其工作負載的成本最佳化層中，同時維護資料不變性。

- **容器層級組態**：使用者可以在容器層級設定以時間為基礎的保留原則和合法保存標記。 使用簡單的容器層級設定，使用者可以建立及鎖定以時間為基礎的保留原則、延長保留間隔、設定和清除法務保存等等。 這些原則會套用到現有和新容器中的所有 Blob。

- **Audit 記錄支援**：每個容器都包含原則審核記錄。 針對鎖定的以時間為基礎的保留原則，最多可顯示7個以時間為基礎的保留命令，並包含使用者識別碼、命令類型、時間戳記及保留間隔。 針對法務保存措施，此記錄包含使用者識別碼、命令類型、時間戳記及法務保存措施標記。 此記錄檔會保留在原則的存留期內（根據 SEC 17a-4 (f) 法規指導方針）。 [Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md)會顯示所有控制平面活動的更完整記錄;啟用[Azure 資源記錄](../../azure-monitor/platform/platform-logs-overview.md)時，會保留並顯示資料平面作業。 基於法規需求或其他目的，使用者有責任持續不斷地儲存那些記錄。

## <a name="how-it-works"></a>運作方式

Azure Blob 儲存體的固定儲存體支援兩種 WORM 或固定原則：以時間為基礎的保留和法務保存措施。 在容器上套用以時間為基礎的保留原則或合法保存時，所有現有的 blob 都會在30秒內移至不可變的 WORM 狀態。 所有上傳至該受原則保護容器的新 blob 也會移至不可變的狀態。 一旦所有 blob 都處於固定狀態之後，就不會確認固定的原則，而且不允許在不可變的容器中執行任何覆寫或刪除作業。

如果容器中的任何 blob 受到合法保存或鎖定的以時間為基礎的原則，也不允許刪除容器和儲存體帳戶。 合法保存原則可防止刪除 blob、容器和儲存體帳戶。 解除鎖定和鎖定以時間為基礎的原則，可防止在指定的時間刪除 blob。 只有在容器中至少有一個 blob 時，已解除鎖定和鎖定的時間型原則才能防止容器刪除。 只有具有 *鎖定* 時間型原則的容器可以防止刪除儲存體帳戶;具有解除鎖定時間型原則的容器不會提供儲存體帳戶刪除保護和合規性。

如需有關如何設定和鎖定以時間為基礎的保留原則的詳細資訊，請參閱 [設定和管理 Blob 儲存體的永久性原則](storage-blob-immutability-policies-manage.md)。

## <a name="time-based-retention-policies"></a>以時間為基礎的保留原則

> [!IMPORTANT]
> 必須 *鎖定* 以時間為基礎的保留原則，讓 blob 處於符合規範的不可變 (寫入和刪除受保護的) 狀態，適用于 SEC 17a-4 (f) 和其他法規合規性。 建議您在合理的時間內（通常小於24小時）鎖定原則。 套用以時間為基礎的保留原則的初始狀態為 *解除鎖定*，可讓您測試功能，並對原則進行變更，然後再進行鎖定。 雖然 *解除鎖定* 狀態提供了永久性保護，但不建議針對短期功能試用以外的任何目的使用已 *解除鎖定* 的狀態。 

在容器上套用以時間為基礎的保留原則時，容器中的所有 blob 在 *有效* 保留期間內都會保持固定狀態。 Blob 的有效保留期限等於 blob **建立時間** 與使用者指定保留間隔之間的差異。 因為使用者可以延長保留間隔，所以固定儲存體會使用使用者所指定保留間隔的最新值，計算有效的保留期間。

例如，假設使用者建立以時間為基礎的保留原則，其保留間隔為五年。 該容器中的現有 blob _testblob1)_ 是在一年前建立的，因此， _testblob1)_ 的有效保留期限是四年。 當新的 blob （ _testblob2_）上傳至容器時， _testblob2_ 的有效保留期限是自其建立時間起的五年。

建議僅針對功能測試使用解除鎖定的以時間為基礎的保留原則，而且必須鎖定原則，才能符合 SEC 17a-4 (f) 和其他法規合規性。 一旦鎖定以時間為基礎的保留原則之後，就無法移除原則，而且允許的有效保留期限最多可有五個增加。

下列限制適用于保留原則：

- 若為儲存體帳戶，具有鎖定之以時間為基礎的不可變原則的容器數目上限為10000。
- 最小保留間隔為1天。 最大值為146000天 (400 年) 。
- 針對容器，針對鎖定之以時間為基礎的不可變原則，延長保留間隔的最大編輯數目為5。
- 針對容器，鎖定的原則會保留最多七個以時間為基礎的保留原則審核記錄。

### <a name="allow-protected-append-blobs-writes"></a>允許受保護的附加 blob 寫入

附加 blob 是由資料區塊所組成，並且針對審核和記錄案例所需的資料附加作業優化。 根據設計，附加 blob 只允許將新區塊新增至 blob 的結尾。 不論是否有永久性、修改或刪除附加 blob 中的現有區塊，基本上都不允許。 若要深入瞭解附加 blob，請參閱 [關於附加 blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)。

只有以時間為基礎的保留原則有一個 `allowProtectedAppendWrites` 設定，可讓您將新的區塊寫入至附加 blob，同時保有永久性保護和合規性。 如果啟用此設定，您可以直接在受原則保護的容器中建立附加 blob，並繼續使用 *AppendBlock* API 將新的資料區塊新增至現有附加 blob 的結尾。 只有新的區塊可以加入，而且無法修改或刪除任何現有的區塊。 仍會套用時間保留的永久性保護，以防止刪除附加 blob，直到有效的保留期限經過為止。 啟用此設定並不會影響區塊 blob 或分頁 blob 的永久性行為。

由於此設定是以時間為基礎的保留原則的一部分，因此在 *有效* 保留期間內，附加 blob 仍會保持固定狀態。 由於新的資料可以附加至附加 blob 的初始建立之外，因此決定保留期限的方式會有些許差異。 有效保留是附加 blob **上次修改時間** 與使用者指定保留間隔之間的差異。 同樣地，當保留間隔延長時，固定儲存體會使用使用者指定的保留間隔的最新值來計算有效的保留期限。

例如，假設使用者建立以時間為基礎的保留原則， `allowProtectedAppendWrites` 並已啟用且保留間隔為90天。 目前在容器中建立了附加 blob _logblob1_，接下來的10天內會繼續將新的記錄新增至附加 blob;因此， _logblob1_ 的有效保留期限是從今天起算的100天， (上次附加 + 90 天的時間) 。

解除鎖定以時間為基礎的保留原則可讓您隨時 `allowProtectedAppendWrites` 啟用和停用設定。 鎖定以時間為基礎的保留原則之後，就 `allowProtectedAppendWrites` 無法變更此設定。

合法保存原則無法啟用 `allowProtectedAppendWrites` ，而且任何合法保存都會使失效 ' allowProtectedAppendWrites ' 屬性。 如果合法保存套用至已啟用的以時間為基礎的保留原則 `allowProtectedAppendWrites` ，則 *AppendBlock* API 將會失敗，直到解除合法保存為止。

## <a name="legal-holds"></a>法務保存

合法保存是暫時保存，可用於法律調查用途或一般保護原則。 每個合法保存原則都必須與一或多個標記相關聯。 標記會用來作為命名識別碼，例如案例識別碼或事件，以分類及描述保存的用途。

一個容器可以同時具有合法保存和以時間為基礎的保留原則。 該容器中的所有 Blob 都會保持固定狀態，直到所有法務保存措施都清除為止 (即使其有效保留期間已過期)。 相反地，Blob 會保持固定狀態，直到有效保留週期到期為止 (即使已清除所有法務保存措施)。

下列限制適用于合法保存：

- 若為儲存體帳戶，具有合法保存設定的容器數目上限為10000。
- 若為容器，合法保存標記數目上限為 10。
- 合法保存標記的最小長度為三個英數位元。 最大長度為23個英數位元。
- 針對容器，原則的持續時間最多會保留10個合法保存原則審核記錄。

## <a name="scenarios"></a>案例

下表顯示針對不同的不可變案例停用的 Blob 儲存體作業類型。 如需詳細資訊，請參閱 [Azure Blob 服務 REST API](/rest/api/storageservices/blob-service-rest-api) 檔。

| 狀況 | Blob 狀態 | Blob 作業拒絕 | 容器與帳戶保護 |
|--|--|--|--|
| Blob 上的有效保留間隔尚未過期及/或已設定合法保存 | 固定：防刪與防寫保護 | Put Blob<sup>1</sup>、put block<sup>1</sup>、put Block List<sup>1</sup>、Delete Container、delete Blob、Set Blob Metadata、Put Page、set Blob Properties、Snapshot Blob、增量複製 blob、Append Block<sup>2</sup> | 已拒絕刪除容器;拒絕刪除儲存體帳戶 |
| Blob 的有效保留間隔已過期，且未設定合法保存 | 僅限防寫保護 (允許刪除作業) | Put Blob<sup>1</sup>、put block<sup>1</sup>、put Block List<sup>1</sup>、set Blob Metadata、Put 頁面、設定 blob 屬性、快照集 Blob、累加複製 Blob、附加區塊<sup>2</sup> | 如果至少有1個 blob 存在於受保護的容器中，則會拒絕刪除容器;只有 *鎖定* 的以時間為基礎的原則，才會拒絕刪除儲存體帳戶 |
| 未套用任何 WORM 原則 (沒有以時間為基礎的保留，且沒有合法的保留標記)  | 可變動 | None | None |

<sup>1</sup> blob 服務可讓這些作業建立新的 blob 一次。 在不可變的容器中，不允許在現有 blob 路徑上進行所有後續的覆寫作業。

只有啟用屬性的以時間為基礎的保留原則時，才允許<sup>2</sup>個附加區塊 `allowProtectedAppendWrites` 。 如需詳細資訊，請參閱「 [允許受保護的附加 Blob 寫入](#allow-protected-append-blobs-writes) 」一節。

> [!IMPORTANT]
> 某些工作負載（例如 [SQL 備份至 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)）會建立 blob，然後將其新增至其中。 如果容器具有以時間為基礎的有效保留原則或合法保存，此模式將不會成功。

## <a name="pricing"></a>定價

使用這項功能不需額外付費。 固定資料的定價方式與可變數據相同。 如需 Azure Blob 儲存體的定價詳細資料，請參閱 [Azure 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="faq"></a>常見問題集

**您可以提供 WORM 合規性的檔嗎？**

是。 為了記載合規性，Microsoft 保留了領先的獨立評估公司，專門針對記錄管理和資訊治理（Cohasset 相關），以評估不可變的 Blob 儲存體，並符合金融服務產業的特定需求。 Cohasset 驗證了不可變的 Blob 儲存體（當用來以 WORM 狀態保留以時間為基礎的 Blob）時，符合 CFTC 規則 1.31 (c)  (d) 、FINRA Rule 4511 和 SEC Rule 17a-4 的相關儲存需求。 Microsoft 已將這組規則設為目標，因為它們代表金融機構記錄保留範圍內的最規範指導方針。 您可以從 [Microsoft 服務信任中心](https://aka.ms/AzureWormStorage)取得 Cohasset 報告。 若要向 Microsoft 要求有關 WORM 永久性合規性的信件證明，請聯絡 Azure 支援。

**這項功能只適用于區塊 blob 和附加 blob，也適用于分頁 blob 嗎？**

固定儲存體可以搭配任何 blob 類型使用，因為它是在容器層級設定的，但我們建議您針對主要儲存區塊 blob 和附加 blob 的容器使用 WORM。 容器中現有的 blob 將受到新設定的 WORM 原則所保護。 但是，您必須在 WORM 容器外部建立任何新的分頁 blob，然後將其複製到中。 一旦複製到 WORM 容器後，就不允許對分頁 blob 進行進一步的變更。 建議您在將 VM 磁片儲存 (頁面 blob) 的容器上設定 WORM 原則，因為它會鎖定 VM 磁片。 建議您在鎖定任何以時間為基礎的原則之前，先徹底查看檔並測試您的案例。

**我需要建立新的儲存體帳戶才能使用此功能嗎？**

否，您可以搭配任何現有或新建立的一般用途 v1、一般用途 v2、BlobStorage 或 BlockBlobStorage 帳戶來使用不可變的儲存體。 支援一般用途 v1 儲存體帳戶，但建議您升級至一般用途 v2，讓您可以利用更多功能。 如需升級現有一般用途 v1 儲存體帳戶的相關資訊，請參閱 [升級儲存體帳戶](../common/storage-account-upgrade.md)。

**我可以同時套用合法保存和以時間為基礎的保留原則嗎？**

是，容器可以同時具有合法保存和以時間為基礎的保留原則;不過，在清除合法保存之前，將不會套用 ' allowProtectedAppendWrites ' 設定。 該容器中的所有 Blob 都會保持固定狀態，直到所有法務保存措施都清除為止 (即使其有效保留期間已過期)。 相反地，Blob 會保持固定狀態，直到有效保留週期到期為止 (即使已清除所有法務保存措施)。 

**合法保存原則是否僅適用于法律記錄，或是否有其他使用案例？**

否，合法保存只是用於非以時間為基礎的保留原則的一般條款。 它不只需要用於訴訟相關的訴訟。 合法保存原則適用于停用覆寫和刪除，以保護重要的企業蠕蟲資料，其中保留期限不明。 您可以使用它作為企業原則來保護您的任務關鍵性 WORM 工作負載，或在自訂事件觸發程式需要使用以時間為基礎的保留原則之前，將其作為預備原則。 

**可以移除 _鎖定_ 的以時間為基礎的保留原則或合法保存嗎？**

只有解除鎖定的以時間為基礎的保留原則可以從容器中移除。 一旦鎖定以時間為基礎的保留原則，就無法移除;只允許有效的保留期限延伸。 可以刪除合法保存標記。 刪除所有合法標記時，會移除合法保存。

**如果我嘗試刪除具有以時間為基礎的保留原則或合法保存的容器，會發生什麼事？**

如果容器內至少有一個 blob 具有鎖定或解除鎖定的以時間為基礎的保留原則，或容器有合法保存，則刪除容器作業會失敗。 只有當容器內沒有任何 blob，而且沒有合法保存時，刪除容器作業才會成功。 

**如果我嘗試刪除具有以時間為基礎的保留原則或合法保存之容器的儲存體帳戶，會發生什麼事？**

如果至少有一個容器具有合法保存集或 **鎖定** 以時間為基礎的原則，則儲存體帳戶刪除將會失敗。 具有解除鎖定之以時間為基礎之原則的容器無法防止刪除儲存體帳戶。 您必須先移除所有合法保存並刪除所有已 **鎖定** 的容器，才能刪除儲存體帳戶。 如需有關容器刪除的資訊，請參閱前面的問題。 您也可以使用 [Azure Resource Manager 鎖定](../../azure-resource-manager/management/lock-resources.md)，對儲存體帳戶套用進一步的刪除保護。

**當 blob 處於固定狀態時，我是否可以將資料移至不同的 blob 層 (經常性存取、非經常性存取、封存) ？**

是，您可以使用「設定 Blob 層」命令，在 Blob 層之間移動資料，同時將資料保持在相容的不可變狀態。 Blob 經常性存取層、非經常性存取層和封存存取層都支援固定儲存體。

**如果我付款失敗，而保留間隔尚未過期，則會發生什麼事？**

如果未付款，則會依照您與 Microsoft 簽訂的合約條款與條件規定，套用一般資料保留原則。 如需一般資訊，請參閱 [Microsoft 的資料管理](https://www.microsoft.com/en-us/trust-center/privacy/data-management)。 

**您是否提供試用此功能的試用版或寬限期？**

是。 當您第一次建立以時間為基礎的保留原則時，它會處於已 *解除鎖定* 的狀態。 在此狀態中，您可以對保留間隔進行任何所需的變更，例如增加或減少保留間隔，甚至刪除原則。 鎖定原則之後，會保持鎖定狀態，直到保留間隔到期為止。 此鎖定的原則可防止刪除和修改保留間隔。 我們強烈建議僅將「未鎖定」狀態使用於試用目的，並且在 24 小時期間內鎖定原則。 這些做法可協助您符合 SEC 17a-4(f) 和其他法規。

**我可以搭配不可變的 blob 原則使用虛刪除嗎？**

是，如果您的合規性需求允許啟用虛刪除。 [Azure Blob 儲存體](./soft-delete-blob-overview.md) 的虛刪除適用于儲存體帳戶內的所有容器，而不論合法保存或以時間為基礎的保留原則。 建議您先啟用虛刪除以進行額外保護，再套用並確認任何不可變的蠕蟲原則。

## <a name="next-steps"></a>後續步驟

- [設定和管理 Blob 儲存體的不變性原則](storage-blob-immutability-policies-manage.md)
- [設定規則以使用生命週期管理自動分層和刪除 blob 資料](storage-lifecycle-management-concepts.md)
- [Azure 儲存體 Blob 的虛刪除](./soft-delete-blob-overview.md)
- [使用 Azure Resource Manager 鎖定來保護訂用帳戶、資源群組和資源](../../azure-resource-manager/management/lock-resources.md)。