---
title: Azure Stack Edge 安全性 |Microsoft Docs
description: 說明安全性和隱私權功能，可保護您的 Azure Stack Edge 裝置、服務，以及內部部署和雲端中的資料。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 3d567ec4d760be24fdbb79ff85bd6db0eb4a66c8
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570066"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Azure Stack Edge 安全性和資料保護

當您採用新技術時，安全性是一大考慮，特別是當此技術與機密或專屬資料搭配使用時。 Azure Stack Edge 可協助您確保只有獲得授權的實體可以查看、修改或刪除您的資料。

本文說明可協助保護每個解決方案元件和其中所儲存之資料的 Azure Stack 邊緣安全性功能。

Azure Stack Edge 包含四個與彼此互動的主要元件：

- 裝載**于 Azure 中的 Azure Stack Edge 服務**。 您用來建立裝置訂單、設定裝置，然後追蹤訂單完成的管理資源。
- **Azure Stack Edge 裝置**。 傳送給您的傳輸裝置，讓您可以將內部部署資料匯入至 Azure。
- **連線到裝置的用戶端/主機**。 您基礎結構中的用戶端會連線到 Azure Stack Edge 裝置，並包含需要保護的資料。
- **雲端存放裝置**。 Azure 雲端平臺中儲存資料的位置。 此位置通常是連結至您所建立 Azure Stack Edge 資源的儲存體帳戶。

## <a name="azure-stack-edge-service-protection"></a>Azure Stack Edge 服務保護

Azure Stack Edge 服務是裝載在 Azure 中的管理服務。 此服務可用來設定和管理裝置。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Azure Stack Edge 裝置保護

Azure Stack Edge 裝置是內部部署裝置，可協助您在本機處理資料，然後將其傳送至 Azure，以進行轉換。 您的裝置：

- 需要啟用金鑰才能存取 Azure Stack Edge 服務。
- 會受到裝置密碼的任何時間保護。
- 是鎖定的裝置。 裝置 BMC 和 BIOS 受到密碼保護。 BIOS 受到受限的使用者存取保護。
- 已啟用安全開機。
- 執行 Windows Defender Device Guard。 Device Guard 可讓您只執行您在程式碼完整性原則中定義的受信任應用程式。

### <a name="protect-the-device-via-activation-key"></a>透過啟用金鑰保護裝置

僅允許授權的 Azure Stack Edge 裝置加入您在 Azure 訂用帳戶中建立的 Azure Stack Edge 服務。 若要授權裝置，您必須使用啟用金鑰來啟用具有 Azure Stack Edge 服務的裝置。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

如需詳細資訊，請參閱[取得啟用金鑰](azure-stack-edge-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>透過密碼保護裝置

密碼可確保只有授權的使用者可以存取您的資料。 Azure Stack Edge 裝置會在鎖定狀態下啟動。

您可以：

- 透過瀏覽器連線至裝置的本機 web UI，然後提供密碼以登入裝置。
- 從遠端連線至透過 HTTP 的裝置 PowerShell 介面。 預設會開啟遠端系統管理。 接著，您可以提供裝置密碼來登入裝置。 如需詳細資訊，請參閱[從遠端連線到您的 Azure Stack Edge 裝置](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本機 web UI 來[變更密碼](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access)。 如果您變更密碼，請務必通知所有遠端存取使用者，讓他們在登入時沒有問題。

## <a name="protect-your-data"></a>保護您的資料

本節說明可保護傳輸中和已儲存資料的 Azure Stack 邊緣安全性功能。

### <a name="protect-data-at-rest"></a>保護靜態資料的安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256 位加密是用來保護本機資料。


### <a name="protect-data-in-flight"></a>保護飛行中的資料

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>透過儲存體帳戶保護資料安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 輪替並定期[同步儲存體帳戶金鑰](azure-stack-edge-manage-shares.md#sync-storage-keys)，以協助保護您的儲存體帳戶免于未經授權的使用者。

## <a name="manage-personal-information"></a>管理個人資訊

Azure Stack Edge 服務會收集下列案例中的個人資訊：

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要查看可存取或刪除共用的使用者清單，請遵循[管理 Azure Stack 邊緣上的共用](azure-stack-edge-manage-shares.md)中的步驟。

如需詳細資訊，請參閱[信任中心](https://www.microsoft.com/trustcenter)的 Microsoft 隱私權原則。

## <a name="next-steps"></a>後續步驟

[部署您的 Azure Stack Edge 裝置](azure-stack-edge-deploy-prep.md)
