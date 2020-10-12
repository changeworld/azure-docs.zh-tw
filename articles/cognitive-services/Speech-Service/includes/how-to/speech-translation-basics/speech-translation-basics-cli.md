---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: f28dd3e94db9d16645bf0d63841a17ee66defd7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776644"
---
語音服務的其中一項核心功能是能夠辨識人類語音，並將它轉譯成其他語言。 在本快速入門中，您將瞭解如何在您的應用程式和產品中使用語音 SDK，以執行高品質的語音翻譯。 本快速入門會將來自麥克風的語音轉譯成另一種語言的文字。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../get-started.md)。

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>設定來源和目的語言

此命令會呼叫「語音 CLI」，以從義大利文將語音轉譯成法文。

```shell
 spx translate --microphone --source it-IT --target fr
```
