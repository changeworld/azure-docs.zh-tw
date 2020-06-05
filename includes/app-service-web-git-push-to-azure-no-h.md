---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "75681010"
---
回到本機終端視窗，將 Azure 遠端新增至本機 Git 存放庫。 將 *\<deploymentLocalGitUrl-from-create-step>* 取代為您從[建立 Web 應用程式](#create-a-web-app)儲存之 Git 遠端的 URL。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

推送到 Azure 遠端，使用下列命令來部署您的應用程式。 當 Git 認證管理員提示輸入認證時，請務必輸入您在[設定部署使用者](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user)中建立的認證，而不是您用來登入 Azure 入口網站的認證。

```bash
git push azure master
```

此命令可能會花數分鐘執行。 執行上述命令時，會顯示類似下列範例的資訊：
