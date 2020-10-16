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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "68249933"
---
Azure AD 資源識別碼會指出已發出權杖的對象可以用來提供 Azure 資源的存取權。 在 Azure 儲存體的情況下，資源識別碼可能專屬於單一儲存體帳戶，也可能適用於任何儲存體帳戶。 下表描述您可以為資源識別碼提供的值：

|資源識別碼  |描述  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | 指定儲存體帳戶的服務端點。 使用此值來取得權杖，以將要求僅授權給該特定 Azure 儲存體帳戶和服務。 使用您的儲存體帳戶名稱取代括弧中的值。      |
|`https://storage.azure.com/`     | 用來取得權杖，以將要求授權給任何 Azure 儲存體帳戶。        |
