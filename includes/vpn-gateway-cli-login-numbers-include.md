---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 52f828b9ba7bd286184b44a3d843c2cf8c75c592
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755391"
---
1. 使用 [az login](/cli/azure/) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。 如需關於登入的詳細資訊，請參閱[開始使用 Azure CLI](/cli/azure/get-started-with-azure-cli)。

   ```azurecli
   az login
   ```
2. 如果您有多個 Azure 訂用帳戶，請列出帳戶的所有訂用帳戶。

   ```azurecli
   az account list --all
   ```
3. 指定您要使用的訂用帳戶。

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
