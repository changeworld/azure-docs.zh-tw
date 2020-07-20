---
title: 包含檔案
description: 包含檔案
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ace22a6896a121f5cd8af838c7b0e427bd0287dc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "67173661"
---
## <a name="enable-event-grid-resource-provider"></a>啟用事件格線資源提供者

如果您先前在 Azure 訂用帳戶中從未使用過事件方格，可能必須註冊事件方格資源提供者。 執行下列命令以註冊提供者：

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

可能需要一點時間才能完成註冊。 若要檢查狀態，請執行：

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

當 `registrationState` 是 `Registered` 時，代表已準備好繼續進行。