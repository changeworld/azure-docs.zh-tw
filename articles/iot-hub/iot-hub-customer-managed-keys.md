---
title: 透過客戶管理的金鑰 Azure IoT 中樞待用資料加密 |Microsoft Docs
description: 使用客戶管理的金鑰來加密待用資料，以進行 IoT 中樞
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370571"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>使用客戶管理的金鑰來加密待用資料，以進行 IoT 中樞

IoT 中樞支援使用客戶管理的金鑰（CMK）來加密待用資料，也稱為「自備金鑰」（BYOK），支援 Azure IoT 中樞。 Azure IoT 中樞提供待用和傳輸中資料的加密。 根據預設，IoT 中樞會使用 Microsoft 管理的金鑰來加密資料。 有了 CMK 支援，客戶現在可以選擇使用[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)，以客戶所管理的金鑰加密金鑰來加密待用資料。

這項功能需要在下列其中一個區域中建立新的 IoT 中樞（基本或標準層）：美國東部、美國西部2、美國中南部或 US Gov。 若要試用這項功能，請透過[Microsoft 支援服務](https://azure.microsoft.com/support/create-ticket/)洽詢我們。 聯繫 Microsoft 支援服務時，請共用您的公司名稱和訂用帳戶識別碼。

## <a name="next-steps"></a>後續步驟

* [深入瞭解 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
