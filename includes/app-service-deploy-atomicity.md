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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004329"
---
## <a name="what-happens-to-my-app-during-deployment"></a>在部署期間我的應用程式會發生什麼事？

所有正式支援的部署方法都會變更 `/home/site/wwwroot` 您應用程式資料夾中的檔案。 這些檔案會用來執行您的應用程式。 因此，部署可能會因為鎖定的檔案而失敗。 在部署期間，應用程式也可能會無法預期地運作，因為並非所有檔案都會同時更新。 這對面向客戶的應用程式是不必要的。 有幾種不同的方式可以避免這些問題：

- [直接從 ZIP 套件執行您的應用程式](../articles/app-service/deploy-run-package.md) ，而不將它解壓縮。
- 停止您的應用程式，或在部署期間針對您的應用程式啟用離線模式。 如需詳細資訊，請參閱 [在部署期間處理鎖定的](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)檔案。
- 部署到[預備位置](../articles/app-service/deploy-staging-slots.md)，並且啟用[自動交換](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)。 
