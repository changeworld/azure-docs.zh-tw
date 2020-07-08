---
title: 透過客戶管理的金鑰 Azure IoT 中樞待用資料加密 |Microsoft Docs
description: 使用客戶管理的金鑰來加密待用資料，以進行 IoT 中樞
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 83d2fa59654e038586a7f23eedbe7c656873f35c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976569"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>使用客戶管理的金鑰來加密待用資料，以進行 IoT 中樞

IoT 中樞支援使用客戶管理的金鑰（CMK）來加密待用資料，也稱為「自備金鑰」（BYOK）。 Azure IoT 中樞提供待用和傳輸中資料的加密，因為它是在資料中心內寫入，並會在您存取它時將其解密。 根據預設，IoT 中樞會使用 Microsoft 管理的金鑰來加密待用資料。 有了 CMK，您可以在預設加密之上取得另一層加密，並可選擇使用金鑰加密金鑰來加密待用資料，並透過您的[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)管理。 這可讓您彈性地建立、輪替、停用及撤銷存取控制。 如果已針對您的 IoT 中樞設定 BYOK，我們也會提供雙重加密，以提供第二層保護，同時可讓您透過 Azure Key Vault 控制加密金鑰。

這項功能需要建立新的 IoT 中樞（基本或標準層）。 若要試用這項功能，請透過[Microsoft 支援服務](https://azure.microsoft.com/support/create-ticket/)洽詢我們。 聯繫 Microsoft 支援服務時，請共用您的公司名稱和訂用帳戶識別碼。


## <a name="next-steps"></a>後續步驟

* [深入瞭解 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
