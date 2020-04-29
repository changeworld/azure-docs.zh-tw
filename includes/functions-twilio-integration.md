---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67173780"
---
這個範例牽涉到使用 [Twilio](https://www.twilio.com/) 服務來將 SMS 訊息傳送至行動電話。 Azure Functions 已透過 [Twilio 繫結](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)支援 Twilio，範例會使用該功能。

首先您需要的是 Twilio 帳戶。 您可以在 https://www.twilio.com/try-twilio 免費建立一個帳戶。 一旦您擁有帳戶，將下列三個**應用程式設定**新增至您的函式應用程式。

| 應用程式設定名稱 | 值說明 |
| - | - |
| **TwilioAccountSid**  | Twilio 帳戶的 SID |
| **TwilioAuthToken**   | Twilio 帳戶的驗證權杖 |
| **TwilioPhoneNumber** | 與您的 Twilio 帳戶相關聯的電話號碼。 這是用來傳送 SMS 訊息。 |