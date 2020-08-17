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
ms.openlocfilehash: 8539696f4521a1b4a2f56fe7d2936b45dec26ec9
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88077646"
---
回到本機終端視窗，將 Azure 遠端新增至本機 Git 存放庫。 以您從[建立 Web 應用程式](#create-a-web-app)儲存的 Git 遠端 URL 取代 *\<deploymentLocalGitUrl-from-create-step>* 。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

推送到 Azure 遠端，使用下列命令來部署您的應用程式。 當 Git 認證管理員提示輸入認證時，請務必輸入您在**設定部署使用者**中建立的認證，而不是您用來登入 Azure 入口網站的認證。

```bash
git push azure master
```

此命令可能會花數分鐘執行。 執行上述命令時，會顯示類似下列範例的資訊：
