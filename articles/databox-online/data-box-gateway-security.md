---
title: Azure 資料盒閘道安全性 |微軟文檔
description: 描述保護 Azure 資料盒閘道虛擬裝置、服務和資料、本地和雲中的安全和隱私功能。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900609"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure 資料盒閘道安全和資料保護

當您採用新技術時，安全性是一個主要問題，尤其是在該技術與機密或專有資料一起使用時。 Azure 資料框閘道可説明您確保只有經過授權的實體才能查看、修改或刪除資料。

本文介紹了 Azure 資料盒閘道安全功能，可説明保護每個解決方案元件及其中存儲的資料。

資料盒閘道解決方案由四個主要元件組成，這些元件相互交互：

- **資料框閘道服務，託管在 Azure 中**。 用於創建設備訂單、配置設備，然後跟蹤訂單完成的管理資源。
- **資料盒閘道設備**。 在提供的系統的虛擬機器管理程式中預配的虛擬裝置。 此虛擬裝置用於將本地資料導入 Azure。
- **連接到設備的用戶端/主機**。 基礎結構中連接到資料盒閘道設備的用戶端，其中包含需要保護的資料。
- **雲存儲**。 存儲資料的 Azure 雲平臺中的位置。 此位置通常是連結到您創建的資料框閘道資源的存儲帳戶。


## <a name="data-box-gateway-service-protection"></a>資料盒閘道服務保護

資料盒閘道服務是在 Azure 中託管的管理服務。 該服務用於配置和管理設備。

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>資料盒閘道設備保護

資料盒閘道設備是一種虛擬裝置，在您提供的本地系統的虛擬機器管理程式中預配。 該設備有助於將資料發送到 Azure。 您的設備：

- 需要啟用金鑰才能訪問資料框邊緣/資料盒閘道服務。
- 隨時受裝置密碼保護。
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>通過啟用金鑰保護設備

只有經過授權的資料盒閘道設備才能加入您在 Azure 訂閱中創建的資料盒閘道服務。 要授權設備，您需要使用啟用金鑰使用資料盒閘道服務啟動設備。

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

有關詳細資訊，請參閱[獲取啟用金鑰](data-box-gateway-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>通過密碼保護設備

密碼確保只有經過授權的使用者才能訪問您的資料。 資料框閘道設備處於鎖定狀態啟動。

您可以：

- 通過瀏覽器連接到設備的本地 Web UI，然後提供密碼以登錄到設備。
- 通過 HTTP 遠端連線到設備的 PowerShell 介面。 預設情況下，遠端系統管理處於打開狀態。 然後，您可以提供裝置密碼以登錄到設備。 有關詳細資訊，請參閱[遠端連線到資料盒閘道設備](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本地 Web UI[更改密碼](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 如果更改密碼，請確保通知所有遠端存取使用者，以便他們在登錄時沒有問題。


## <a name="protect-your-data"></a>保護您的資料

本節介紹保護傳輸中和存儲資料的資料盒閘道安全功能。

### <a name="protect-data-at-rest"></a>保護靜態資料的安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>保護飛行中的資料

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>透過儲存體帳戶保護資料安全

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 定期旋轉並[同步存儲帳戶金鑰](data-box-gateway-manage-shares.md#sync-storage-keys)，以説明保護您的存儲帳戶免受未經授權的使用者的攻擊。

## <a name="manage-personal-information"></a>管理個人資訊

資料盒閘道服務在以下情況下收集個人資訊：

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

要查看可以訪問或刪除共用的使用者清單，請按照["資料框閘道"上的管理共用](data-box-gateway-manage-shares.md)中的步驟操作。

有關詳細資訊，請查看[信任中心的](https://www.microsoft.com/trustcenter)Microsoft 隱私政策。

## <a name="next-steps"></a>後續步驟

[部署資料盒閘道設備](data-box-gateway-deploy-prep.md)
