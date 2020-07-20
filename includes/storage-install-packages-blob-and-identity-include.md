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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74806572"
---
## <a name="install-client-library-packages"></a>安裝用戶端程式庫套件

> [!NOTE]
> 此處顯示的範例會使用 Azure 儲存體用戶端程式庫第12版。 版本12用戶端程式庫是 Azure SDK 的一部分。 如需 Azure SDK 的詳細資訊，請參閱[GitHub](https://github.com/Azure/azure-sdk)上的 Azure sdk 存放庫。

若要安裝 Blob 儲存體套件，請從 NuGet 套件管理員主控台執行下列命令：

```powershell
Install-Package Azure.Storage.Blobs
```

此處顯示的範例也會使用[適用于 .net 的 Azure 身分識別用戶端程式庫](https://www.nuget.org/packages/Azure.Identity/)的最新版本，以 Azure AD 認證進行驗證。 若要安裝套件，請從 NuGet 套件管理員主控台執行下列命令：

```powershell
Install-Package Azure.Identity
```
