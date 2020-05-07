---
title: Azure 資料箱閘道安全性 |Microsoft Docs
description: 說明安全性和隱私權功能，可保護內部部署和雲端中的 Azure 資料箱閘道虛擬裝置、服務和資料。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 2476cf360909374f711564fb5fad5c9e0706083d
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562469"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure 資料箱閘道安全性和資料保護

當您採用新技術時，安全性是一大考慮，特別是當此技術與機密或專屬資料搭配使用時。 Azure 資料箱閘道可協助您確保只有獲得授權的實體可以查看、修改或刪除您的資料。

本文說明可協助保護每個解決方案元件和其中所儲存之資料的 Azure 資料箱閘道安全性功能。

資料箱的閘道解決方案包含四個與彼此互動的主要元件：

- 裝載**于 Azure 中的資料箱閘道服務**。 您用來建立裝置訂單、設定裝置，然後追蹤訂單完成的管理資源。
- **資料箱閘道裝置**]。 您在提供之系統的虛擬機器中布建的虛擬裝置。 此虛擬裝置可用來將您的內部部署資料匯入至 Azure。
- **連線到裝置的用戶端/主機**。 您基礎結構中的用戶端會連線到資料箱閘道裝置，並包含需要保護的資料。
- **雲端存放裝置**。 Azure 雲端平臺中儲存資料的位置。 此位置通常是連結至您所建立資料箱閘道資源的儲存體帳戶。


## <a name="data-box-gateway-service-protection"></a>資料箱閘道服務保護

資料箱閘道服務是裝載在 Azure 中的管理服務。 此服務可用來設定和管理裝置。

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>資料箱閘道裝置保護

資料箱閘道裝置是在您提供的內部部署系統的虛擬機器中布建的虛擬裝置。 裝置可協助將資料傳送至 Azure。 您的裝置：

- 需要啟用金鑰才能存取 Azure Stack Edge/資料箱閘道服務。
- 會受到裝置密碼的任何時間保護。
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>透過啟用金鑰保護裝置

僅允許授權的資料箱閘道裝置加入您在 Azure 訂用帳戶中建立的資料箱閘道服務。 若要授權裝置，您必須使用啟用金鑰來啟動具有資料箱閘道服務的裝置。

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

如需詳細資訊，請參閱[取得啟用金鑰](data-box-gateway-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>透過密碼保護裝置

密碼可確保只有授權的使用者可以存取您的資料。 資料箱閘道裝置會在鎖定狀態下開機。

您可以：

- 透過瀏覽器連線至裝置的本機 web UI，然後提供密碼以登入裝置。
- 透過 HTTP 遠端連線至裝置的 PowerShell 介面。 預設會開啟遠端系統管理。 接著，您可以提供裝置密碼來登入裝置。 如需詳細資訊，請參閱[從遠端連線到您的資料箱閘道裝置](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本機 web UI 來[變更密碼](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 如果您變更密碼，請務必通知所有遠端存取使用者，使其不會發生登入問題。


## <a name="protect-your-data"></a>保護您的資料

本節說明可保護傳輸中和已儲存資料的資料箱閘道安全性功能。

### <a name="protect-data-at-rest"></a>保護靜態資料的安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>保護飛行中的資料

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>透過儲存體帳戶保護資料安全

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 輪替並定期[同步儲存體帳戶金鑰](data-box-gateway-manage-shares.md#sync-storage-keys)，以協助保護您的儲存體帳戶免于未經授權的使用者。

## <a name="manage-personal-information"></a>管理個人資訊

資料箱閘道服務會收集下列案例中的個人資訊：

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要查看可存取或刪除共用的使用者清單，請遵循[管理資料箱閘道上的共用](data-box-gateway-manage-shares.md)中的步驟。

如需詳細資訊，請參閱[信任中心](https://www.microsoft.com/trustcenter)的 Microsoft 隱私權原則。

## <a name="next-steps"></a>後續步驟

[部署您的資料箱閘道裝置](data-box-gateway-deploy-prep.md)
