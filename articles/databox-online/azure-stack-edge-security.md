---
title: Azure Stack Edge Pro 安全性 |Microsoft Docs
description: 描述保護您 Azure Stack Edge Pro 裝置、服務和內部部署和雲端資料的安全性和隱私權功能。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: b5c5143c7bc92487aeabc3cdc1afea5061466027
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903163"
---
# <a name="azure-stack-edge-pro-security-and-data-protection"></a>Azure Stack Edge Pro 安全性和資料保護

當您採用新的技術時，安全性是一項重要的考慮，特別是當該技術與機密或專屬的資料搭配使用時。 Azure Stack Edge Pro 可協助您確定只有獲得授權的實體可以查看、修改或刪除您的資料。

本文描述的 Azure Stack Edge Pro 安全性功能，可協助保護每個解決方案元件和其中儲存的資料。

Azure Stack Edge Pro 是由四個彼此互動的主要元件所組成：

- **在 Azure 中託管的 Azure Stack Edge 服務**。 您用來建立裝置訂單的管理資源、設定裝置，然後追蹤訂單的完成。
- **Azure Stack Edge Pro 裝置**。 傳送給您的傳送裝置可讓您將內部部署資料匯入 Azure。
- **連線到裝置的用戶端/主機**。 基礎結構中的用戶端，連接到 Azure Stack Edge Pro 裝置，並包含需要保護的資料。
- **雲端儲存體**。 Azure 雲端平臺中儲存資料的位置。 此位置通常是連結至您所建立之 Azure Stack Edge 資源的儲存體帳戶。

## <a name="azure-stack-edge-service-protection"></a>Azure Stack Edge 服務保護

Azure Stack Edge 服務是在 Azure 中託管的管理服務。 此服務可用來設定和管理裝置。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-pro-device-protection"></a>Azure Stack Edge Pro 裝置保護

Azure Stack Edge Pro 裝置是內部部署裝置，可在本機處理資料，然後將資料傳送至 Azure，以協助轉換您的資料。 您的裝置：

- 需要啟用金鑰才能存取 Azure Stack Edge 服務。
- 會受到裝置密碼的任何時間保護。
- 是鎖定的裝置。 裝置 BMC 和 BIOS 都受到密碼保護。 BIOS 受到受限的使用者存取保護。
- 已啟用安全開機。
- Windows Defender Device Guard 執行。 Device Guard 可讓您只執行您在程式碼完整性原則中定義的受信任應用程式。

### <a name="protect-the-device-via-activation-key"></a>透過啟用金鑰保護裝置

只有授權的 Azure Stack Edge Pro 裝置才能加入您在 Azure 訂用帳戶中建立的 Azure Stack Edge 服務。 若要授權裝置，您必須使用啟用金鑰來啟用 Azure Stack Edge 服務的裝置。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

如需詳細資訊，請參閱 [取得啟用金鑰](azure-stack-edge-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>透過密碼保護裝置

密碼可確保只有經過授權的使用者可以存取您的資料。 Azure Stack Edge Pro 裝置開機時，會處於鎖定狀態。

您可以：

- 透過瀏覽器連線至裝置的本機 web UI，然後提供密碼以登入裝置。
- 從遠端連線至透過 HTTP 的裝置 PowerShell 介面。 預設會開啟遠端系統管理。 接著，您可以提供裝置密碼以登入裝置。 如需詳細資訊，請參閱 [從遠端連線到您的 Azure Stack Edge Pro 裝置](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本機 web UI 來 [變更密碼](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access)。 如果您變更密碼，請務必通知所有遠端存取使用者，讓他們不會在登入時發生問題。

## <a name="protect-your-data"></a>保護您的資料

本節說明保護傳輸和儲存資料的 Azure Stack Edge Pro 安全性功能。

### <a name="protect-data-at-rest"></a>保護靜態資料的安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256-位加密用來保護本機資料。


### <a name="protect-data-in-flight"></a>保護傳輸中的資料

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>透過儲存體帳戶保護資料安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 定期輪替並 [同步處理您的儲存體帳戶金鑰](azure-stack-edge-manage-shares.md#sync-storage-keys) ，以協助保護您的儲存體帳戶免于未經授權的使用者。

## <a name="manage-personal-information"></a>管理個人資訊

Azure Stack Edge 服務會在下列情況下收集個人資訊：

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要查看可存取或刪除共用的使用者清單，請依照 [Azure Stack Edge Pro 上的管理共用](azure-stack-edge-manage-shares.md)中的步驟執行。

如需詳細資訊，請參閱 [信任中心](https://www.microsoft.com/trustcenter)上的 Microsoft 隱私權原則。

## <a name="next-steps"></a>後續步驟

[部署 Azure Stack Edge Pro 裝置](azure-stack-edge-deploy-prep.md)
