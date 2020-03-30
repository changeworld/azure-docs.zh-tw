---
title: Azure IoT 中心資料通過客戶管理的金鑰進行靜態加密*微軟文檔
description: 使用 IoT 中心的客戶託管金鑰組靜態資料進行加密
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370571"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>使用 IoT 中心的客戶託管金鑰組靜態資料進行加密

IoT 中心使用客戶管理的金鑰 （CMK）支援靜態資料加密，也稱為自帶金鑰 （BYOK），支援 Azure IoT 中心。 Azure IoT 中心提供靜態和傳輸中的資料加密。 預設情況下，IoT 中心使用 Microsoft 管理的金鑰組資料進行加密。 借助 CMK 支援，客戶現在可以選擇使用[Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)使用由客戶管理的金鑰加密金鑰來加密靜態資料。

此功能需要在以下區域之一（美國東部、美國西部 2、美國中南部或美國政府）創建新的 IoT 中心（基本或標準層）。 要嘗試此功能，請通過[Microsoft 支援](https://azure.microsoft.com/support/create-ticket/)與我們聯繫。 聯繫 Microsoft 支援部門時，請共用公司名稱和訂閱 ID。

## <a name="next-steps"></a>後續步驟

* [瞭解有關 IoT 中心](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [瞭解有關 Azure 金鑰保存庫的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
