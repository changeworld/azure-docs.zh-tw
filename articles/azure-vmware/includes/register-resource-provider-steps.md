---
title: 註冊 Azure VMware 解決方案資源提供者
description: 註冊 Azure VMware 解決方案資源提供者的步驟。
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 96d15546c5102a69a0b19f92de33d35d2e9ab6c2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575736"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

若要使用 Azure VMware 解決方案，您必須先向您的訂用帳戶註冊資源提供者。

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>或者，您可以使用 GUI 來註冊 **Microsoft.AVS** 資源提供者。  如需詳細資訊，請參閱[註冊資源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)一文。  
