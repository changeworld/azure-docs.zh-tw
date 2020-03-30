---
title: Azure 設備預配服務資料通過客戶管理的金鑰進行靜態加密*微軟文檔
description: 使用客戶管理的金鑰組靜態資料進行加密，以便進行設備佈建服務
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675139"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>使用客戶管理的金鑰組靜態資料進行加密，以便進行設備佈建服務

## <a name="overview"></a>總覽

設備佈建服務 （DPS） 支援使用客戶管理的金鑰 （CMK）對靜態資料進行加密，也稱為自帶金鑰 （BYOK）。 DPS 提供靜態和傳輸中的資料加密。 預設情況下，DPS 使用 Microsoft 管理的金鑰組資料進行加密。 借助 CMK 支援，客戶現在可以選擇使用[Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)使用由客戶管理的金鑰加密金鑰來加密靜態資料。

此功能要求在以下區域之一（美國東部、美國西部 2 或美國中南部）創建新的 DPS。 要嘗試此功能，請通過[Microsoft 支援](https://azure.microsoft.com/support/create-ticket/)與我們聯繫。 聯繫 Microsoft 支援部門時，請共用公司名稱和訂閱 ID。

## <a name="next-steps"></a>後續步驟

* [瞭解有關設備預配服務的資訊](https://docs.microsoft.com/azure/iot-dps/)

* [瞭解有關 Azure 金鑰保存庫的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)