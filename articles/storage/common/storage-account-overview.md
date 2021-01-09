---
title: 儲存體帳戶概觀
titleSuffix: Azure Storage
description: 瞭解 Azure 儲存體中不同類型的儲存體帳戶。 查看帳戶命名、效能層級、存取層、冗余、加密、端點等等。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5cf43310c68c8446b9465a39d85f84c8273a68d8
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051219"
---
# <a name="storage-account-overview"></a>儲存體帳戶概觀

Azure 儲存體帳戶包含您所有的 Azure 儲存體資料物件：Blob、檔案、佇列、資料表和磁碟。 儲存體帳戶會為您的 Azure 儲存體資料提供唯一的命名空間，此命名空間可透過 HTTP 或 HTTPS 從世界各地存取。 您 Azure 儲存體帳戶中的資料具有持久性、高度可用、安全且可大規模調整。

若要了解如何建立 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](storage-account-create.md)。

## <a name="types-of-storage-accounts"></a>儲存體帳戶類型

Azure 儲存體提供數種儲存體帳戶。 每個類型都支援不同的功能，而且都有自己的計價模式。 您在建立儲存體帳戶之前請先考量這些差異，以判斷您的應用程式最適用的帳戶類型。 儲存體帳戶的類型如下：

- **一般用途 v2 帳戶**：適用於 Blob、文件，佇列和資料表的基本儲存體帳戶類型。 在大部分情況下，建議您使用 Azure 儲存體。
- **一般用途 v1 帳戶**：適用於 Blob、文件，佇列和資料表的傳統帳戶類型。 如果可能的話，請改為使用一般用途 v2 帳戶。
- **BlockBlobStorage 帳戶**：在區塊 Blob 和附加 Blob 上具有進階效能特性的儲存體帳戶。 建議用於具有高交易率的案例，或使用較小物件或需要儲存延遲保持一樣低的案例。
- **FileStorage 帳戶**：具有進階效能特性的僅限檔案儲存體帳戶。 建議用於企業或高效能規模的應用程式。
- **BlobStorage 帳戶**：舊版僅限 Blob 的儲存體帳戶。 如果可能的話，請改為使用一般用途 v2 帳戶。

下表描述儲存體帳戶的類型、其支援的服務，以及每個帳戶類型所支援的部署模型：

| 儲存體帳戶類型 | 支援的服務 | 冗余選項 | 部署模型<sup>1</sup> |
|--|--|--|--|
| 一般用途 V2 | Blob、檔案、佇列、資料表、磁片和 Data Lake Gen2<sup>2</sup> | LRS、GRS、RA-GRS、ZRS、GZRS、RA-GZRS<sup>3</sup> | Resource Manager |
| 一般用途 V1 | Blob、檔案、佇列、資料表及磁碟 | LRS、GRS、RA-GRS | Resource Manager、傳統 |
| BlockBlobStorage | Blob (僅限區塊 Blob 和附加 Blob) | LRS，ZRS<sup>3</sup> | Resource Manager |
| FileStorage | 僅檔案 | LRS，ZRS<sup>3</sup> | Resource Manager |
| BlobStorage | Blob (僅限區塊 Blob 和附加 Blob) | LRS、GRS、RA-GRS | Resource Manager |

<sup>1</sup>建議使用 Azure Resource Manager 部署模型。 使用傳統部署模型的儲存體帳戶仍可建立於某些位置，而且會繼續支援現有的傳統帳戶。 如需詳細資訊，請參閱 [Azure Resource Manager vs. 傳統部署：了解資源的部署模型和狀態](../../azure-resource-manager/management/deployment-models.md)。

<sup>2</sup>Azure Data Lake Storage Gen2 是以 Azure Blob 儲存體為基礎的大型資料分析專用的一組功能。 只有已啟用階層命名空間的一般用途 V2 儲存體帳戶可支援 Data Lake Storage Gen2。 如需 Data Lake Storage Gen2 的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 簡介](../blobs/data-lake-storage-introduction.md)。

<sup>3</sup>區域冗余儲存體 (ZRS) 和地理區域冗余儲存體 (GZRS/RA-GZRS) 僅適用于特定區域中的標準一般用途 V2、BlockBlobStorage 和 FileStorage 帳戶。 如需 Azure 儲存體備援選項的詳細資訊，請參閱 [Azure 儲存體備援](storage-redundancy.md)。

### <a name="storage-account-redundancy"></a>儲存體帳戶的冗余

儲存體帳戶的備援選項包含：

