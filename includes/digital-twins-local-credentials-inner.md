---
author: baanders
description: 包含用來在 Azure Digital Twins 範例中為 DefaultAzureCredential 設定本機驗證的檔案 (不包含簡介)
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 3cb053f9673532ac19e2098678ec341f2f676486
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329469"
---
使用 `DefaultAzureCredential`，該範例會搜尋本機環境中的認證，例如在本機 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 中或在 Visual Studio 或 Visual Studio Code 中的 Azure 登入。 因此，您應該透過下列其中一種機制 *本機登入 Azure* ，以設定該範例的認證。

如果您使用 Visual Studio 或 Visual Studio Code 來執行程式碼範例，請確定使用要用來存取 Azure Digital Twins 執行個體相同的 Azure 認證來登入該編輯器。

否則，您可以[安裝本機 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)，在電腦上啟動命令提示字元，然後執行 `az login` 命令以登入您的 Azure 帳戶。 登入之後，當您執行程式碼範例時，應該會自動登入。