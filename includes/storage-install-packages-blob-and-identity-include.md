---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806572"
---
## <a name="install-client-library-packages"></a>安裝用戶端庫包

> [!NOTE]
> 此處顯示的示例使用 Azure 存儲用戶端庫版本 12。 版本 12 用戶端庫是 Azure SDK 的一部分。 有關 Azure SDK 的詳細資訊，請參閱[GitHub](https://github.com/Azure/azure-sdk)上的 Azure SDK 存儲庫。

要安裝 Blob 存儲包，請從 NuGet 包管理器主控台運行以下命令：

```powershell
Install-Package Azure.Storage.Blobs
```

此處顯示的示例還使用最新版本的[Azure 標識用戶端庫（用於 .NET）](https://www.nuget.org/packages/Azure.Identity/)使用 Azure AD 憑據進行身份驗證。 要安裝包，請從 NuGet 包管理器主控台運行以下命令：

```powershell
Install-Package Azure.Identity
```
