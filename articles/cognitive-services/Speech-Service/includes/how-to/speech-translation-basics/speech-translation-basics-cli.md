---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a5796dfb066ea8ac7c59c02b29ef67b184c67edb
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425121"
---
語音服務的其中一項核心功能是能夠辨識人類語音，並將它轉譯成其他語言。 在本快速入門中，您將瞭解如何在您的應用程式和產品中使用語音 SDK，以執行高品質的語音翻譯。 本快速入門會將來自麥克風的語音轉譯成另一種語言的文字。

## <a name="prerequisites"></a>Prerequisites

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>設定來源和目的語言

此命令會呼叫「語音 CLI」，以從義大利文將語音轉譯成法文。

```shell
 spx translate --microphone --source it-IT --target fr
```