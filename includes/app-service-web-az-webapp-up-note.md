---
title: 包含檔案
description: 包含檔案
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 08458bd170707b28c69fdad1d8aa115a7ad245a5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95564293"
---
> [!NOTE]
> `az webapp up` 命令會執行下列動作：
>
>- 建立預設[資源群組](/cli/azure/group?view=azure-cli-latest#az-group-create)。
>
>- 建立預設 [App Service 方案](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)。
>
>- 使用所指定的名稱[建立應用程式](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)。
>
>- [以 Zip 檔進行部署](../articles/app-service/deploy-zip.md)，將目前工作目錄中的檔案部署到應用程式。
>