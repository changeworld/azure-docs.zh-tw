---
title: 如何尋找您的 Azure 訂用帳戶
description: 尋找您的 Azure 訂用帳戶，讓您可以設定您的環境。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: cli,portal
ms.openlocfilehash: 8924b77cddc9efc4d2b1b8451df38de77b37c09c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267348"
---
# <a name="find-your-azure-subscription"></a>尋找您的 Azure 訂用帳戶

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[入口網站](#tab/portal/)

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 [Azure 服務] 標題下，選取 [訂閱]。  (如果未列出任何訂用帳戶，您可能需要切換 Azure AD 的租使用者。 ) 您的訂用帳戶識別碼會列在第二個數據行中。
1. 複製訂用帳戶識別碼，並將它貼到您選擇的文字檔中，以供稍後使用。

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>使用 Azure CLI

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the "task-" prepended to the file name. -->

### <a name="list-your-azure-subscriptions-with-cli"></a>使用 CLI 列出您的 Azure 訂用帳戶

[!INCLUDE [List your Azure subscriptions with CLI](./includes/task-list-set-subscription-cli.md)]

### <a name="see-also"></a>另請參閱

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)

---

## <a name="next-steps"></a>後續步驟

[串流處理檔案](stream-files-dotnet-quickstart.md)
