---
title: 我的 ASP.NET 專案發生什麼情形？
description: 描述使用 Visual Studio 連接的服務將 Azure 存儲添加到ASP.NET專案後會發生什麼情況
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4374955d6d51c1ae44c211aaa93d0b5e8930fe5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300050"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>我的 ASP.NET 專案 (Visual Studio Azure 儲存體連接的服務) 發生什麼狀況？
## <a name="references-added"></a>加入參考
Azure 儲存體 NuGet 封裝已加入至 Visual Studio 專案。  
這個封裝會加入下列 .NET 參考：

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **系統.資料**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>加入 Azure 儲存體的連接字串
在專案的 web.config 檔案中，已使用所選儲存體帳戶的連接字串和金鑰建立一個元素。

如需詳細資訊，請參閱 [ASP.NET](https://www.asp.net)。

