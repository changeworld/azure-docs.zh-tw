---
title: Microsoft Azure 資料箱磁碟安全性功能 |資料中的 Microsoft Docs
description: 說明為 Azure 資料箱磁碟所實行的安全性功能，這是可讓您將大量資料傳輸至 Azure 的雲端解決方案
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: 59cce6d89bc863ad489a8ed37c7f284d3085c775
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117294"
---
# <a name="azure-data-box-disk-security-and-data-protection"></a>Azure 資料箱磁碟安全性和資料保護

本文說明 Azure 資料箱磁碟的安全性功能，這些功能可協助保護每個資料箱解決方案元件，以及這些元件中儲存的資料。 

## <a name="data-flow-through-components"></a>透過元件的資料流程

Microsoft Azure 資料箱解決方案包含四個彼此互動的主要元件：

- **在 Azure 中託管的 Azure 資料箱服務** – 可供您用來建立磁碟訂單、設定磁碟，然後追蹤訂單直到完成的管理服務。
- **資料箱磁碟** – 寄送給您的實體磁碟，可供您將內部部署資料匯入 Azure。 
- **連線至磁碟的用戶端/主機** – 在基礎結構中，透過 USB 連線至資料箱磁碟、並產生需受保護資料的用戶端。
- **雲端儲存體** – Azure 雲端中儲存資料的位置。 這通常是儲存體帳戶，且連結到您所建立的 Azure 資料箱資源。

下圖表示透過 Azure 資料箱磁碟解決方案從內部部署至 Azure 的資料流程。

![資料箱磁碟安全性](media/data-box-disk-security/data-box-disk-security-1.png)

## <a name="security-features"></a>安全性功能

資料箱磁碟會確保只有獲得授權的實體可以檢視、修改或刪除您的資料，從而提供資料保護的安全解決方案。 此解決方案的安全性功能適用於磁碟以及相關聯的服務，確保在其中所儲存資料的安全性。

### <a name="data-box-disk-protection"></a>資料箱磁碟保護

資料箱磁碟受到下列功能保護：

- 隨時進行磁碟的 BitLocker AES-128 位元加密。
- 保護磁碟的更新功能。
- 磁碟會以鎖定狀態寄送，且只能透過資料箱磁碟解除鎖定工具來解除鎖定。 資料箱磁碟服務入口網站中提供解除鎖定工具。

### <a name="data-box-disk-data-protection"></a>資料箱磁碟資料保護

流入及流出資料箱磁碟的資料會受到下列功能保護：

- 隨時進行 BitLocker 的資料加密。
- 將資料上傳至 Azure 完成之後，安全清除磁碟中的資料。 資料清除是根據 NIST 800-88r1 標準。

### <a name="data-box-service-protection"></a>資料箱服務保護

資料箱服務受到下列功能保護。

- 若要存取資料箱磁碟服務，貴組織需有內含資料箱磁碟的 Azure 訂用帳戶。 您的訂用帳戶控管您在 Azure 入口網站中可存取的功能。
- 因為資料箱服務裝載於 Azure 中，所以會受到 Azure 安全性功能的保護。 如需有關 Microsoft Azure 所提供的安全性功能的詳細資訊，請移至 [Microsoft Azure 信任中心](https://www.microsoft.com/TrustCenter/Security/default.aspx)。
- 資料箱磁碟會儲存磁碟通行金鑰，可用來將服務中的磁碟解除鎖定。 
- 資料箱磁碟服務會在服務中儲存訂單詳細資料和狀態。 刪除訂單時，會將此資訊刪除。

## <a name="managing-personal-data"></a>管理個人資料

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Azure 資料箱磁碟會在服務中收集並顯示下列金鑰執行個體的個人資訊：

- **通知設定** - 當您建立訂單時，要在通知設定下設定使用者的電子郵件地址。 系統管理員可以檢視此資訊。 當作業達到終止狀態、或您刪除訂單時，服務會將這項資訊刪除。

- **訂單詳細資料** – 一旦訂單建立後，交貨地址、電子郵件、使用者的連絡人資訊都會儲存在 Azure 入口網站中。 儲存的資訊包括︰

  - 連絡人姓名
  - 電話號碼
  - 電子郵件
  - 街道地址
  - City
  - 郵遞區號
  - State
  - 國家/地區/州/省/區域
  - 磁碟機識別碼
  - 貨運公司帳戶號碼
  - 運送追蹤號碼

    當作業完成、或當您刪除訂單時，資料箱服務會刪除訂單詳細資料。

- **交貨地址** – 下訂單之後，資料箱服務會向第三方快遞公司 (例如 UPS 或 DHL) 提供交貨地址。 

如需詳細資訊，請在[信任中心](https://www.microsoft.com/trustcenter)檢閱 Microsoft 隱私權原則。


## <a name="next-steps"></a>後續步驟

- 檢閱[資料箱磁碟需求](data-box-disk-system-requirements.md)。
- 了解[資料箱磁碟限制](data-box-disk-limits.md)。
- 在 Azure 入口網站中快速部署 [Azure 資料箱磁碟](data-box-disk-quickstart-portal.md)。
