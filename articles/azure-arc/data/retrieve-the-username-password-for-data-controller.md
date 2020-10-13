---
title: 擷取使用者名稱和密碼以連線到 Arc 資料控制器
description: 擷取使用者名稱和密碼以連線到 Arc 資料控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 8cabb48e3620f1a17d1bb9b87e1646ce2793143b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761697"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>擷取使用者名稱和密碼以連線到 Arc 資料控制器

您可能會遇到需要取出資料控制器之使用者名稱和密碼的情況。 這些是您執行時所需的命令。 

```console
azdata login
```

如果您是叢集的 Kubernetes 系統管理員。 如此一來，您就可以執行命令以從 Kubernetes 秘密中取出，以儲存 Azure Arc 保存在該處的資訊。

> [!NOTE]
>  如果您針對建立資料控制器的命名空間使用不同的名稱，請務必變更 `-n arc` 下列命令中的參數，以使用您建立資料控制器的命名空間名稱。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

執行下列命令來取出使用者名稱：

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

執行下列命令來取出密碼：

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

執行下列命令來取出使用者名稱：

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

執行下列命令來取出密碼：

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>後續步驟

試用其他 [案例](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md)
