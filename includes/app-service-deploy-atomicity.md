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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75945167"
---
## <a name="what-happens-to-my-app-during-deployment"></a>在部署期間我的應用程式會發生什麼事？

所有正式支援的部署方法都會對應用程式資料夾中的檔案進行變更 `/home/site/wwwroot` 。 這些檔案是用來執行您的應用程式。 因此，部署可能會因為鎖定的檔案而失敗。 應用程式在部署期間可能也會無法預期的行為，因為並非所有檔案都同時更新。 對於客戶面向的應用程式，這是不必要的。 有幾種不同的方式可以避免這些問題：

- [直接從 ZIP 套件執行應用程式](../articles/app-service/deploy-run-package.md)，而不將它解壓縮。
- 停止您的應用程式，或在部署期間針對您的應用程式啟用離線模式。 如需詳細資訊，請參閱[在部署期間處理鎖定的](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)檔案。
- 部署到[預備位置](../articles/app-service/deploy-staging-slots.md)，並且啟用[自動交換](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)。 
