---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482113"
---
1. 確定您具有[有效訂用帳戶的 Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

1. 安裝 [Python 2.7+ 或 3.5.3+](https://www.python.org/downloads)。

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli)。

1. 遵循[設定本機開發驗證](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication)以建立本機服務主體，並透過環境變數將其提供給適用於 Python 的 Azure Key Vault 用戶端。 

    直接在 Azure 上執行程式碼時，如果應用程式使用受控識別，則不需要個別的服務主體。

1. 在終端機或命令提示字元中，建立適當的專案資料夾，然後建立並啟動 Python 虛擬環境，如[使用 Python 虛擬環境](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)中所述

1. 安裝 Azure Active Directory 身分識別程式庫：

    ```terminal
    pip install azure.identity
    ```
