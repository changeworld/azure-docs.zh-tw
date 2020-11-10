---
title: Microsoft Azure 資料箱安全性概觀 | 資料中的 Microsoft Docs
description: 描述裝置、服務中的 Azure 資料箱安全性功能，以及駐留在資料箱上的資料
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: alkohli
ms.openlocfilehash: f9330f99a0473aa38da2fcbb8ae0624a37746613
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444753"
---
# <a name="azure-data-box-security-and-data-protection"></a>Azure 資料箱安全性和資料保護

資料箱會確保只有獲得授權的實體可以檢視、修改或刪除您的資料，從而提供資料保護的安全解決方案。 本文說明 Azure 資料箱的安全性功能，這些功能可協助保護每個資料箱解決方案元件，以及這些元件中儲存的資料。 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>透過元件的資料流程

Microsoft Azure 資料箱解決方案包含四個彼此互動的主要元件：

- **在 Azure 中託管的 Azure 資料箱服務** – 可供您用來建立裝置訂單、設定裝置，然後追蹤訂單直到完成的管理服務。
- **資料箱裝置** – 寄送給您的傳輸裝置，可供您將內部部署資料匯入 Azure。 
- **連線至裝置的用戶端/主機** – 在基礎結構中，連線至資料箱裝置並包含需受保護資料的用戶端。
- **雲端儲存體** – Azure 中儲存資料的位置。 這通常是儲存體帳戶，且連結到您所建立的 Azure 資料箱資源。

下圖指出透過 Azure 資料箱解決方案從內部部署至 Azure 的資料流程，以及資料流程經解決方案時的各種安全性功能。 此流程適用于資料箱的匯入順序。

![資料箱匯入安全性](media/data-box-security/data-box-security-import.png)

下圖適用于資料箱的匯出順序。

![資料箱匯出安全性](media/data-box-security/data-box-security-export.png)

當資料流經此解決方案時，就會記錄事件並產生記錄檔。 如需詳細資訊，請移至：

- [Azure 資料箱匯入訂單的追蹤和事件記錄](data-box-logs.md)。
- [Azure 資料箱匯出訂單的追蹤和事件記錄](data-box-export-logs.md)

## <a name="security-features"></a>安全性功能

資料箱會確保只有獲得授權的實體可以檢視、修改或刪除您的資料，從而提供資料保護的安全解決方案。 此解決方案的安全性功能適用於磁碟以及相關聯的服務，確保在其中所儲存資料的安全性。 

### <a name="data-box-device-protection"></a>資料箱裝置保護

資料箱裝置受到下列功能保護：

- 堅固耐用的裝置外殼，可承受衝擊、不利的運輸狀況以及環境條件。 
- 硬體及軟體竄改偵測，可防止進一步的裝置作業。
- 只能執行資料箱專用軟體。
- 在鎖定狀態下開機。
- 透過裝置解除鎖定密碼控制裝置存取。
- 存取認證，以將資料複製到裝置中或從裝置複製資料。 Azure 入口網站中，所有對 [裝置認證] 頁面的存取都會記錄於[活動記錄](data-box-logs.md#query-activity-logs-during-setup)中。

### <a name="data-box-data-protection"></a>資料箱資料保護

流入及流出資料箱的資料會受到下列功能保護：

- 待用資料的 AES 256 位元加密。
- 可用於資料傳輸的加密通訊協定。 建議您在將資料從資料伺服器複製到資料時，使用具有加密的 SMB 3.0 來保護資料。
- 將資料上傳至 Azure 完成之後，安全清除裝置中的資料。 資料清除是依據[附錄 A：NIST 800-88r1 標準中的 ATA 硬碟](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)中的指導方針進行。 資料清除事件記錄在[訂購記錄](data-box-logs.md#download-order-history)中。

### <a name="data-box-service-protection"></a>資料箱服務保護

資料箱服務受到下列功能保護。

- 若要存取資料箱服務，貴組織需有內含資料箱的 Azure 訂用帳戶。 您的訂用帳戶控管您在 Azure 入口網站中可存取的功能。
- 因為資料箱服務裝載於 Azure 中，所以會受到 Azure 安全性功能的保護。 如需有關 Microsoft Azure 所提供的安全性功能的詳細資訊，請移至 [Microsoft Azure 信任中心](https://www.microsoft.com/TrustCenter/Security/default.aspx)。
- 您可以透過使用 Azure 角色來控制資料箱訂單的存取權。 如需詳細資訊，請參閱[設定資料箱訂單的存取控制](data-box-logs.md#set-up-access-control-on-the-order)
- 資料箱服務會儲存解除鎖定密碼，用來解鎖服務中的裝置。
- 資料箱服務會在服務中儲存訂單詳細資料和狀態。 刪除訂單時，會將此資訊刪除。

## <a name="managing-personal-data"></a>管理個人資料

Azure 資料箱會在服務中收集並顯示下列金鑰執行個體的個人資訊：

- **通知設定** - 當您建立訂單時，要在通知設定下設定使用者的電子郵件地址。 系統管理員可以檢視此資訊。 當作業達到終止狀態、或您刪除訂單時，服務會將這項資訊刪除。

- **訂單詳細資料** – 一旦訂單建立後，交貨地址、電子郵件、使用者的連絡人資訊都會儲存在 Azure 入口網站中。 儲存的資訊包括︰

  - 連絡人姓名
  - 電話號碼
  - 電子郵件
  - 街道地址
  - City
  - 郵遞區號
  - 狀態
  - 國家/地區/州/省/區域
  - 貨運公司帳戶號碼
  - 運送追蹤號碼

    當作業完成、或當您刪除訂單時，資料箱服務會刪除訂單詳細資料。

- **交貨地址** – 下訂單之後，資料箱服務會向第三方貨運公司 (例如 UPS 或 DHL) 提供交貨地址。 

如需詳細資訊，請在[信任中心](https://www.microsoft.com/trustcenter)檢閱 Microsoft 隱私權原則。


## <a name="security-guidelines-reference"></a>安全性方針參考

資料箱中會實作下列安全性指導方針： 

|指導方針   |說明   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | 適用於防水防塵         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | 適用於不利的運輸條件耐久性          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | 適用於安全韌體更新         |
|[FIPS 140-2 Level 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | 適用於資料保護         |
|附錄 A：[NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf) 中的 ATA 硬碟      | 適用於資料清理         |

## <a name="next-steps"></a>後續步驟

- 檢閱[資料箱需求](data-box-system-requirements.md)。
- 了解[資料箱限制](data-box-limits.md)。
- 在 Azure 入口網站中快速部署 [Azure 資料箱](data-box-quickstart-portal.md)。
