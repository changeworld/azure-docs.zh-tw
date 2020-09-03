---
title: Azure CLI 指令碼範例 - 發佈資產 | Microsoft Docs
description: 本文示範如何使用 Azure CLI 指令碼來發佈資產。
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
ms.openlocfilehash: 3b3c358a84dd74595c476f029a1c8f28bc3c901f
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295864"
---
# <a name="cli-example-publish-an-asset"></a>CLI 範例：發佈資產

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文中的 Azure CLI 指令碼會示範如何建立串流定位器，並取回串流 URL。 

## <a name="prerequisites"></a>Prerequisites 

[建立媒體服務帳戶](./create-account-howto.md)。

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>後續步驟

[媒體服務概觀](media-services-overview.md)
