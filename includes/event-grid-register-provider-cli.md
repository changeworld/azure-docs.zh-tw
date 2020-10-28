---
title: 包含檔案
description: 包含檔案
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6c5ab187eb1307a95bbae4ff39d387221e094ea5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736867"
---
## <a name="enable-the-event-grid-resource-provider"></a>啟用事件方格資源提供者

如果您先前在 Azure 訂用帳戶中從未使用過事件方格，可能必須註冊事件方格資源提供者。 執行下列命令以註冊提供者：

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

可能需要一點時間才能完成註冊。 若要檢查狀態，請執行：

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

當 `registrationState` 是 `Registered` 時，代表已準備好繼續進行。
