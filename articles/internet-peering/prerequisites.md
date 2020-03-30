---
title: 設定 Microsoft 對等互連的必要條件
titleSuffix: Azure
description: 設定 Microsoft 對等互連的必要條件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775403"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>設定 Microsoft 對等互連的必要條件

在請求新的對等互連或將舊體對等互連轉換為 Azure 資源之前，請確保滿足以下先決條件。

## <a name="azure-related-prerequisites"></a>Azure 相關先決條件
* **微軟 Azure 帳戶：** 如果沒有 Microsoft Azure 帳戶，請創建 Microsoft [Azure 帳戶](https://azure.microsoft.com/free)。 需要有效和活動的 Microsoft Azure 訂閱來設置對等互連，因為對等互連建模為 Azure 訂閱中的資源。 要注意的重點是：
    * 用於設置對等互連的 Azure 資源類型始終免費 Azure 產品，即不收取創建 Azure 帳戶、創建訂閱或訪問 Azure 資源**對等處理**和**對等互連**的費用。 這與您和 Microsoft 之間直接對等互連的對等協定不相混淆，這些術語與我們的同儕小組進行了明確討論。 如果對此有任何疑問，請與[Microsoft 聯繫。](mailto:peering@microsoft.com)
    * 您可以使用相同的 Azure 訂閱訪問其他可能是免費的或付費的 Azure 產品或雲服務。 當您訪問付費產品時，您將產生費用。
    * 如果要創建新的 Azure 帳戶和/或訂閱，則在試用期間可能有資格獲得免費 Azure 積分，而試用 Azure 雲服務。 如果有興趣，請訪問[Microsoft Azure 帳戶](https://azure.microsoft.com/free)瞭解更多資訊。

* **關聯對等 ASN：** 在請求對等互連之前，請先將 ASN 和聯繫資訊與訂閱相關聯。 按照[將對等體 ASN 到 Azure 訂閱](howto-subscription-association-powershell.md)中的說明進行操作。

## <a name="other-prerequisites"></a>其他先決條件
* **對等資料庫設定檔：** 同行們有望在[對同儕DB](https://www.peeringdb.com)上擁有完整和最新的設定檔。 我們在註冊系統中使用這些資訊來驗證同行的詳細資訊，如 NOC 資訊、技術聯繫資訊及其在對等設施的存在等。

## <a name="next-steps"></a>後續步驟

* [使用門戶創建或修改直接對等互連](howto-direct-portal.md)。
* [使用入口網站將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)
* [使用門戶創建或修改 Exchange 對等互連](howto-exchange-portal.md)
* [使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源](howto-legacy-exchange-portal.md)