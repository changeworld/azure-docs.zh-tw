---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174458"
---
若要透過容器直接開啟 SSH 工作階段，您的應用程式應在執行中。

在瀏覽器中貼入下列 URL，並以您的應用程式名稱取代 \<app-name>：

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

如果您尚未經過驗證，必須向您的 Azure 訂用帳戶進行驗證才能連線。 驗證之後，您會看到瀏覽器中的殼層，您可以在其中執行您容器內的命令。

![SSH 連線](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