- **本機冗余儲存體 (LRS)**：簡單、低成本的冗余策略。 資料會在主要區域中的單一實體位置內同步複製三次。
- **區域冗余儲存體 (ZRS)**：需要高可用性的案例冗余。 資料會同步複製到主要區域中的三個 Azure 可用性區域。
- **異地冗余儲存體 (GRS)**：跨區域冗余以防止區域中斷。 資料會在主要區域中同步複製三次，然後以非同步方式複製到次要區域。 如需次要區域中資料的讀取存取權，請啟用讀取存取異地備援儲存體 (RA-GRS)。
- **異地區域冗余儲存體 (GZRS)**：需要高可用性和最大耐久性的案例的冗余。 資料會同步複製到主要區域中的三個 Azure 可用性區域，然後以非同步方式複製到次要區域。 如需次要區域中資料的讀取存取權，請啟用讀取存取異地區域備援儲存體 (RA-GZRS)。

如需 Azure 儲存體中備援選項的詳細資訊，請參閱 [Azure 儲存體備援](storage-redundancy.md)。

### <a name="general-purpose-v2-accounts"></a>一般用途 v2 帳戶

一般用途 v2 儲存體帳戶能支援最新的 Azure 儲存體功能，而且包含一般用途 v1 與 Blob 儲存體帳戶的所有功能。 一般用途 v2 帳戶能針對 Azure 儲存體提供最低的每 GB 容量價格，以及極具業界競爭力的交易價格。 一般用途 v2 儲存體帳戶支援這些 Azure 儲存體服務：

- Blob (所有類型：區塊、附加、分頁)
- Data Lake Gen2
- 檔案
- 磁碟
- 佇列
- 資料表

> [!NOTE]
> Microsoft 建議，在大部分情況下使用一般用途 v2 儲存體帳戶。 您不需停機，也不必複製資料，即可輕鬆地將一般用途 v1 或 Blob 儲存體帳戶升級至一般用途 v2 帳戶。
>
> 如需有關如何升級至一般用途 v2 帳戶的詳細資訊，請參閱[升級至一般用途 v2 儲存體帳戶](storage-account-upgrade.md)。

