---
title: Microsoft Azure 資料靜態加密 | Microsoft Docs
description: 本文提供 Microsoft Azure 資料靜態加密概觀、整體功能及一般考量。
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 337deec076a25183a4dd866362c7dc55a485834d
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224487"
---
# <a name="azure-data-encryption-at-rest"></a>待用 Azure 資料加密

Microsoft Azure 包含根據貴公司的安全性和合規性需求來保護資料的工具。 本文著重於：

- 在整個 Microsoft Azure 中保護資料的方式
- 討論參與資料保護實作的各種元件，
- 檢閱不同金鑰管理保護方法的優缺點。

靜態加密是常見的安全性需求。 在 Azure 中，組織可以在沒有自訂金鑰管理解決方案所帶來之風險或成本的情況下，對待用資料進行加密。 組織可以選擇讓 Azure 完全管理靜態加密。 此外，組織擁有各種選項能密切管理加密或加密金鑰。

## <a name="what-is-encryption-at-rest"></a>什麼是靜態加密？

待用加密就是將存留的資料進行編碼 (加密)。 Azure 中的靜態加密設計是使用對稱加密，根據簡單概念模型，快速將大量資料加密及解密：

- 對稱式加密金鑰可用來在寫入儲存體時將資料加密。
- 當資料準備好在記憶體中使用時，相同的加密金鑰可用來將資料解密。
- 資料可能會進行分割，每個分割區可能會使用不同的金鑰。
- 金鑰必須儲存在具備以身分識別為基礎的存取控制和稽核原則的安全位置。 資料加密金鑰通常會使用 Azure Key Vault 中的金鑰加密金鑰進行加密，以進一步限制存取權。

在實務上，金鑰管理和控制情節，以及級別和可用性保證，都需要其他建構。 以下將說明 Microsoft Azure 靜態加密的概念和元件。

## <a name="the-purpose-of-encryption-at-rest"></a>靜態加密的目的

待用加密可為儲存的資料 (待用) 提供資料保護。 對靜態資料的攻擊包含嘗試取得儲存資料的硬體之實體存取，並洩露內含的資料。 在這類攻擊中，伺服器的硬碟可能已在維護期間 mishandled，讓攻擊者可以移除硬碟。 稍後，攻擊者會將硬碟放入受他們控制下的電腦，以嘗試存取資料。

靜態加密旨在防止攻擊者存取未加密的資料，方法是確保資料在磁碟上時就已加密。 如果攻擊者取得具有已加密資料的硬碟，但是沒有加密金鑰，則該攻擊者必須將加密破解才能讀取資料。 與存取硬碟上未加密的資料相比，此攻擊將會變得更為複雜，且需要耗用更多資源。 基於這個理由，強烈建議您使用靜態加密，且對許多組織而言是高優先順序的需求。

組織需要資料管理和合規性工作的需求，也可能需要待用加密。 產業和政府規定 (例如 HIPAA、PCI 和 FedRAMP 等) 就資料保護和加密需求制定了具體的保護措施。 待用加密是符合這其中部分規範所需的必要手段。 如需 Microsoft 的 FIPS 140-2 驗證方法的詳細資訊，請參閱 [聯邦資訊處理標準 (fips) 發行集 140-2](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2)。

除了能滿足合規性和法規要求之外，靜態加密也能提供深度防禦的保護。 Microsoft Azure 針對服務、應用程式及資料提供符合規範的平台。 它也提供全方位的設施和實體安全性、資料存取控制及稽核。 不過，請務必提供其他「重迭」安全性措施，以防其中一個其他安全性措施失敗，待用加密則提供這樣的安全性措施。

Microsoft 致力於為所有雲端服務提供靜態加密選項，並給予客戶對加密金鑰和金鑰使用記錄的控制。 此外，Microsoft 也正朝著預設將所有客戶的待用資料加密的方向努力。

## <a name="azure-encryption-at-rest-components"></a>Azure 靜態加密元件

如先前所述，靜態加密的目的，是使用祕密加密金鑰將保存在磁碟上的資料進行加密。 若要達到這個目標，就必須提供安全的金鑰建立、儲存、存取控制，以及加密金鑰管理。 儘管細節可能有所差異，但 Azure 服務「待用加密」實作可以下圖所示的術語來加以說明。

