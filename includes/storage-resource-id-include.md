---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68249933"
---
Azure AD 資源識別碼 指示可用於提供對 Azure 資源的訪問的權杖的受眾。 在 Azure 存儲的情況下，資源識別碼 可能特定于單個存儲帳戶，也可以應用於任何存儲帳戶。 下表描述了可以為資源識別碼 提供的值：

|資源識別碼  |描述  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | 給定存儲帳戶的服務終結點。 使用此值獲取權杖，以便僅授權對該特定 Azure 存儲帳戶和服務的請求。 將括弧中的值替換為存儲帳戶的名稱。      |
|`https://storage.azure.com/`     | 用於獲取授權請求到任何 Azure 存儲帳戶的權杖。        |
