---
title: Azure 裝置布建服務資料透過客戶管理的金鑰進行待用加密 |Microsoft Docs
description: 使用裝置布建服務的客戶管理金鑰來加密待用資料
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: e9b5a549d550f117e080fc64193fbe29658f401d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85298220"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>使用裝置布建服務的客戶管理金鑰來加密待用資料

## <a name="overview"></a>概觀

裝置布建服務 (DPS) 支援使用客戶管理的金鑰來加密待用資料 (CMK) ，也就是所謂的「攜帶您自己的金鑰」 (BYOK) 。 DPS 提供加密待用和傳輸中的資料，因為它是寫入我們的資料中心，並在您存取時將其解密。 根據預設，DPS 會使用 Microsoft 管理的金鑰來加密待用資料。 透過 CMK，您可以選擇使用金鑰加密金鑰來加密待用資料（透過您的 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)管理），以在預設平臺加密之上取得額外的加密層級。 這可讓您彈性地建立、輪替、停用及撤銷金鑰。 如果已針對您的 DPS 設定 CMK，即表示已啟用雙重加密，這兩個層級的保護會主動保護您的資料。 

這項功能需要建立新的 DPS。 若要試用這項功能，請透過 [Microsoft 支援服務](https://azure.microsoft.com/support/create-ticket/)來洽詢我們。 聯繫 Microsoft 支援服務時，請共用您的公司名稱和訂用帳戶識別碼。


## <a name="next-steps"></a>後續步驟

* [深入瞭解裝置布建服務](https://docs.microsoft.com/azure/iot-dps/)

* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
