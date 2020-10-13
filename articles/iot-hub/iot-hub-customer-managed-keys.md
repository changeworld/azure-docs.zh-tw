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
ms.openlocfilehash: 83d2fa59654e038586a7f23eedbe7c656873f35c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84976569"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>使用 IoT 中樞客戶管理的金鑰來加密待用資料

IoT 中樞支援使用客戶管理的金鑰來加密待用資料 (CMK) ，也稱為自備金鑰 (BYOK) 。 Azure IoT 中樞可加密待用和傳輸中的資料，因為它是寫入我們的資料中心，並在您存取它時將它解密。 根據預設，IoT 中樞會使用 Microsoft 管理的金鑰來加密待用資料。 透過 CMK，您可以在預設加密上取得另一層加密，並可選擇使用金鑰加密金鑰（透過您的 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)管理）來加密待用資料。 這可讓您彈性地建立、輪替、停用及撤銷存取控制。 如果已針對您的 IoT 中樞設定 BYOK，我們也會提供雙重加密，以提供第二層保護，並可讓您透過 Azure Key Vault 控制加密金鑰。

這項功能需要 (基本或標準層) 建立新的 IoT 中樞。 若要試用這項功能，請透過 [Microsoft 支援服務](https://azure.microsoft.com/support/create-ticket/)來洽詢我們。 聯繫 Microsoft 支援服務時，請共用您的公司名稱和訂用帳戶識別碼。


## <a name="next-steps"></a>接下來的步驟

* [深入瞭解 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
