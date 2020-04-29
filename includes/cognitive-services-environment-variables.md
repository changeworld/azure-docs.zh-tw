---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "70274599"
---
## <a name="configure-an-environment-variable-for-authentication"></a>設定環境變數以進行驗證

應用程式需要驗證其所使用之認知服務的存取權。 若要進行驗證，我們建議您建立環境變數來儲存 Azure 資源的金鑰。 

擁有金鑰之後，請在執行應用程式的本機電腦上，將它寫入新的環境變數。 若要設定環境變數，請開啟主控台視窗，並遵循您的作業系統所適用的指示。 將`your-key`取代為您資源的其中一個金鑰。

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