---
title: Azure CLI 指令碼範例 - 重設您的帳戶認證 | Microsoft Docs
description: 使用 Azure CLI 指令碼來重設您的帳戶認證，並還原為 app.config 設定。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 63f2abe7a3890efbaf4c79186467a3eb20d8afda
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382982"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI 的範例：重設帳戶認證

本文中的 Azure CLI 指令碼會示範如何重設您的帳戶認證，並還原為 app.config 設定。

## <a name="prerequisites"></a>Prerequisites

[建立媒體服務帳戶](create-account-cli-how-to.md)。

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>範例指令碼

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>後續步驟

* [az ams](/cli/azure/ams)
* [重設認證](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
