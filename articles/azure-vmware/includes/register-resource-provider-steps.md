---
title: 註冊 Azure VMware 解決方案資源提供者
description: 註冊 Azure VMware 解決方案資源提供者的步驟。
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512351"
---
若要使用 Azure VMware 解決方案，您必須先向您的訂用帳戶註冊資源提供者。

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

如需註冊資源提供者的其他方式，請參閱 [Azure 資源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md)。