---
title: Azure 資料箱閘道安全性 |Microsoft Docs
description: 描述保護您的 Azure 資料箱閘道虛擬裝置、服務和資料、內部部署和雲端中的安全性和隱私權功能。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 45506b87bf13b44e035655dbdf0fac03ea50612c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90893862"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure 資料箱閘道安全性和資料保護

當您採用新的技術時，安全性是一項重要的考慮，特別是當該技術與機密或專屬的資料搭配使用時。 Azure 資料箱閘道可協助您確保只有授權的實體可以查看、修改或刪除您的資料。

本文說明 Azure 資料箱閘道安全性功能，可協助保護每個解決方案元件和其中儲存的資料。

資料箱閘道解決方案包含四個彼此互動的主要元件：

- **Azure 中託管的資料箱閘道服務**。 您用來建立裝置訂單的管理資源、設定裝置，然後追蹤訂單的完成。
- **資料箱閘道裝置**。 您在提供的系統虛擬程式中布建的虛擬裝置。 此虛擬裝置可用來將您的內部部署資料匯入 Azure。
- **連線到裝置的用戶端/主機**。 基礎結構中的用戶端，可連線至資料箱閘道裝置，並包含需要保護的資料。
- **雲端儲存體**。 Azure 雲端平臺中儲存資料的位置。 此位置通常是連結至您所建立之資料箱閘道資源的儲存體帳戶。

## <a name="data-box-gateway-service-protection"></a>資料箱閘道服務保護

資料箱閘道服務是在 Azure 中託管的管理服務。 此服務可用來設定和管理裝置。

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>資料箱閘道裝置保護

資料箱閘道裝置是在您提供的內部部署系統的虛擬程式中布建的虛擬裝置。 裝置有助於將資料傳送至 Azure。 您的裝置：

- 需要啟用金鑰才能存取 Azure Stack Edge Pro/Data Box Gateway 服務。
- 會受到裝置密碼的任何時間保護。
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>透過啟用金鑰保護裝置

只有授權的資料箱閘道裝置可以加入您在 Azure 訂用帳戶中建立的資料箱閘道服務。 若要授權裝置，您必須使用啟用金鑰以使用資料箱閘道服務來啟用裝置。

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

如需詳細資訊，請參閱 [取得啟用金鑰](data-box-gateway-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>透過密碼保護裝置

密碼可確保只有經過授權的使用者可以存取您的資料。 資料箱閘道裝置會在鎖定狀態下開機。

您可以：

- 透過瀏覽器連線至裝置的本機 web UI，然後提供密碼以登入裝置。
- 透過 HTTP 從遠端連線到裝置的 PowerShell 介面。 預設會開啟遠端系統管理。 接著，您可以提供裝置密碼以登入裝置。 如需詳細資訊，請參閱 [從遠端連線到您的資料箱閘道裝置](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本機 web UI 來 [變更密碼](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 如果您變更密碼，請務必通知所有遠端存取使用者，讓他們不會在登入時發生問題。

## <a name="protect-your-data"></a>保護您的資料

本節說明可保護傳輸中和儲存資料的資料箱閘道安全性功能。

### <a name="protect-data-at-rest"></a>保護靜態資料的安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>保護傳輸中的資料

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>使用儲存體帳戶保護資料

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]

- 定期輪替並 [同步處理您的儲存體帳戶金鑰](data-box-gateway-manage-shares.md#sync-storage-keys) ，以協助保護您的儲存體帳戶免于未經授權的使用者。

### <a name="protect-the-device-data-using-bitlocker"></a>使用 BitLocker 保護裝置資料

若要保護資料箱閘道虛擬機器上的虛擬磁片，建議您啟用 BitLocker。 根據預設，不會啟用 BitLocker。 如需詳細資訊，請參閱

- [Hyper-V 管理員中的加密支援設定](hhttps://docs.microsoft.com/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [虛擬機器中的 BitLocker 支援](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>管理個人資訊

在下列案例中，資料箱閘道服務會收集個人資訊：

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要查看可存取或刪除共用的使用者清單，請依照在 [資料箱閘道上管理共用](data-box-gateway-manage-shares.md)中的步驟執行。

如需詳細資訊，請參閱 [信任中心](https://www.microsoft.com/trustcenter)上的 Microsoft 隱私權原則。

## <a name="next-steps"></a>下一步

[部署資料箱閘道裝置](data-box-gateway-deploy-prep.md)
