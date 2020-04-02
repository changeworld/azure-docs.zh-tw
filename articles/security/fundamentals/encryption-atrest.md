---
title: Microsoft Azure 資料靜態加密 | Microsoft Docs
description: 本文提供 Microsoft Azure 資料靜態加密概觀、整體功能及一般考量。
services: security
documentationcenter: na
author: msmbaldwin
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 42b83963dc4996a7347d57be712451086fa79b26
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548633"
---
# <a name="azure-data-encryption-at-rest"></a>Azure 資料靜態加密

Microsoft Azure 內有數項工具，可根據公司的安全性和合規性需求來保護資料。 本文著重於：

- 在整個 Microsoft Azure 中保護資料的方式
- 討論參與資料保護實作的各種元件，
- 檢閱不同金鑰管理保護方法的優缺點。

靜態加密是常見的安全性需求。 在 Azure 中，組織可以在沒有自訂金鑰管理解決方案所帶來之風險或成本的情況下，對待用資料進行加密。 組織可以選擇讓 Azure 完全管理靜態加密。 此外，組織擁有各種選項能密切管理加密或加密金鑰。

## <a name="what-is-encryption-at-rest"></a>什麼是靜態加密？

待用加密就是將存留的資料進行編碼 (加密)。 Azure 中的靜態加密設計是使用對稱加密，根據簡單概念模型，快速將大量資料加密及解密：

- 對稱式加密金鑰可用來在寫入儲存體時將資料加密。
- 當資料準備好在記憶體中使用時，相同的加密金鑰可用來將資料解密。
- 資料可能會進行分割，每個分割區可能會使用不同的金鑰。
- 金鑰必須儲存在具備以身分識別為基礎的存取控制和稽核原則的安全位置。 數據加密密鑰通常使用 Azure 密鑰保管庫中的密鑰加密密鑰進行加密,以進一步限制訪問。

在實務上，金鑰管理和控制情節，以及級別和可用性保證，都需要其他建構。 以下將說明 Microsoft Azure 靜態加密的概念和元件。

## <a name="the-purpose-of-encryption-at-rest"></a>靜態加密的目的

待用加密可為儲存的資料 (待用) 提供資料保護。 對靜態資料的攻擊包含嘗試取得儲存資料的硬體之實體存取，並洩露內含的資料。 在這類攻擊中，伺服器的硬碟可能已在維護期間受到錯誤的處理，讓攻擊者能夠移除硬碟。 稍後，攻擊者會將硬碟放入受他們控制下的電腦，以嘗試存取資料。

靜態加密旨在防止攻擊者存取未加密的資料，方法是確保資料在磁碟上時就已加密。 如果攻擊者取得具有已加密資料的硬碟，但是沒有加密金鑰，則該攻擊者必須將加密破解才能讀取資料。 與存取硬碟上未加密的資料相比，此攻擊將會變得更為複雜，且需要耗用更多資源。 基於這個理由，強烈建議您使用靜態加密，且對許多組織而言是高優先順序的需求。

組織致力於資料治理及合規性之際，也可能需要使用待用加密。 產業和政府規定 (例如 HIPAA、PCI 和 FedRAMP 等) 就資料保護和加密需求制定了具體的保護措施。 待用加密是符合這其中部分規範所需的必要手段。 有關 Microsoft 的 FIPS 140-2 驗證方法的詳細資訊,請參閱[聯邦資訊處理標準 (FIPS) 出版物 140-2](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2)。 

除了能滿足合規性和法規要求之外，靜態加密也能提供深度防禦的保護。 Microsoft Azure 針對服務、應用程式及資料提供符合規範的平台。 它也提供全方位的設施和實體安全性、資料存取控制及稽核。 但是,在其他安全措施之一失敗且靜態加密提供此類安全措施的情況下,提供其他「重疊」安全措施非常重要。

Microsoft 致力於為所有雲端服務提供靜態加密選項，並給予客戶對加密金鑰和金鑰使用記錄的控制。 此外，Microsoft 也正朝著預設將所有客戶的待用資料加密的方向努力。

## <a name="azure-encryption-at-rest-components"></a>Azure 靜態加密元件