一般用途 v2 儲存體帳戶提供多個存取層，以便根據您的使用量模式來儲存資料。 如需詳細資訊，請參閱[區塊 blob 資料的存取層](#access-tiers-for-block-blob-data)。

### <a name="general-purpose-v1-accounts"></a>一般用途 v1 帳戶

一般用途 v1 儲存體帳戶提供所有 Azure 儲存體服務的存取權，但可能沒有最新的功能或每 gb 的最低定價。 一般用途 v1 儲存體帳戶支援這些 Azure 儲存體服務：

- Blobs (所有類型)
- 檔案
- 磁碟
- 佇列
- 資料表

Microsoft 建議用於大部分案例的一般用途 v2 帳戶。 在這些案例中，您可以使用一般用途 v1 帳戶：

- 您的應用程式需要 Azure 傳統部署模型。 一般用途 v2 帳戶和 Blob 儲存體帳戶僅支援 Azure Resource Manager 部署模型。

- 您的應用程式會耗用大量交易或使用大量的異地複寫頻寬，但不需要大量的容量。 在此情況下，一般用途 v1 可能是最經濟實惠的選擇。

- 您使用的 [儲存體服務 REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 版本早于2014-02-14，或版本低於4.x 的用戶端程式庫。 您無法升級應用程式。

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage 帳戶

BlockBlobStorage 帳戶是 premium 效能層級中的特殊儲存體帳戶，可將非結構化物件資料儲存為區塊 blob 或附加 blob。 相較于一般用途 v2 和 BlobStorage 帳戶，BlockBlobStorage 帳戶可提供低、一致的延遲和更高的交易速率。

BlockBlobStorage 帳戶目前不支援經常性存取、非經常性存取層或封存存取層的分層。 這種類型的儲存體帳戶不支援分頁 blob、資料表或佇列。

### <a name="filestorage-accounts"></a>FileStorage 帳戶

FileStorage 帳戶是專門用來儲存和建立 premium 檔案共用的儲存體帳戶。 此儲存體帳戶種類支援檔案，但不支援區塊 blob、附加 blob、分頁 blob、資料表或佇列。

FileStorage 帳戶提供獨特的效能專屬特性，例如 IOPS 高載。 如需這些特性的詳細資訊，請參閱檔案規劃指南的檔案 [共用儲存層](../files/storage-files-planning.md#storage-tiers) 一節。

## <a name="naming-storage-accounts"></a>儲存體帳戶命名

為您的儲存體帳戶命名時，請記住這些規則：

- 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。
- 儲存體帳戶名稱必須在 Azure 中是獨一無二的。 任兩個儲存體帳戶名稱不得相同。

## <a name="performance-tiers"></a>效能層級

視您建立的儲存體帳戶類型而定，您可以在標準和 premium 效能層級之間進行選擇。 下表摘要說明哪些效能層級適用于哪些類型的儲存體帳戶。

| 儲存體帳戶類型 | 支援的效能層級 |
|--|--|
| 一般用途 V2 | Standard、Premium<sup>1</sup> |
| 一般用途 V1 | Standard、Premium<sup>1</sup> |
| BlockBlobStorage | 進階 |
| FileStorage | 進階 |
| BlobStorage | 標準 |

<sup>1</sup>一般用途 v2 和一般用途 v1 帳戶的 Premium 效能僅適用于磁片和分頁 blob。 區塊或附加 Blob 的進階效能僅適用於 BlockBlobStorage 帳戶。 檔案進階效能僅適用於 FileStorage 帳戶。

### <a name="general-purpose-storage-accounts"></a>一般用途的儲存體帳戶

可以針對下列任一效能層級設定一般用途儲存體帳戶：

- 標準效能層可供儲存 Blob、檔案、資料表、佇列和 Azure 虛擬機器磁碟。 如需標準儲存體帳戶的擴充性目標的詳細資訊，請參閱 [標準儲存體帳戶的擴充性目標](scalability-targets-standard-account.md)。
- 用於儲存非受控虛擬機器磁片的 premium 效能層級。 Microsoft 建議搭配 Azure 虛擬機器使用受控磁片，而不是使用非受控磁片。 如需 premium 效能層級的擴充性目標的詳細資訊，請參閱 [premium 分頁 blob 儲存體帳戶的擴充性目標](../blobs/scalability-targets-premium-page-blobs.md)。

### <a name="blockblobstorage-storage-accounts"></a>BlockBlobStorage 儲存體帳戶

BlockBlobStorage 儲存體帳戶會提供 premium 效能層級來儲存區塊 blob 和附加 blob。 如需詳細資訊，請參閱 [premium 區塊 blob 儲存體帳戶的擴充性目標](../blobs/scalability-targets-premium-block-blobs.md)。

### <a name="filestorage-storage-accounts"></a>FileStorage 儲存體帳戶

FileStorage 儲存體帳戶會為 Azure 檔案共用提供 premium 效能層級。 如需詳細資訊，請參閱 [Azure 檔案儲存體可擴縮性和效能目標](../files/storage-files-scale-targets.md)。

## <a name="access-tiers-for-block-blob-data"></a>區塊 blob 資料的存取層

Azure 儲存體提供不同的選項，以便根據使用量模式來存取區塊 blob 資料。 Azure 儲存體中的每個存取層都已針對特定的資料使用量模式最佳化。 針對您的需求選取適當的存取層，您即可以最具成本效益的方式儲存區塊 blob 資料。

可用的存取層如下：

- 經常性 **存取層** 。 這一層最適合用來經常存取儲存體帳戶中的物件。 存取經常性存取層中的資料最符合成本效益，但儲存體成本較高。 預設會在經常性存取層中建立新的儲存體帳戶。
- 非 **經常性存取層** 。 這一層最適合用於儲存不常存取且至少儲存30天的大量資料。 在非經常性存取層中儲存資料更符合成本效益，但是存取該資料可能比存取經常性存取層中的資料更為昂貴。
- 「封存」存取層。 這一層僅適用于個別的區塊 blob。 封存層最適合可容忍數小時的抓取延遲時間，而且將保留在封存層中至少180天的資料。 封存層是儲存資料最符合成本效益的選項。 不過，存取該資料比存取經常性存取層或非經常性存取層中的資料更為昂貴。

如果您的資料使用模式有變更，您可以隨時在這些存取層之間切換。 如需存取層的詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取、非經常性存取層和封存存取層](../blobs/storage-blob-storage-tiers.md)。

下表顯示每個儲存體帳戶類型中 blob 的可用存取層。

| 儲存體帳戶類型 | 支援的存取層 |
|--|--|
| 一般用途 V2 | 經常性存取、非經常性存取、封存<sup>1</sup> |
| 一般用途 V1 | N/A |
| BlockBlobStorage | N/A |
| FileStorage | N/A |
| BlobStorage | 經常性存取、非經常性存取、封存<sup>1</sup> |

<sup>1</sup> 封存儲存體和 blob 層級的階層處理只支援區塊 blob。 封存儲存層適用於於個別 Blob 的等級，不適用於儲存體帳戶層級。 如需詳細資訊，請參閱 [Azure Blob 儲存體經常性存取、非經常性存取層和](../blobs/storage-blob-storage-tiers.md)封存。

> [!IMPORTANT]
> 變更現有儲存體帳戶或 Blob 的存取層可能會導致額外的費用。 如需詳細資訊，請參閱 [儲存體帳戶計費](#storage-account-billing)。

## <a name="encryption"></a>加密

您儲存體帳戶中的所有資料都會在服務端加密。 如需加密的詳細資訊，請參閱[待用資料的 Azure 儲存體服務加密](storage-service-encryption.md)。

## <a name="storage-account-endpoints"></a>儲存體帳戶端點

儲存體帳戶會在 Azure 中為您的資料提供唯一命名空間。 每個儲存在 Azure 儲存體中的物件都有一個位址，其中包含您的唯一帳戶名稱。 帳戶名稱與 Azure 儲存體服務端點的組合會形成儲存體帳戶的端點。

下表列出每個 Azure 儲存體服務的端點。

| 儲存體服務 | 端點 |
|--|--|
| Blob 儲存體 | `https://<storage-account>.blob.core.windows.net` |
| Azure Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure 檔案 | `https://<storage-account>.file.core.windows.net` |
| 佇列儲存體 | `https://<storage-account>.queue.core.windows.net` |
| 表格儲存體 | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> 區塊 blob 和 blob 儲存體帳戶只會公開 Blob 服務端點。

藉由將物件在儲存體帳戶中的位置附加至端點，以建立用來存取儲存體帳戶中物件的 URL。 例如，blob 位址可能會有如下格式︰ http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

您也可以將儲存體帳戶設定為使用 Blob 的自訂網域。 如需詳細資訊，請參閱[為 Azure 儲存體帳戶設定自訂網域名稱](../blobs/storage-custom-domain-name.md)。  

## <a name="control-access-to-account-data"></a>控制帳戶資料的存取

根據預設，您帳戶中的資料只有帳戶擁有者 (也就是您) 可以使用。 您可以控制誰可以存取您的資料，以及他們具有哪些權限。

對您的儲存體帳戶提出的每個要求都必須經過授權。 在服務層級，要求必須包含有效的 *授權* 標頭。 具體而言，此標頭包含服務在執行要求之前驗證要求所需的所有資訊。

您可以使用下列任何一種方法，授與您儲存體帳戶中資料的存取權：

- **Azure Active Directory：** 使用 Azure Active Directory (Azure AD) 認證來驗證使用者、群組或其他身分識別，以存取 blob 和佇列資料。 如果身分識別驗證成功，Azure AD 會傳回一個權杖，以使用於對 Azure Blob 儲存體或佇列儲存體的要求授權。 如需詳細資訊，請參閱[使用 Azure Active Directory 來驗證 Azure 儲存體的存取權](storage-auth-aad.md)。
- **共用金鑰授權：** 使用儲存體帳戶存取金鑰來建構一個連接字串，以便您的應用程式在執行階段用來存取 Azure 儲存體。 連接字串中的值用來建構會傳遞至 Azure 儲存體的「授權」標頭。 如需詳細資訊，請參閱[設定 Azure 儲存體連接字串](storage-configure-connection-string.md)。
- **共用存取** 簽章： (SAS) 的共用存取簽章是一種權杖，可允許委派存取儲存體帳戶中的資源。 SAS 權杖會封裝授權要求以在 URL 上 Azure 儲存體所需的所有資訊。 當您建立 SAS 時，您可以指定 SAS 授與資源的許可權，以及許可權有效的間隔。 您可以使用 Azure AD 認證或使用共用金鑰來簽署 SAS 權杖。 如需詳細資訊，請參閱 [使用共用存取簽章將有限存取權授與 Azure 儲存體資源 (SAS) ](storage-sas-overview.md)。

> [!NOTE]
> 使用 Azure AD 認證來驗證使用者或應用程式，可提供比其他授權方法更高的安全性，也更容易使用。 雖然您可以繼續使用共用金鑰授權於應用程式，但使用 Azure AD 就不需要將帳戶存取金鑰和程式碼一起儲存。 您也可以繼續使用共用存取簽章 (SAS) 將細部存取權授與儲存體帳戶中的資源，但 Azure AD 提供類似功能，卻不必管理 SAS 權杖或擔心需要撤銷遭盜用的 SAS。
>
> Microsoft 建議您盡可能使用 Azure AD Azure 儲存體 blob 和佇列應用程式的授權。

## <a name="copying-data-into-a-storage-account"></a>將資料複製到儲存體帳戶中

Microsoft 會提供一些公用程式和程式庫，以便將從內部部署儲存裝置或第三方雲端儲存體提供者匯入您的資料。 您所使用的解決方案取決於您要傳輸的資料量。

當您從一般用途 v1 或 Blob 儲存體帳戶升級至一般用途 v2 儲存體帳戶時，您的資料會自動遷移。 Microsoft 建議使用此路徑來升級您的帳戶。 但是，如果您決定將資料從一般用途 v1 帳戶移至 Blob 儲存體帳戶，則您會使用下列所述的工具和程式庫，手動遷移資料。

### <a name="azcopy"></a>AzCopy

AzCopy 為 Windows 命令列公用程式，可以極高效能將資料複製到 Azure 儲存體，以及從 Azure 儲存體複製資料。 您可以使用 AzCopy 將資料從現有一般用途的儲存體帳戶複製到 Blob 儲存體帳戶中，或從內部部署儲存體裝置上傳資料。 如需詳細資訊，請參閱 [使用 AzCopy 傳輸資料 Command-Line 公用程式](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="data-movement-library"></a>資料移動程式庫

適用於 .NET 的 Azure 儲存體資料移動程式庫是以支援 AzCopy 的核心資料移動架構為基礎。 此程式庫是針對類似於 AzCopy 的高效能、可靠且簡單的資料傳輸作業而設計的。 您可以使用資料手機連結庫，以原生方式利用 AzCopy 功能。 如需詳細資訊，請參閱 [適用于 .net 的 Azure 儲存體資料手機連結庫](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API 或用戶端程式庫

您可以建立自訂應用程式，將您的資料從一般用途 v1 儲存體帳戶遷移至 Blob 儲存體帳戶。 使用其中一個 Azure 用戶端程式庫或 Azure 儲存體服務 REST API。 Azure 儲存體針對以下多種語言和平台提供豐富的用戶端程式庫：例如 .NET、Java、C++、Node.JS、PHP、Ruby 和 Python。 這些用戶端程式庫提供多種進階功能，例如大重試邏輯、記錄與並行上傳等等。 您也可以直接透過 REST API 開發，它可以透過提出 HTTP/HTTPS 要求的任何語言進行呼叫。

如需 Azure 儲存體 REST API 的詳細資訊，請參閱 [Azure 儲存體服務 REST API 參考](/rest/api/storageservices/)。

> [!IMPORTANT]
> 使用用戶端加密來加密的 Blob 會儲存 Blob 加密相關中繼資料。 如果您複製使用用戶端加密來加密的 blob，請確定複製作業會保留 blob 中繼資料，特別是加密相關中繼資料。 如果您複製不含加密中繼資料的 Blob，便無法再次擷取 Blob 內容。 若想進一步了解與加密有關的中繼資料，請參閱 [Azure 儲存體用戶端加密](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="storage-account-billing"></a>儲存體帳戶計費

根據您的儲存體帳戶使用量 Azure 儲存體帳單。 儲存體帳戶中的所有物件會做為群組共同計費。 儲存體成本是根據下列因素來計算：

- **「區域」** 係指您帳戶所在的地理區域。
- 「帳戶類型」係指您目前使用的儲存體帳戶類型。
- 「存取層」係指您已為一般用途 v2 或 Blob 儲存體帳戶指定的資料使用模式。
- **容量** 是指您用來儲存資料的儲存體帳戶配額量。
- 「複寫」會決定一次維護多少個資料複本，以及在哪些位置維護。
- 「交易」係指對「Azure 儲存體」進行的所有讀取和寫入作業。
- 「資料輸出」係指從 Azure 區域傳出的任何資料。 當您儲存體帳戶中的資料受不同區域中執行的應用程式存取時，您需負擔資料輸出的費用。 如需了解如何使用資源群組來將您的資料和服務聚集在相同區域以限制輸出費用，請參閱[什麼是 Azure 資源群組?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)

[Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/) 頁面提供了以帳戶類型、儲存體容量、複寫和交易為基礎的詳細價格資訊。 [資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/) 則提供了出口流量的詳細定價資訊。 您可以使用 [Azure 儲存體定價計算機](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) ，以協助消除成本。

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>後續步驟

- [建立儲存體帳戶](storage-account-create.md)
- [建立區塊 Blob 儲存體帳戶](../blobs/storage-blob-create-account-block-blob.md)
- [升級至一般用途 v2 儲存體帳戶](storage-account-upgrade.md)
- [復原已刪除的儲存體帳戶](storage-account-recover.md)