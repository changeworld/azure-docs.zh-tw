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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75775403"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>設定 Microsoft 對等互連的必要條件

在您要求新的對等互連或將舊版對等互連轉換成 Azure 資源之前，請先確定符合下列必要條件。

## <a name="azure-related-prerequisites"></a>Azure 相關的必要條件
* **Microsoft Azure 帳戶：** 如果您沒有 Microsoft Azure 帳戶，請建立[Microsoft Azure 帳戶](https://azure.microsoft.com/free)。 若要設定對等互連，您必須具備有效且作用中的 Microsoft Azure 訂用帳戶，因為對等互連會模型化為 Azure 訂用帳戶內的資源。 要注意的重點是：
    * 用來設定對等互連的 Azure 資源類型一律是免費的 Azure 產品，亦即，您不需支付建立 Azure 帳戶或建立訂用帳戶，或存取 Azure 資源**PeerAsn**和對等**互連**來設定對等互連。 這不會與您和 Microsoft 之間的直接對等互連協定混淆，這是與我們的對等小組明確討論的詞彙。 如有任何問題，請洽詢[Microsoft 對等互連](mailto:peering@microsoft.com)。
    * 您可以使用相同的 Azure 訂用帳戶來存取其他可免費或付費的 Azure 產品或雲端服務。 當您存取付費產品時，將會產生費用。
    * 如果您要建立新的 Azure 帳戶和/或訂用帳戶，您可能會在試用期間享有免費 Azure 點數的資格，您可以在此期間利用來試用 Azure 雲端服務。 如有興趣，請造訪[Microsoft Azure 帳戶](https://azure.microsoft.com/free)以取得詳細資訊。

* **關聯對等 ASN：** 要求對等互連之前，請先將您的 ASN 和連絡人資訊關聯至您的訂用帳戶。 依照[將對等 ASN 與 Azure 訂用帳戶建立關聯](howto-subscription-association-powershell.md)中的指示進行。

## <a name="other-prerequisites"></a>其他必要條件
* **PeeringDB 設定檔：** 對等應該在[PeeringDB](https://www.peeringdb.com)上有完整且最新的設定檔。 我們會在我們的註冊系統中使用這項資訊來驗證對等的詳細資料，例如 NOC 資訊、技術連絡人資訊，以及其在對等互連設施中的狀態等。

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)。
* [使用入口網站將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)
* [使用入口網站建立或修改 Exchange 對等互連](howto-exchange-portal.md)
* [使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源](howto-legacy-exchange-portal.md)