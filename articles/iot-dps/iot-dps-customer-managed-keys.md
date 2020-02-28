---
title: Azure 裝置布建服務透過客戶管理的金鑰進行待用資料加密 |Microsoft Docs
description: 使用裝置布建服務的客戶管理金鑰加密待用資料
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77675139"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>使用裝置布建服務的客戶管理金鑰加密待用資料

## <a name="overview"></a>概觀

「裝置布建服務」（DPS）支援使用客戶管理的金鑰（CMK）加密待用資料，也稱為「自備金鑰」（BYOK）。 DPS 提供待用和傳輸中資料的加密。 根據預設，DPS 會使用 Microsoft 管理的金鑰來加密資料。 有了 CMK 支援，客戶現在可以選擇使用[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)，以客戶所管理的金鑰加密金鑰來加密待用資料。

這項功能需要在下列其中一個區域中建立新的 DPS：美國東部、美國西部2或美國中南部。 若要試用這項功能，請透過[Microsoft 支援服務](https://azure.microsoft.com/support/create-ticket/)洽詢我們。 聯繫 Microsoft 支援服務時，請共用您的公司名稱和訂用帳戶識別碼。

## <a name="next-steps"></a>後續步驟

* [深入瞭解裝置布建服務](https://docs.microsoft.com/azure/iot-dps/)

* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)