![單元](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure 金鑰保存庫

加密金鑰的儲存位置以及這些金鑰的存取控制是靜態加密模型的核心。 金鑰必須是高度安全的，但需可由指定使用者進行管理，且可用於特定服務。 針對 Azure 服務，Azure Key Vault 是建議的金鑰儲存體解決方案，並提供常見的跨服務管理體驗。 金鑰是儲存在金鑰保存庫並加以管理，可以將存取金鑰保存庫提供給使用者或服務。 Azure Key Vault 支援客戶建立金鑰或匯入客戶金鑰，可供在客戶管理的加密金鑰情節下使用。

### <a name="azure-active-directory"></a>Azure Active Directory

使用 Azure Key Vault 中儲存的金鑰加以管理或存取，從而進行靜態加密及靜態解密的權限，可以提供給 Azure Active Directory 帳戶。

### <a name="key-hierarchy"></a>金鑰階層

在靜態加密實作中，會使用一個以上的加密金鑰。 將加密金鑰儲存在 Azure Key Vault 可確保金鑰的安全金鑰存取和集中管理。 不過，對於大量加密和解密而言，服務本機存取加密金鑰比與每個資料作業的 Key Vault 互動更有效率，因此可提供更強的加密和更好的效能。 限制使用單一加密金鑰會降低金鑰洩露的風險，以及在必須更換金鑰時的重新加密成本。 Azure 加密待用模型會使用由下列類型的金鑰組成的金鑰階層，以解決所有這些需求：

- **資料加密金鑰 (DEK)** – 用於將分割區或資料區塊加密的對稱 AES256 金鑰。  單一資源可能會有多個分割區和多個資料加密金鑰。 使用不同金鑰將每個資料區塊進行加密，會使密碼編譯分析攻擊更加困難。 資源提供者或要將特定區塊加密和解密的應用程式執行個體都需要存取 DEK。 當新的金鑰取代 DEK 時，只有在相關聯區塊中的資料才需要使用新的金鑰重新加密。
- **金鑰加密金鑰 (KEK) ** –用來加密資料加密金鑰的加密金鑰。 使用永遠不會離開 Key Vault 的金鑰加密金鑰，可讓資料加密金鑰本身進行加密和控制。 可存取 KEK 的實體可能不同於需要 DEK 的實體。 實體可以代理存取 DEK，以限制每個 DEK 只能由特定分割區存取。 因為需要 KEK 才能將 DEK 解密，KEK 實際上就是單一點，藉以透過刪除 KEK 來有效地刪除 DEK。

以金鑰加密金鑰加密的資料加密金鑰會分開儲存，而且只有具備金鑰加密金鑰存取權的實體，才可以解密這些資料加密金鑰。 支援不同模型的金鑰儲存。 如需詳細資訊，請參閱 [資料加密模型](encryption-models.md) 。

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Microsoft 雲端服務中的靜態加密

這三種雲端模型中全都是使用 Microsoft 雲端服務：IaaS、PaaS、SaaS。 下列範例說明它們如何符合每個模型：

- 軟體服務是指「軟體即服務」或 SaaS，具有雲端所提供的應用程式，例如 Office 365。
- 平台服務是指客戶在其應用程式中利用雲端，會針對諸如儲存、分析和服務匯流排功能等項目使用雲端。
- 基礎結構服務或基礎結構即服務 (IaaS) 可供客戶在其中部署裝載於雲端中且可能運用其他雲端服務的作業系統和應用程式。

### <a name="encryption-at-rest-for-saas-customers"></a>SaaS 客戶的靜態加密

軟體即服務 (SaaS) 客戶通常會啟用或可在每個服務中使用靜態加密。 Office 365 有幾個選項可供客戶確認或啟用靜態加密。 如需 Office 365 服務的相關資訊，請參閱 [Office 365 中的加密](https://docs.microsoft.com/office365/securitycompliance/encryption) \(機器翻譯\)。

### <a name="encryption-at-rest-for-paas-customers"></a>PaaS 客戶的靜態加密

平臺即服務 (PaaS) 客戶的資料通常位於儲存體服務（例如 Blob 儲存體）中，但也可以快取或儲存在應用程式執行環境中，例如虛擬機器。 若要查看您可使用的靜態加密選項，請檢查下表以查看您所使用的儲存體和應用程式平台。

### <a name="encryption-at-rest-for-iaas-customers"></a>IaaS 客戶的靜態加密

基礎結構即服務 (IaaS) 客戶可以有各種不同的使用中服務和應用程式。 IaaS 服務可以在其 Azure 裝載的虛擬機器和使用 Azure 磁碟加密的 VHD 中啟用靜態加密。

#### <a name="encrypted-storage"></a>加密的儲存體

諸如 PaaS、IaaS 等解決方案可以運用儲存資料靜態加密的其他 Azure 服務。 在這些情況下，您可以啟用每個已使用 Azure 服務所提供的靜態加密支援。 下表列舉出主要儲存體、服務和應用程式平台，以及支援的待用加密模型。

#### <a name="encrypted-compute"></a>加密的計算

所有受控磁碟、快照集和映射都會使用以服務管理的金鑰儲存體服務加密來加密。 更完整的待用加密解決方案可確保資料永遠不會以未加密的形式保存。 在處理虛擬機器上的資料時，資料可以保存到 Windows 分頁檔案或 Linux 交換檔案、損毀傾印或應用程式記錄檔。 若要確保此資料會進行靜態加密，IaaS 應用程式可以在 Azure IaaS 虛擬機器 (Windows 或 Linux) 和虛擬磁碟上使用 Azure 磁碟加密。

#### <a name="custom-encryption-at-rest"></a>自訂靜態加密

建議您盡可能讓 IaaS 應用程式運用任何已使用 Azure 服務所提供的 Azure 磁碟加密及靜態加密選項。 在某些情況下，例如異常加密需求或非以 Azure 為基礎的儲存體，IaaS 應用程式的開發人員可能需要自行實作靜態加密。 IaaS 解決方案的開發人員可以運用某些 Azure 元件，更妥善與 Azure 管理和客戶期望進行整合。 具體來說，開發人員應該使用 Azure Key Vault 服務來提供安全的金鑰儲存體，並為客戶提供一致的金鑰管理選項，以及大部分 Azure 平台服務的選項。 此外，自訂的解決方案應該使用 Azure 受控服務識別，讓服務帳戶可存取加密金鑰。 如需關於 Azure Key Vault 和受控服務識別的開發人員資訊，請參閱其個別的 SDK。

## <a name="azure-resource-providers-encryption-model-support"></a>Azure 資源提供者加密模型支援

每個 Microsoft Azure 服務都支援一或多個靜態加密模型。 不過，針對某些服務，一或多個加密模型不可能適用。 支援客戶管理金鑰案例的服務所支援的金鑰類型，可能只有 Azure Key Vault 所支援用於金鑰加密金鑰的一小部分。 此外，這些服務可能會在不同的排程發行這些案例和金鑰類型的支援。 本章節針對每個主要 Azure 資料儲存體服務描述撰寫本文時的靜態加密支援。

### <a name="azure-disk-encryption"></a>Azure 磁碟加密

任何使用 Azure 基礎結構即服務 (IaaS) 功能的客戶都可透過 Azure 磁碟加密讓其 IaaS VM 和磁碟達到靜態加密。 如需有關 Azure 磁片加密的詳細資訊，請參閱 [Azure 磁碟加密檔](../azure-security-disk-encryption-overview.md)。

#### <a name="azure-storage"></a>Azure 儲存體

所有 Azure 儲存體服務 (Blob 儲存體、佇列儲存體、表格儲存體和 Azure 檔案儲存體) 支援待用的伺服器端加密;有些服務會額外支援客戶管理的金鑰和用戶端加密。

- 伺服器端：所有 Azure 儲存體服務預設會使用服務管理的金鑰啟用伺服器端加密，這對應用程式是透明的。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體服務加密](../../storage/common/storage-service-encryption.md)。 Azure Blob 儲存體和 Azure 檔案也支援 Azure Key Vault 中的 RSA 2048 位元客戶管理金鑰。 如需詳細資訊，請參閱[使用 Azure Key Vault 中客戶管理的金鑰進行儲存體服務加密](../../storage/common/storage-encryption-keys-portal.md)。
- 用戶端：Azure Blob、資料表和佇列支援用戶端加密。 當您使用用戶端加密時，客戶會將資料加密，並上傳資料做為加密的 blob。 金鑰管理是由客戶完成。 如需詳細資訊，請參閱 [Microsoft Azure 儲存體的用戶端加密和 Azure Key Vault](../../storage/common/storage-client-side-encryption.md)。

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database 目前支援針對由 Microsoft 管理之服務端和用戶端加密案例的靜態加密。

伺服器加密的支援目前是透過稱為「透明資料加密」的 SQL 功能所提供。 一旦 Azure SQL Database 客戶啟用 TDE 後，就會為他們自動建立和管理金鑰。 可以在資料庫和伺服器等級啟用靜態加密。 自 2017 年 6 月起，[透明資料加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) 依預設會在新建立的資料庫上啟用。 Azure SQL Database 支援 Azure Key Vault 中的 RSA 2048 位元客戶管理金鑰。 如需詳細資訊，請參閱 [Azure SQL Database 和資料倉儲的透明資料加密與攜帶您自己的金鑰支援](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current)。

透過 [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 功能可支援 Azure SQL Database 資料的用戶端加密。 Always Encrypted 會使用用戶端所建立及儲存的金鑰。 客戶可以將主要金鑰儲存在 Windows 憑證存放區、Azure Key Vault 或硬體安全性模組中。 使用 SQL Server Management Studio，SQL 使用者可以選擇他們想要用來加密哪一個資料行的金鑰。

## <a name="conclusion"></a>結論

對於 Microsoft 而言，保護儲存在 Azure 服務內的客戶資料是至關重要的。 所有的 Azure 託管服務都致力於提供靜態加密選項。 Azure 服務支援服務管理的金鑰、客戶管理的金鑰或用戶端加密。 Azure 服務廣泛增強待用加密的可用性，並在未來幾個月內規劃新的選項，並提供預覽和公開上市。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解服務管理的金鑰與客戶管理的金鑰，請參閱 [資料加密模型](encryption-models.md) 。
- 瞭解 Azure 如何使用 [雙重加密](double-encryption.md) 來減輕加密資料所帶來的威脅。
