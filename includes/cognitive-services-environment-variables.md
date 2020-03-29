---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "70274599"
---
## <a name="configure-an-environment-variable-for-authentication"></a>配置用於身份驗證的環境變數

應用程式需要驗證對它們使用的認知服務的存取權限。 要進行身份驗證，我們建議創建一個環境變數來存儲 Azure Resources 的鍵。 

獲得金鑰後，將其寫入運行應用程式的本地電腦上的新環境變數。 若要設定環境變數，請開啟主控台視窗，並遵循您的作業系統所適用的指示。 替換為`your-key`資源中的一個鍵。

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

新增環境變數之後，您可能需要重新啟動任何需要讀取環境變數的執行中程式，包括主控台視窗。 例如，如果您使用 Visual Studio 做為編輯器，請在執行範例前重新啟動 Visual Studio。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

新增環境變數之後，從主控台視窗執行 `source ~/.bashrc`，讓變更生效。

#### <a name="macos"></a>[macOS](#tab/unix)

編輯 .bash_profile，然後新增環境變數：

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

新增環境變數之後，從主控台視窗執行 `source .bash_profile`，讓變更生效。

***