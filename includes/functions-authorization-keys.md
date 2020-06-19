---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 926434d7110877e234888682cb6c946afe3ae685
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648909"
---
Functions 可讓您使用金鑰來提高開發期間存取 HTTP 函式端點的困難度。 除非 HTTP 觸發程序函數的 HTTP 存取層級設定為 `anonymous`，否則要求必須在要求中包含 API 存取金鑰。 

#### <a name="authorization-scopes-function-level"></a>授權範圍 (函式層級)

函式層級金鑰有兩個存取範圍：

* **Function**：這些金鑰僅適用於據以定義它們的特定函式。 當做為 API 金鑰使用時，這些金鑰僅允許存取該函式。

* **主機**：具有主機範圍的金鑰可用來存取函數應用程式內的所有函式。 當做為 API 金鑰使用時，這些金鑰會允許存取函數應用程式中的任何函式。 

每個金鑰均為具名以供參考，並且在函式和主機層級有一預設金鑰 (名稱為 "default")。 函式金鑰的優先順序高於主機金鑰。 當您使用相同的名稱來定義兩個金鑰時，一律會使用函式金鑰。

#### <a name="master-key-admin-level"></a>主要金鑰 (管理員層級) 

每個函數應用程式也有一個名為 `_master` 的管理員層級主機金鑰。 除了為應用程式中的所有函式提供主機層級存取之外，主要金鑰也會提供執行階段 REST API 的系統管理存取權。 無法撤銷此金鑰。 當您將存取層級設定為 `admin` 時，要求就必須使用主要金鑰；任何其他金鑰則會導致存取失敗。

> [!CAUTION]  
> 由於主要金鑰會在您的函數應用程式中授與提高的權限，因此您不應該與第三方共用此金鑰，或是在原生用戶端應用程式中散發它。 當您選擇管理存取層級時，請務必謹慎。
