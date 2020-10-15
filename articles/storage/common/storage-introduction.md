---
title: Azure 儲存體簡介 - Azure 中的雲端儲存體 | Microsoft Docs
description: 核心 Azure 儲存體平臺是 Microsoft 的雲端儲存體解決方案。 Azure 儲存體提供了高可用性、安全、持久、可大幅調整且具有備援的資料物件儲存體。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 337a4c1ba3e355c2ba1dd7ea6e34645112d1c7b1
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92091299"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>核心 Azure 儲存體服務簡介

Azure 儲存體平臺是適用于新式資料儲存案例的 Microsoft 雲端儲存體解決方案。 核心儲存體服務提供可高度調整的資料物件存放區、適用于 Azure 虛擬機器的磁片儲存體 (Vm) 、適用于雲端的檔案系統服務、可靠訊息的訊息存放區，以及 NoSQL 存放區。 這些服務包括：

- **持久與高可用性。** 備援功能可在發生暫時性硬體失敗時，確保您的資料安全無虞。 您也可以選擇在資料中心或地理區域間複寫資料，以便在發生地方性災難或天然災害時獲得額外保護。 以此方式複寫資料，可在發生未預期的中斷事件時保持高可用性。
- **安全。** 所有寫入 Azure 儲存體帳戶的資料都會由服務加密。 Azure 儲存體在存取您資料的人員控管上，提供更細微的控制。
- **可 伸縮。** Azure 儲存體設計為可大幅調整，以符合現今應用程式的資料儲存和效能需求。
- **管理。** Azure 會為您處理硬體維護、更新和重大問題。
- **可存取。** 您可以從世界各地透過 HTTP 或 HTTPS 存取 Azure 儲存體中的資料。 Microsoft 提供以各種語言 Azure 儲存體的用戶端程式庫，包括 .NET、JAVA、Node.js、Python、PHP、Ruby、Go 和其他語言，以及成熟的 REST API。 Azure 儲存體支援在 Azure PowerShell 或 Azure CLI 中使用指令碼。 而且在使用資料方面，Azure 入口網站和 Azure 儲存體總管提供簡易的視覺式解決方案。  

## <a name="core-storage-services"></a>核心儲存體服務

Azure 儲存體平臺包含下列資料服務：

- [Azure Blob](../blobs/storage-blobs-introduction.md)：適用於文字和二進位資料且可大幅調整的物件存放區。 也包含透過 Data Lake Storage Gen2 的大型資料分析支援。
- [Azure 檔案儲存體](../files/storage-files-introduction.md)：適用于雲端或內部部署的受控檔案共用。
- [Azure 佇列](../queues/storage-queues-introduction.md)：可在應用程式元件之間可靠傳訊的訊息存放區。
- [Azure 資料表](../tables/table-storage-overview.md)：以無結構描述方式儲存結構化資料的 NoSQL 存放區。
- [Azure 磁片](../../virtual-machines/managed-disks-overview.md)：適用于 azure vm 的區塊層級儲存體磁片區。

每個服務都會透過儲存體帳戶存取。 若要開始使用，請參閱[建立儲存體帳戶](storage-account-create.md)。

## <a name="example-scenarios"></a>範例案例

下表比較檔案、Blob、磁片、佇列和資料表，並顯示每個檔案、Blob、磁片、佇列和資料表的範例案例。

| 功能 | 描述 | 使用時機 |
|--------------|-------------|-------------|
| **Azure 檔案** |提供完全受控的雲端檔案共用，您可以從任何地方透過業界標準伺服器訊息區 (SMB) 通訊協定來存取。<br><br>您可以從 Windows、Linux 和 macOS 的雲端或內部部署環境掛接 Azure 檔案共用。 | 您想要將應用程式「隨即轉移」至已使用原生檔案系統 Api 的雲端，以在其和在 Azure 中執行的其他應用程式之間共用資料。<br/><br/>您想要取代或補充內部部署檔案伺服器或 NAS 裝置。<br><br> 您想要儲存需要從許多虛擬機器存取的開發和偵錯工具。 |
| **Azure Blob** | 允許在區塊 blob 中大規模地儲存和存取非結構化資料。<br/><br/>也支援將 [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) 用於企業巨量資料分析解決方案。 | 您想要應用程式支援串流及隨機存取案例。<br/><br/>您想要能夠從任何位置存取應用程式資料。<br/><br/>您想要在 Azure 上建置企業 Data Lake，並執行巨量資料分析。 |
| **Azure 磁碟** | 允許從連接的虛擬硬碟持續儲存和存取資料。 | 您想要「隨即轉移」使用原生檔案系統 Api 的應用程式，以讀取和寫入持續性磁片的資料。<br/><br/>您想要儲存不需要從連結磁碟的虛擬機器之外存取的資料。 |
| **Azure 佇列** | 允許應用程式元件之間的非同步訊息佇列。 | 您想要分離應用程式元件，並使用非同步訊息在兩者之間進行通訊。<br><br>如需有關何時使用佇列儲存體與服務匯流排佇列的指引，請參閱 [儲存體佇列和服務匯流排佇列-比較和對比](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)。 |
| **Azure 資料表** | 可讓您將結構化的 NoSQL 資料儲存在雲端，並提供具有無架構設計的索引鍵/屬性存放區。 | 您想要儲存具彈性的資料集，例如 web 應用程式的使用者資料、通訊錄、裝置資訊，或服務所需的其他元資料類型。 <br/><br/>如需有關何時使用資料表儲存體和 Azure Cosmos DB 資料表 API 的指引，請參閱 [使用 Azure Cosmos DB 資料表 API 和 Azure 資料表儲存體進行開發](../../cosmos-db/table-support.md)。 |

