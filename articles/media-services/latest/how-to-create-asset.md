---
title: 使用 Azure CLI 將內容上傳至 Azure 媒體服務資產
description: 本主題中的 Azure CLI 指令碼會示範如何建立內容上傳目標的媒體服務資產。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: aa5c9178bc84983b7e577cd63cf5b8d9fb9a8436
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89254656"
---
# <a name="create-an-asset"></a>建立資產

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文說明如何建立媒體服務資產。  您將使用資產來保存媒體內容，以進行編碼和串流處理。  若要深入了解媒體服務資產，請閱讀 [Azure 媒體服務 v3 中的資產](assets-concept.md)

## <a name="prerequisites"></a>必要條件

依照[建立媒體服務帳戶](./create-account-howto.md)中的步驟來建立所需的媒體服務帳戶和資源群組，以建立資產。

## <a name="methods"></a>方法

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>使用 REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>使用 cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="using-postman"></a>使用 Postman

[!INCLUDE[Create an asset with Postman](./includes/task-create-asset-postman.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>後續步驟

[媒體服務總覽](media-services-overview.md)
