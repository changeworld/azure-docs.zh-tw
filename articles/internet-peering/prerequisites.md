---
title: 設定 Microsoft 對等互連的必要條件
titleSuffix: Azure
description: 設定 Microsoft 對等互連的必要條件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bc45bc8809eabe75902b602835ea595b56ff3cf8
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586835"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>設定 Microsoft 對等互連的必要條件

在您要求新的對等互連或將舊版對等互連轉換為 Azure 資源之前，請確定符合下列必要條件。

## <a name="azure-related-prerequisites"></a>Azure 相關的必要條件
* **Microsoft Azure 帳戶：** 如果您沒有 Microsoft Azure 帳戶，請建立 [Microsoft Azure 帳戶](https://azure.microsoft.com/free)。 因為對等互連會模型化為 Azure 訂用帳戶內的資源，所以設定對等互連時，必須具備有效且有效的 Microsoft Azure 訂用帳戶。 要注意的重點是：
    * 用來設定對等互連的 Azure 資源類型一律是免費的 Azure 產品，亦即，您不需支付建立 Azure 帳戶或建立訂用帳戶，或存取 Azure 資源 **PeerAsn** 和對等 **互連** 來設定對等互連。 這不會與您與 Microsoft 之間直接對等互連的對等互連協定混淆，而是與我們的對等互連團隊明確討論的條款。 如果有任何問題，請洽詢 [Microsoft 對等互連](mailto:peering@microsoft.com) 。
    * 您可以使用相同的 Azure 訂用帳戶來存取可免費或付費的其他 Azure 產品或雲端服務。 當您存取付費產品時，將會產生費用。
    * 如果您要建立新的 Azure 帳戶和/或訂用帳戶，您可能會在試用期間享有免費的 Azure 點數，您可能會利用這些帳戶來試用 Azure 雲端服務。 如果有興趣，請造訪 [Microsoft Azure 帳戶](https://azure.microsoft.com/free) 以取得詳細資訊。

* **將對等 ASN：** 要求對等互連之前，請先將您的 ASN 和連絡人資訊與您的訂用帳戶產生關聯。 遵循 [將對等 ASN 與 Azure 訂用帳戶建立關聯](howto-subscription-association-powershell.md)的指示。

## <a name="other-prerequisites"></a>其他必要條件
* **PeeringDB 設定檔：** 對等應該會在 [PeeringDB](https://www.peeringdb.com)上擁有完整且最新的設定檔。 我們在註冊系統中使用這項資訊來驗證對等的詳細資料，例如 NOC 資訊、技術連絡人資訊，以及它們在對等互連設備上的存在等。

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)。
* [使用入口網站將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)
* [使用入口網站建立或修改 Exchange 對等互連](howto-exchange-portal.md)
* [使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源](howto-legacy-exchange-portal.md)