## <a name="blob-storage"></a>Blob 儲存體

Azure Blob 儲存體是 Microsoft 針對雲端推出的物件儲存體解決方案。 Blob 儲存體已針對儲存大量非結構化物件資料 (例如文字或二進位資料) 最佳化。

Blob 儲存體是適合用於：

- 直接提供映像或文件給瀏覽器。
- 儲存檔案供分散式存取。
- 串流影片和音訊。
- 儲存備份和還原、災害復原和封存資料。
- 儲存資料供內部部署或 Azure 裝載服務進行分析。

使用者可從世界各地透過 HTTP 或 HTTPS 存取 Blob 儲存體中的物件。 使用者或用戶端應用程式可以透過 URL、[Azure 儲存體 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/storage) 或 Azure 儲存體用戶端程式庫存取 Blob。 儲存體用戶端程式庫提供多種語言，包括 [.NET](/dotnet/api/overview/azure/storage)、[Java](https://docs.microsoft.com/java/api/overview/azure/storage)、[Node.js](https://azure.github.io/azure-storage-node)、[Python](https://azure-storage.readthedocs.io/)、[PHP](https://azure.github.io/azure-storage-php/) 和 [Ruby](https://azure.github.io/azure-storage-ruby)。

如需有關 Blob 儲存體的詳細資訊，請參閱 [Blob 儲存體簡介](../blobs/storage-blobs-introduction.md)。

## <a name="azure-files"></a>Azure 檔案

[Azure 檔案儲存體](../files/storage-files-introduction.md) 可讓您設定高可用性網路檔案共用，其可使用標準伺服器訊息區 (SMB) 通訊協定來存取。 這表示多個 VM 可以透過讀取和寫入權限共用相同的檔案。 您也可以使用 REST 介面或儲存體用戶端程式庫來讀取檔案。

區分 Azure 檔案服務與公司檔案共用上的檔案的方法之一，就是您可以使用指向檔案並包含共用存取簽章 (SAS) 權杖的 URL，從世界各地存取檔案。 您可以產生 SAS 權杖；SAS 權杖可允許特定一段時間內私人資產的特定存取。

檔案共用可以用於許多常見案例：

- 許多內部部署應用程式會使用檔案共用。 這項功能可讓您更輕鬆地將共用資料的應用程式移轉至 Azure。 如果您將檔案共用掛接至內部部署應用程式使用的相同磁碟機代號，則存取檔案共用的應用程式部分應會在變動最小 (如果有的話) 的情況下運作。

- 組態檔可以儲存在檔案共用上並從多個 VM 進行存取。 由群組中多個開發人員所用的工具和公用程式可以儲存於檔案共用，確保所有人都可以找到它們並使用相同的版本。

- 資源記錄、計量和損毀傾印只是三個數據範例，可寫入檔案共用並在稍後處理或分析。

如需 Azure 檔案服務的詳細資訊，請參閱 [Azure 檔案服務簡介](../files/storage-files-introduction.md)。

某些 SMB 功能並不適用於雲端。 如需詳細資訊，請參閱 [Azure 檔案服務不支援的功能](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)。

## <a name="queue-storage"></a>佇列儲存體

Azure 佇列服務用來儲存及擷取訊息。 佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬則訊息。 佇列通常用來儲存要以非同步方式處理的訊息清單。

例如，假設您希望客戶能夠上傳圖片，而且要建立每張圖片的縮圖。 您可以讓客戶在上傳圖片時等候您建立縮圖。 另外，也可以使用佇列。 當客戶完成上傳時，請將訊息寫入佇列。 然後讓 Azure Function 從佇列擷取訊息並建立縮圖。 這項處理的每個部分都可以個別調整，讓您在針對您的使用量進行微調時有更多控制權。

如需 Azure 佇列的詳細資訊，請參閱[佇列簡介](../queues/storage-queues-introduction.md)。

## <a name="table-storage"></a>表格儲存體

Azure 資料表儲存體現在屬於 Azure Cosmos DB。 若要查看 Azure 資料表儲存體文件，請參閱 [Azure 資料表儲存體概觀](../tables/table-storage-overview.md)。 除了現有的 Azure 資料表儲存體服務，有新的 Azure Cosmos DB 資料表 API 供應項目，可提供輸送量最佳化的資料表、全域發佈，以及自動次要索引。 若要深入瞭解並試用新的 premium 體驗，請參閱 [Azure Cosmos DB 資料表 API](https://aka.ms/premiumtables)。

如需資料表儲存體的詳細資訊，請參閱 [Azure 資料表儲存體概觀](../tables/table-storage-overview.md)。

## <a name="disk-storage"></a>磁碟儲存體

Azure 受控磁碟是虛擬硬碟 (VHD)。 您可以將它視為內部部署伺服器中虛擬化的實體磁碟。 Azure 受控磁片會儲存為分頁 blob，這是 Azure 中的隨機 IO 儲存物件。 我們會呼叫受控磁片「受控」，因為它是分頁 blob、blob 容器和 Azure 儲存體帳戶的抽象概念。 使用受控磁碟，您所要做的就是佈建磁碟，Azure 會負責執行剩餘的部分。

如需受控磁片的詳細資訊，請參閱 [Azure 受控磁片簡介](../../virtual-machines/managed-disks-overview.md)。

## <a name="types-of-storage-accounts"></a>儲存體帳戶類型

Azure 儲存體提供數種儲存體帳戶。 每個類型都支援不同的功能，而且都有自己的計價模式。 如需有關儲存體帳戶類型的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](storage-account-overview.md)。

## <a name="secure-access-to-storage-accounts"></a>保護對儲存體帳戶的存取

每個對 Azure 儲存體的要求都必須獲得授權。 Azure 儲存體支援下列授權方法：

- **Azure Active Directory (Azure AD blob 和佇列資料的) 整合。** Azure 儲存體透過 Azure 角色型存取控制 (Azure RBAC) ，支援對 Blob 和佇列服務的 Azure AD 進行驗證和授權。 建議使用 Azure AD 的授權，以提供更佳的安全性和易用性。 如需詳細資訊，請參閱 [使用 Azure Active Directory 授與 Azure blob 和佇列的存取權](storage-auth-aad.md)。
- **Azure AD 透過 SMB 進行 Azure 檔案儲存體的授權。** Azure 檔案儲存體透過 Azure Active Directory Domain Services (Azure AD DS) 或內部部署 Active Directory Domain Services (preview) ，支援透過 SMB (Server Message Block) 進行以身分識別為基礎的授權。 您已加入網域的 Windows Vm 可以使用 Azure AD 認證來存取 Azure 檔案共用。 如需詳細資訊，請參閱 [AZURE 檔案儲存體 SMB 存取的身分識別型驗證支援](../files/storage-files-active-directory-overview.md) 和 [規劃 Azure 檔案儲存體部署](../files/storage-files-planning.md#identity)的總覽。
- **使用共用金鑰進行授權。** Azure 儲存體 Blob、檔案、佇列和表格服務都支援具有共用金鑰的授權。 使用共用金鑰授權的用戶端會將標頭傳遞給使用儲存體帳戶存取金鑰簽署的每個要求。 如需詳細資訊，請參閱[使用共用金鑰進行授權](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)。
- **使用共用存取簽章 (SAS) 的授權。**  (SAS) 的共用存取簽章是一個字串，其中包含可附加至儲存體資源之 URI 的安全性權杖。 安全性權杖會封裝條件約束，例如許可權和存取間隔。 如需詳細資訊，請參閱 [使用共用存取簽章 (SAS) ](storage-sas-overview.md)。
- **對容器和 blob 的匿名存取。** 容器及其 blob 可能可公開使用。 當您將容器或 blob 指定為公用時，任何人都可以匿名讀取它;不需要驗證。 如需詳細資訊，請參閱 [管理對容器與 Blob 的匿名讀取權限](../blobs/storage-manage-access-to-resources.md)。

## <a name="encryption"></a>加密

有兩種基本加密可用於核心儲存體服務。 如需安全性和加密的詳細資訊，請參閱 [Azure 儲存體安全性指南](../blobs/security-recommendations.md)。

### <a name="encryption-at-rest"></a>待用加密

Azure 儲存體加密可保護您的資料安全，以符合組織的安全性和合規性承諾。 Azure 儲存體會在保存到儲存體帳戶之前自動加密所有資料，並在抓取之前將其解密。 加密、解密和金鑰管理程式對使用者而言是透明的。 客戶也可以選擇使用 Azure Key Vault 管理自己的金鑰。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體加密](storage-service-encryption.md)。

### <a name="client-side-encryption"></a>用戶端加密

Azure 儲存體的用戶端程式庫會提供方法，以便在透過網路傳送和解密回應之前，先從用戶端程式庫加密資料。 透過用戶端加密來加密的資料也會 Azure 儲存體待用加密。 如需用戶端加密的詳細資訊，請參閱 [使用 .Net 用戶端加密進行 Azure 儲存體](storage-client-side-encryption.md)。

## <a name="redundancy"></a>備援性

為了確保您的資料持久，Azure 儲存體儲存資料的多個複本。 當您設定儲存體帳戶時，您可選取備援選項。 如需詳細資訊，請參閱 [Azure 儲存體備援](/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json) \(部分機器翻譯\)。

## <a name="transfer-data-to-and-from-azure-storage"></a>將資料傳入和傳出 Azure 儲存體

您可以透過數個選項將資料移入或移出 Azure 儲存體。 應選擇哪個選項，取決於您的資料集大小和網路頻寬。 如需詳細資訊，請參閱[選擇適合資料轉送的 Azure 解決方案](storage-choose-data-transfer-solution.md)。

## <a name="pricing"></a>定價

決定如何儲存及存取資料時，您也應考慮牽涉的成本。 如需詳細資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

## <a name="storage-apis-libraries-and-tools"></a>儲存體 API、程式庫和工具

您可以使用任何可發出 HTTP/HTTPS 要求的語言來存取儲存體帳戶中的資源。 此外，核心 Azure 儲存體服務也提供數種熱門語言的程式設計程式庫。 這些程式庫可透過處理詳細資料 (例如同步和非同步叫用、進行批次作業、例外狀況管理、自動重試、運作方式等等) 來簡化使用 Azure 儲存體的許多項目。 程式庫目前適用於下列語言和平台，以及正在研發的其他語言和平台：

### <a name="azure-storage-data-api-and-library-references"></a>Azure 儲存體資料 API 和程式庫參考

- [Azure 儲存體 REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [適用于 .NET 的 Azure 儲存體用戶端程式庫](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [適用于 JAVA/Android 的 Azure 儲存體用戶端程式庫](https://docs.microsoft.com/java/api/overview/azure/storage)
- [適用于 Node.js的 Azure 儲存體用戶端程式庫 ](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [適用于 Python 的 Azure 儲存體用戶端程式庫](https://github.com/Azure/azure-storage-python)
- [適用于 PHP 的 Azure 儲存體用戶端程式庫](https://github.com/Azure/azure-storage-php)
- [適用于 Ruby 的 Azure 儲存體用戶端程式庫](https://github.com/Azure/azure-storage-ruby)
- [適用于 c + + 的 Azure 儲存體用戶端程式庫](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure 儲存體管理 API 和程式庫參考

- [儲存體資源提供者 REST API](https://docs.microsoft.com/rest/api/storagerp/)
- [適用於 .NET 的儲存體資源提供者用戶端程式庫](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [儲存體服務管理 REST API (傳統)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure 儲存體資料移動 API 和程式庫參考

- [儲存體匯入/匯出服務 REST API](https://docs.microsoft.com/rest/api/storageimportexport/)
- [適用於 .NET 的儲存體資料移動 用戶端程式庫](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>工具和公用程式

- [儲存體的 Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.storage)
- [儲存體的 Azure CLI Cmdlet](https://docs.microsoft.com/cli/azure/storage)
- [AzCopy Command-Line 公用程式](https://aka.ms/downloadazcopy)
- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。
- [適用于 Azure 儲存體的 Azure Resource Manager 範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>後續步驟

若要啟動並執行核心 Azure 儲存體服務，請參閱 [建立儲存體帳戶](storage-account-create.md)。
