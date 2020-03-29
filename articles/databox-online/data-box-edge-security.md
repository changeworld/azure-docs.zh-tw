---
title: Azure 資料框邊緣安全性 |微軟文檔
description: 描述保護 Azure 資料框邊緣設備、服務和本地和雲中資料的安全和隱私功能。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "69970891"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>安全性和資料保護

當您採用新技術時，安全性是一個主要問題，尤其是在該技術與機密或專有資料一起使用時。 Azure 資料框邊緣可説明您確保只有經過授權的實體才能查看、修改或刪除資料。

本文介紹了資料框邊緣安全功能，可説明保護每個解決方案元件及其中存儲的資料。

Azure 資料框邊緣由四個主要元件組成，這些元件彼此交互：

- **資料框邊緣服務，託管在 Azure 中**。 用於創建設備訂單、配置設備，然後跟蹤訂單完成的管理資源。
- **資料框邊緣設備**。 已發運給您的傳輸設備，以便您可以將本地資料導入 Azure。
- **連接到設備的用戶端/主機**。 基礎結構中連接到資料框邊緣設備的用戶端，其中包含需要保護的資料。
- **雲存儲**。 存儲資料的 Azure 雲平臺中的位置。 此位置通常是連結到您創建的資料框邊緣資源的存儲帳戶。

## <a name="data-box-edge-service-protection"></a>資料框邊緣服務保護

資料框邊緣服務是在 Azure 中託管的管理服務。 該服務用於配置和管理設備。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>資料框邊緣設備保護

資料框邊緣設備是一種本地設備，它通過在本地處理資料，然後將其發送到 Azure，説明轉換資料。 您的設備：

- 需要啟用金鑰才能訪問資料框邊緣服務。
- 隨時受裝置密碼保護。
- 是鎖定的設備。 設備 BMC 和 BIOS 受密碼保護。 BIOS 受受限使用者訪問的保護。
- 已啟用安全引導。
- 運行 Windows 防禦器設備保護。 Device Guard 允許您僅運行在代碼完整性策略中定義的受信任應用程式。

### <a name="protect-the-device-via-activation-key"></a>通過啟用金鑰保護設備

只有經過授權的資料框邊緣設備才能加入您在 Azure 訂閱中創建的資料框邊緣服務。 要授權設備，您需要使用啟用金鑰使用資料盒邊緣服務啟動設備。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

有關詳細資訊，請參閱[獲取啟用金鑰](data-box-edge-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>通過密碼保護設備

密碼確保只有經過授權的使用者才能訪問您的資料。 資料框邊緣設備處於鎖定狀態啟動。

您可以：

- 通過瀏覽器連接到設備的本地 Web UI，然後提供密碼以登錄到設備。
- 通過 HTTP 遠端連線到設備 PowerShell 介面。 預設情況下，遠端系統管理處於打開狀態。 然後，您可以提供裝置密碼以登錄到設備。 有關詳細資訊，請參閱[遠端連線到資料盒邊緣設備](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本地 Web UI[更改密碼](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access)。 如果更改密碼，請確保通知所有遠端存取使用者，以便他們在登錄時沒有問題。

## <a name="protect-your-data"></a>保護您的資料

本節介紹保護傳輸中和存儲資料的資料框邊緣安全功能。

### <a name="protect-data-at-rest"></a>保護靜態資料的安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256 位加密用於保護本地資料。


### <a name="protect-data-in-flight"></a>保護飛行中的資料

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>透過儲存體帳戶保護資料安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 定期旋轉並[同步存儲帳戶金鑰](data-box-edge-manage-shares.md#sync-storage-keys)，以説明保護您的存儲帳戶免受未經授權的使用者的攻擊。

## <a name="manage-personal-information"></a>管理個人資訊

資料框邊緣服務在以下情況下收集個人資訊：

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

要查看可以訪問或刪除共用的使用者清單，請按照["資料框邊緣"上的"管理共用"](data-box-edge-manage-shares.md)中的步驟操作。

有關詳細資訊，請查看[信任中心的](https://www.microsoft.com/trustcenter)Microsoft 隱私政策。

## <a name="next-steps"></a>後續步驟

[部署資料框邊緣設備](data-box-edge-deploy-prep.md)
