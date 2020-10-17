---
title: 透過客戶管理的金鑰 Azure IoT 中樞待用資料加密 |Microsoft Docs
description: 使用 IoT 中樞客戶管理的金鑰來加密待用資料
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 352da24b36124ff0446a81c1ecbc584da545bb16
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142207"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>使用 IoT 中樞客戶管理的金鑰來加密待用資料

IoT 中樞支援使用客戶管理的金鑰來加密待用資料 (CMK) ，也稱為自備金鑰 (BYOK) 。 Azure IoT 中樞可加密待用和傳輸中的資料，因為它是寫入我們的資料中心，並在您存取它時將它解密。 根據預設，IoT 中樞會使用 Microsoft 管理的金鑰來加密待用資料。 透過 CMK，您可以在預設加密上取得另一層加密，並可選擇使用金鑰加密金鑰（透過您的 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)管理）來加密待用資料。 這可讓您彈性地建立、輪替、停用及撤銷存取控制。 如果已針對您的 IoT 中樞設定 BYOK，我們也會提供雙重加密，以提供第二層保護，並可讓您透過 Azure Key Vault 控制加密金鑰。

這項功能需要 (基本或標準層) 建立新的 IoT 中樞。 若要試用這項功能，請透過 [Microsoft 支援服務](https://azure.microsoft.com/support/create-ticket/)來洽詢我們。 聯繫 Microsoft 支援服務時，請共用您的公司名稱和訂用帳戶識別碼。


## <a name="next-steps"></a>後續步驟

* [深入瞭解 IoT 中樞](./about-iot-hub.md)

* [深入瞭解 Azure Key Vault](../key-vault/general/overview.md)