如先前所述，靜態加密的目的，是使用祕密加密金鑰將保存在磁碟上的資料進行加密。 若要達到這個目標，就必須提供安全的金鑰建立、儲存、存取控制，以及加密金鑰管理。 儘管細節可能有所差異，但 Azure 服務「待用加密」實作可以下圖所示的術語來加以說明。

![元件](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure 金鑰保存庫

加密金鑰的儲存位置以及這些金鑰的存取控制是靜態加密模型的核心。 金鑰必須是高度安全的，但需可由指定使用者進行管理，且可用於特定服務。 針對 Azure 服務，Azure Key Vault 是建議的金鑰儲存體解決方案，並提供常見的跨服務管理體驗。 金鑰是儲存在金鑰保存庫並加以管理，可以將存取金鑰保存庫提供給使用者或服務。 Azure Key Vault 支援客戶建立金鑰或匯入客戶金鑰，可供在客戶管理的加密金鑰情節下使用。

### <a name="azure-active-directory"></a>Azure Active Directory

使用 Azure Key Vault 中儲存的金鑰加以管理或存取，從而進行靜態加密及靜態解密的權限，可以提供給 Azure Active Directory 帳戶。

### <a name="key-hierarchy"></a>金鑰階層

在靜態加密實作中，會使用一個以上的加密金鑰。 在 Azure 金鑰保管庫中儲存加密金鑰可確保金鑰的安全訪問和密鑰的集中管理。 但是,與與密鑰保管庫交互的每個數據操作相比,服務本地訪問加密密鑰對於批量加密和解密效率更高,從而可實現更強的加密和更好的性能。 限制使用單一加密金鑰會降低金鑰洩露的風險，以及在必須更換金鑰時的重新加密成本。 靜態模型中的 Azure 加密使用由以下類型金鑰組成的金鑰層次結構來滿足所有這些需求:

- **資料加密金鑰 (DEK)** – 用於將分割區或資料區塊加密的對稱 AES256 金鑰。  單一資源可能會有多個分割區和多個資料加密金鑰。 使用不同金鑰將每個資料區塊進行加密，會使密碼編譯分析攻擊更加困難。 資源提供者或要將特定區塊加密和解密的應用程式執行個體都需要存取 DEK。 當新的金鑰取代 DEK 時，只有在相關聯區塊中的資料才需要使用新的金鑰重新加密。
- **金鑰加密金鑰 (KEK)** – 用於加密資料加密金鑰的加密金鑰。 使用從不離開金鑰保管庫的密鑰金鑰允許對數據加密密鑰本身進行加密和控制。 可存取 KEK 的實體可能不同於需要 DEK 的實體。 實體可以代理存取 DEK，以限制每個 DEK 只能由特定分割區存取。 因為需要 KEK 才能將 DEK 解密，KEK 實際上就是單一點，藉以透過刪除 KEK 來有效地刪除 DEK。

使用金鑰加密金鑰加密的數據加密金鑰單獨存儲,只有有權存取金鑰加密金鑰的實體才能解密這些資料加密金鑰。 支援不同模型的金鑰儲存。 我們稍後將在下一節更深入討論每個模型。

## <a name="data-encryption-models"></a>資料加密模型

若要了解 Azure 中的各種資源提供者如何實作待用加密，就務必了解各種加密模式及其優缺點。 這些定義會跨 Azure 中的所有資源提供者進行共用，以確保為通用的語言和分類法。

伺服器端加密有三種情節：

- 使用服務管理之金鑰的伺服器端加密
  - Azure 資源提供者執行加密和解密作業
  - Microsoft 管理金鑰
  - 完整的雲端功能

- 在 Azure Key Vault 中使用客戶管理金鑰的伺服器端加密
  - Azure 資源提供者執行加密和解密作業
  - 客戶透過 Azure Key Vault 控制金鑰
  - 完整的雲端功能

- 在客戶控制的硬體上使用客戶管理金鑰的伺服器端加密
  - Azure 資源提供者執行加密和解密作業
  - 客戶在客戶控制的硬體上控制金鑰
  - 完整的雲端功能

針對用戶端加密，請考慮下列各項：

- Azure 服務無法查看解密的資料
- 客戶在內部部署環境 (或其他安全的儲存區中) 管理或儲存金鑰。 金鑰無法供 Azure 服務使用
- 縮減的雲端功能

Azure 中支援的模型如先前所述區分成兩個主要群組：「用戶端加密」和「伺服器端加密」。 除了所使用的加密靜態模型之外，Azure 服務一律建議使用諸如 TLS 或 HTTPS 等安全的傳輸。 因此，傳輸中的加密應該由傳輸通訊協定來處理，且不應作為要使用哪個靜態加密模型的主要判斷因素。

### <a name="client-encryption-model"></a>用戶端加密模型

用戶端加密模型是指由服務或呼叫應用程式在資源提供者或 Azure 外部執行的加密。 加密可由 Azure 中的服務應用程式或客戶資料中心內執行的應用程式執行。 在任一案例中，利用此加密模型時，Azure 資源提供者無需以任何方式解密的能力或具有加密金鑰的存取權，即可接收加密的 blob 資料。 在此模型中，金鑰管理是由呼叫服務/應用程式所完成，且對 Azure 服務不透明。

![Client](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>伺服器端加密模型

伺服器端加密模型是指 Azure 服務所執行的加密。 在這個模型中，資源提供者會執行加密和解密作業。 例如，Azure 儲存體可能會在純文字作業中接收資料，並在內部執行加密和解密。 資源提供者可能會使用由 Microsoft 或客戶管理的加密金鑰，根據提供的設定而定。

![伺服器](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>伺服器端加密金鑰管理模型

每個伺服器端靜態加密模型都表示金鑰管理的特殊特性。 這包括加密金鑰所建立及儲存的位置和方式，以及存取模型和金鑰輪替程序。

#### <a name="server-side-encryption-using-service-managed-keys"></a>使用服務管理金鑰的伺服器端加密

對許多客戶而言，基本需求就是確保在靜態時會將資料加密。 使用服務管理之金鑰的伺服器端加密會啟用這個模型，方法是允許客戶標示特定的資源 (儲存體帳戶、SQL DB 等) 以進行加密，並將諸如金鑰發佈、輪替和備份等所有金鑰管理層面保留給 Microsoft。 大部分支援靜態加密的 Azure 服務通常會支援這個將加密金鑰管理卸載至 Azure 的模型。 Azure 資源提供者會建立金鑰、將它們放置在安全的儲存體，並在需要時加以擷取。 這表示服務具有金鑰的完整存取權，且服務可完整控制認證生命週期管理。

![受控](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

使用服務管理之金鑰的伺服器端加密因此能快速解決靜態加密的需求，並為客戶提供低額外負荷。 情況允許時，客戶通常會開啟目標訂用帳戶和資源提供者的 Azure 入口網站，並勾選方塊以指示要加密資料。 在部分 Resource Manager 中，使用服務管理金鑰的伺服器端加密預設為開啟。

搭配 Microsoft 管理之金鑰的伺服器端加密確實表示服務具有儲存及管理金鑰的完整存取權。 雖然部分客戶會因為認為可獲得更高的安全性而要管理金鑰，但在評估此模型時，應該考慮自訂金鑰儲存解決方案的相關成本和風險。 在許多情況下，組織可能會判斷資源限制或內部部署解決方案的風險可能會大於在雲端管理靜態加密金鑰的風險。  不過，對於需要控制加密金鑰的建立或生命週期，或是使用與管理服務不同的人員來管理服務的加密金鑰 (也就是說，將金鑰管理與服務的整體管理模型隔離) 的組織而言，此模型可能並不足夠。

##### <a name="key-access"></a>金鑰存取

使用具服務管理之金鑰的伺服器端加密時，金鑰建立、儲存和存取服務都是由服務所管理。 一般而言，基本的 Azure 資源提供者會將資料加密金鑰儲存在接近資料、快速可用且可存取的儲存區中，而金鑰加密金鑰則是儲存在安全的內部儲存區中。

**優點**

- 簡單設定
- Microsoft 會管理金鑰輪替、備份與備援
- 客戶沒有與實作相關聯的成本，或自訂金鑰管理配置的風險。

**缺點**

- 沒有加密金鑰 (金鑰規格、生命週期、撤銷等) 的客戶控制權
- 無法從服務的整體管理模型將金鑰管理隔離

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>在 Azure Key Vault 中使用客戶管理金鑰的伺服器端加密

在需要將待用資料加密並控制加密金鑰的情節中，客戶可以使用 Key Vault 中使用客戶管理之金鑰的伺服器端加密。 某些服務可能只會在 Azure Key Vault 中儲存根金鑰加密金鑰，並將加密的資料加密金鑰儲存在較接近資料的內部位置。 在該案例中，客戶可以將自己的金鑰帶到 Key Vault (BYOK – 自備金鑰) 或產生新的金鑰，並使用它們來加密所需的資源。 當資源提供程式執行加密和解密操作時,它使用配置的密鑰加密密鑰作為所有加密操作的根密鑰。

金鑰加密金鑰丟失意味著資料丟失。 因此,不應刪除金鑰。 每當創建或旋轉時,都應備份鍵。 要在儲存金鑰加密金鑰的任何保管庫中開啟[。](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 而不是刪除金鑰,設置為 false 或設置到期日期。

##### <a name="key-access"></a>金鑰存取

Azure Key Vault 中使用客戶管理之金鑰的伺服器端加密模型，需讓服務視需要存取金鑰來進行加密和解密。 透過存取控制原則，服務可以存取靜態加密金鑰。 此原則授與服務身分識別存取，以接收金鑰。 代表相關聯的訂用帳戶所執行的 Azure 服務，可以使用該訂用帳戶中的身分識別來加以設定。 服務可以執行 Azure Active Directory 驗證，並接收驗證權杖，會將其本身識別為代表訂用帳戶的該服務。 接著會將該權杖提供給 Key Vault，從而取得已獲得存取權的金鑰。

針對使用加密金鑰的作業，可以授與服務識別存取任何下列作業：解密、加密、解除包裝金鑰、包裝金鑰、驗證、簽署、取得、列出、更新、建立、匯入、刪除、備份和還原。

若要取得用於將靜態資料加密或解密的金鑰，Resource Manager 服務執行個體所要執行的服務識別必須擁有「解除包裝金鑰」(可取得解密金鑰) 和「包裝金鑰」(建立新的金鑰時，可將金鑰插入保存庫金鑰)。

>[!NOTE]
>如需關於 Key Vault 授權的詳細資訊，請參閱 [Azure Key Vault 文件](../../key-vault/key-vault-secure-your-key-vault.md)中的「保護您的金鑰保存庫」頁面。

**優點**

- 完整控制所使用的金鑰 – 加密金鑰會在客戶控制下的 Key Vault 中進行管理。
- 能夠將多個服務加密到一個主機
- 可從服務的整體管理模型將金鑰管理隔離
- 可跨區域定義服務與金鑰位置

**缺點**

- 客戶對於金鑰存取管理擁有完全責任
- 客戶對於金鑰生命週期管理擁有完全責任
- 安裝與設定的其他額外負荷

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>使用客戶控制的硬體中的客戶管理金鑰進行伺服器端加密

某些 Azure 服務可允許「裝載您自己的金鑰」(HYOK) 金鑰管理模型。 當需要加密待用資料並在不受 Microsoft 控制的專屬存放庫中管理金鑰時，便適用此管理模式。 在此模型中，服務必須從外部網站擷取金鑰。 效能和可用性保證會受到影響，且設定會更為複雜。 此外，因為服務在加密和解密作業期間可存取 DEK，此模型的整體安全性保證會與在 Azure Key Vault 中由客戶管理金鑰時類似。  因此，此模型不適用於大部分的組織，除非組織有特定金鑰管理需求。 由於這些限制，大部分的 Azure 服務不支援在客戶所控制的硬體中使用伺服器管理金鑰的伺服器端加密。

##### <a name="key-access"></a>金鑰存取

在客戶所控制的硬體中採用使用服務管理之金鑰的伺服器端加密時，金鑰會保留在客戶所設定的系統上。 支援這個模型的 Azure 服務可提供方法，來建立與客戶提供之金鑰儲存區的安全連線。

**優點**

- 完整控制所使用的根金鑰 – 金鑰加密會由客戶提供的儲存區進行管理
- 能夠將多個服務加密到一個主機
- 可從服務的整體管理模型將金鑰管理隔離
- 可跨區域定義服務與金鑰位置

**缺點**

- 對於金鑰儲存、安全性、效能和可用性擁有完全責任
- 對於金鑰存取管理擁有完全責任
- 對於金鑰生命週期管理擁有完全責任
- 安裝、設定和持續維護的成本高昂
- 客戶資料中心與 Azure 資料中心之間網路可用性的相依性增加。

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Microsoft 雲端服務中的靜態加密

這三種雲端模型中全都是使用 Microsoft 雲端服務：IaaS、PaaS、SaaS。 下列範例說明它們如何符合每個模型：

- 軟體服務是指「軟體即服務」或 SaaS，具有雲端所提供的應用程式，例如 Office 365。
- 平台服務是指客戶在其應用程式中利用雲端，會針對諸如儲存、分析和服務匯流排功能等項目使用雲端。
- 基礎結構服務或基礎結構即服務 (IaaS) 可供客戶在其中部署裝載於雲端中且可能運用其他雲端服務的作業系統和應用程式。

### <a name="encryption-at-rest-for-saas-customers"></a>SaaS 客戶的靜態加密

軟體即服務 (SaaS) 客戶通常會啟用或可在每個服務中使用靜態加密。 Office 365 有幾個選項可供客戶確認或啟用靜態加密。 如需 Office 365 服務的相關資訊，請參閱 [Office 365 中的加密](https://docs.microsoft.com/office365/securitycompliance/encryption) \(機器翻譯\)。

### <a name="encryption-at-rest-for-paas-customers"></a>PaaS 客戶的靜態加密

平臺即服務 (PaaS) 客戶的數據通常駐留在儲存服務(如 Blob 儲存),但也可能緩存或存儲在應用程式執行環境(如虛擬機器)中。 若要查看您可使用的靜態加密選項，請檢查下表以查看您所使用的儲存體和應用程式平台。

### <a name="encryption-at-rest-for-iaas-customers"></a>IaaS 客戶的靜態加密

基礎結構即服務 (IaaS) 客戶可以有各種不同的使用中服務和應用程式。 IaaS 服務可以在其 Azure 裝載的虛擬機器和使用 Azure 磁碟加密的 VHD 中啟用靜態加密。

#### <a name="encrypted-storage"></a>加密的儲存體

諸如 PaaS、IaaS 等解決方案可以運用儲存資料靜態加密的其他 Azure 服務。 在這些情況下，您可以啟用每個已使用 Azure 服務所提供的靜態加密支援。 下表列舉出主要儲存體、服務和應用程式平台，以及支援的待用加密模型。 

#### <a name="encrypted-compute"></a>加密的計算

使用服務管理金鑰使用儲存服務加密對所有託管磁碟、快照和映射進行加密。 更完整的靜態加密解決方案可確保數據永遠不會以未加密的形式保留。 在虛擬機上處理數據時,資料可以保存到 Windows 頁檔或 Linux 交換檔、崩潰轉儲或應用程式日誌。 若要確保此資料會進行靜態加密，IaaS 應用程式可以在 Azure IaaS 虛擬機器 (Windows 或 Linux) 和虛擬磁碟上使用 Azure 磁碟加密。

#### <a name="custom-encryption-at-rest"></a>自訂靜態加密

建議您盡可能讓 IaaS 應用程式運用任何已使用 Azure 服務所提供的 Azure 磁碟加密及靜態加密選項。 在某些情況下，例如異常加密需求或非以 Azure 為基礎的儲存體，IaaS 應用程式的開發人員可能需要自行實作靜態加密。 IaaS 解決方案的開發人員可以運用某些 Azure 元件，更妥善與 Azure 管理和客戶期望進行整合。 具體來說，開發人員應該使用 Azure Key Vault 服務來提供安全的金鑰儲存體，並為客戶提供一致的金鑰管理選項，以及大部分 Azure 平台服務的選項。 此外，自訂的解決方案應該使用 Azure 受控服務識別，讓服務帳戶可存取加密金鑰。 如需關於 Azure Key Vault 和受控服務識別的開發人員資訊，請參閱其個別的 SDK。

## <a name="azure-resource-providers-encryption-model-support"></a>Azure 資源提供者加密模型支援

每個 Microsoft Azure 服務都支援一或多個靜態加密模型。 不過，針對某些服務，一或多個加密模型不可能適用。 支援客戶管理金鑰案例的服務所支援的金鑰類型，可能只有 Azure Key Vault 所支援用於金鑰加密金鑰的一小部分。 此外，這些服務可能會在不同的排程發行這些案例和金鑰類型的支援。 本章節針對每個主要 Azure 資料儲存體服務描述撰寫本文時的靜態加密支援。

### <a name="azure-disk-encryption"></a>Azure 磁碟加密

任何使用 Azure 基礎結構即服務 (IaaS) 功能的客戶都可透過 Azure 磁碟加密讓其 IaaS VM 和磁碟達到靜態加密。 有關 Azure 磁碟加密的詳細資訊,請參閱[Azure 磁碟加密文件](../azure-security-disk-encryption-overview.md)。

#### <a name="azure-storage"></a>Azure 儲存體

所有 Azure 儲存服務(Blob 儲存、佇列儲存、表存儲和 Azure 檔案)都支援伺服器端靜態加密;某些服務還支援客戶管理的密鑰和用戶端加密。 

- 伺服器端：所有 Azure 儲存體服務預設會使用服務管理的金鑰啟用伺服器端加密，這對應用程式是透明的。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體服務加密](../../storage/common/storage-service-encryption.md)。 Azure Blob 儲存體和 Azure 檔案也支援 Azure Key Vault 中的 RSA 2048 位元客戶管理金鑰。 如需詳細資訊，請參閱[使用 Azure Key Vault 中客戶管理的金鑰進行儲存體服務加密](../../storage/common/storage-encryption-keys-portal.md)。
- 用戶端：Azure Blob、資料表和佇列支援用戶端加密。 當您使用用戶端加密時，客戶會將資料加密，並上傳資料做為加密的 blob。 金鑰管理是由客戶完成。 如需詳細資訊，請參閱 [Microsoft Azure 儲存體的用戶端加密和 Azure Key Vault](../../storage/common/storage-client-side-encryption.md)。

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database 目前支援針對由 Microsoft 管理之服務端和用戶端加密案例的靜態加密。

伺服器加密的支援目前是透過稱為「透明資料加密」的 SQL 功能所提供。 一旦 Azure SQL Database 客戶啟用 TDE 後，就會為他們自動建立和管理金鑰。 可以在資料庫和伺服器等級啟用靜態加密。 自 2017 年 6 月起，[透明資料加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) 依預設會在新建立的資料庫上啟用。 Azure SQL Database 支援 Azure Key Vault 中的 RSA 2048 位元客戶管理金鑰。 如需詳細資訊，請參閱 [Azure SQL Database 和資料倉儲的透明資料加密與攜帶您自己的金鑰支援](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current)。

透過 [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 功能可支援 Azure SQL Database 資料的用戶端加密。 Always Encrypted 會使用用戶端所建立及儲存的金鑰。 客戶可以將主要金鑰儲存在 Windows 憑證存放區、Azure Key Vault 或硬體安全性模組中。 SQL 使用者可以使用 SQL Server Management Studio 來選擇他們要用來加密資料行的金鑰。

#### <a name="encryption-model-and-key-management-table"></a>加密模型與金鑰管理表

|                                  |                    | **加密模型和金鑰管理** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **使用服務管理金鑰的伺服器端**     | **使用客戶管理的金鑰的伺服器端**             | **用戶端使用用戶端管理金鑰**      |
| **AI 與機器學習**      |                    |                    |                    |
| Azue 認知搜尋           | 是                | 是                | -                  |
| Azure Machine Learning           | 是                | 是                | -                  |
| Azure Machine Learning Studio    | 是                | 預覽，RSA 2048 位元 | -               |
| Power BI                         | 是                | 預覽，RSA 2048 位元 | -                  |
| **分析**                    |                    |                    |                    |
| Azure 串流分析           | 是                | -                  | -                  |
| 事件中樞                       | 是                | 是,所有 RSA 長度。 | -                  |
| 函式                        | 是                | 是,所有 RSA 長度。 | -                  |
| Azure Analysis Services          | 是                | -                  | -                  |
| Azure 資料目錄               | 是                | -                  | -                  |
| 阿帕契卡夫卡在 Azure HDInsight  | 是                | 所有 RSA 長度。   | -                  |
| Azure 監視器應用程式見解 | 是                | 是                | -                  |
| Azure 監視器紀錄分析 | 是                | 是                | -                  |
| Azure 資料總管              | 是                | 是                | -                  |
| Azure Data Factory               | 是                | 是                | -                  |
| Azure Data Lake Store            | 是                | 是，RSA 2048 位元  | -                  |
| **容器**                   |                    |                    |                    |
| Azure Kubernetes Service         | 是                | 是                | -                  |
| Container Instances              | 是                | 是                | -                  |
| Container Registry               | 是                | 是                | -                  |
| **計算**                      |                    |                    |                    |
| 虛擬機器                 | 是                | 是，RSA 2048 位元  | -                  |
| 虛擬機器擴展集        | 是                | 是，RSA 2048 位元  | -                  |
| SAP HANA                         | 是                | 是，RSA 2048 位元  | -                  |
| App Service 方案                      | 是                | 是                | -                  |
| 自動化                       | 是                | 是                | -                  |
| Azure 入口網站                     | 是                | 是                | -                  |
| Logic Apps                       | 是                | 是                | -                  |
| Azure 受控應用程式       | 是                | 是                | -                  |
| 服務匯流排                      | 是                | 是                | -                  |
| Site Recovery                    | 是                | 是                | -                  |
| **資料庫**                    |                    |                    |                    |
| 虛擬機器上的 SQL Server   | 是                | 是，RSA 2048 位元  | 是                |
| Azure SQL Database               | 是                | 是，RSA 2048 位元  | 是                |
| 瑪麗亞DB的 Azure SQL 資料庫   | 是                | -                  | -                  |
| MySQL 的 Azure SQL 資料庫     | 是                | 是                | -                  |
| Azure SQL 資料庫,用於後格雷SQL | 是               | 是                | -                  |
| Azure Synapse Analytics          | 是                | 是，RSA 2048 位元  | -                  |
| SQL Server Stretch Database      | 是                | 是，RSA 2048 位元  | 是                |
| 表格儲存體                    | 是                | 是                | 是                |
| Azure Cosmos DB                  | 是                | 是                | -                  |
| Azure Databricks                 | 是                | 是                | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps                     | 是                | -                  | 是                |
| Azure Repos                      | 是                | -                  | 是                |
| **身分識別**                     |                    |                    |                    |
| Azure Active Directory           | 是                | -                  | -                  |
| Azure Active Directory Domain Services | 是          | 是，RSA 2048 位元  | -                  |
| **整合**                  |                    |                    |                    |
| 服務匯流排                      | 是                | 是                | 是                |
| Event Grid                       | 是                | -                  | -                  |
| API 管理                   | 是                | -                  | -                  |
| **IoT 服務**                 |                    |                    |                    |
| IoT 中樞                          | 是                | 是                | 是                |
| **管理和治理**    |                    |                    |                    |
| Azure Site Recovery              | 是                | -                  | -                  |
| **媒體**                        |                    |                    |                    |
| 媒體服務                   | 是                | -                  | 是                |
| **儲存空間**                      |                    |                    |                    |
| Blob 儲存體                     | 是                | 是，RSA 2048 位元  | 是                |
| 磁碟儲存體                     | 是                | 是                | -                  |
| 託管磁碟儲存             | 是                | 是                | -                  |
| 檔案儲存體                     | 是                | 是，RSA 2048 位元  | -                  |
| 佇列儲存體                    | 是                | 是                | 是                |
| Avere vFXT                       | 是                | -                  | -                  |
| Azure NetApp Files               | 是                | -                  | -                  |
| 封存儲存體                  | 是                | 是，RSA 2048 位元  | -                  |
| StorSimple                       | 是                | 是，RSA 2048 位元  | 是                |
| Azure 備份                     | 是                | 是                | 是                |
| 資料箱                         | 是                | -                  | 是                |
| 資料箱邊緣                    | 是                | 是                | -                  |

## <a name="conclusion"></a>結論

對於 Microsoft 而言，保護儲存在 Azure 服務內的客戶資料是至關重要的。 所有的 Azure 託管服務都致力於提供靜態加密選項。 諸如 Azure 儲存體、Azure SQL Database 與金鑰分析和智慧服務等基本的服務，都已經提供待用加密選項。 這些服務有部分會支援客戶控制的金鑰和用戶端加密，以及服務管理的金鑰和加密。 Microsoft Azure 服務正廣泛提高靜態加密可用性，新的選項已規劃提供預覽，並即將於幾個月內公開上市。
