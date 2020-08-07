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
ms.openlocfilehash: b48e5e28b15d17796138d493a7d9c628791df2e6
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830112"
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