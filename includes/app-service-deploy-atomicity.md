---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75945167"
---
## <a name="what-happens-to-my-app-during-deployment"></a>在部署期間我的應用程式會發生什麼事？

所有官方支援的部署方法都對應用`/home/site/wwwroot`資料夾中的檔進行更改。 這些檔用於運行你的應用。 因此，部署可能會因為檔鎖定而失敗。 應用在部署期間也可能不可預測性，因為並非所有檔同時更新。 這對面向客戶的應用來說不可取。 有幾種不同的方式可以避免這些問題：

- [直接從 ZIP 包運行應用，](../articles/app-service/deploy-run-package.md)而無需拆開它。
- 停止您的應用程式，或在部署期間針對您的應用程式啟用離線模式。 有關詳細資訊，請參閱[在部署期間處理鎖定的檔](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)。
- 部署到[預備位置](../articles/app-service/deploy-staging-slots.md)，並且啟用[自動交換](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)。 
