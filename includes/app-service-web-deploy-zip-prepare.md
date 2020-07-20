---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 035399924216434de85865102db8838ea3fa15a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85570113"
---
## <a name="create-a-project-zip-file"></a>建立專案 ZIP 檔案

>[!NOTE]
> 如果您在 ZIP 檔案中下載了檔案，請先將檔案解壓縮。 例如，如果您從 GitHub 下載了 ZIP 檔案，則無法以原狀部署該檔案。 GitHub 會新增其他巢狀目錄，其不適用於 App Service。 
>

在本機終端機視窗中，瀏覽至應用程式專案的根目錄。 

此目錄應該包含您的 web 應用程式的項目檔案，例如 index.html__、index.php__ 和 app.js__。 也可以包含套件管理檔案，像是 project.json__、composer.json__、package.json__、bower.json__ 和 requirements.txt__。

除非您想要 App Service 為您執行部署自動化，否則請執行所有的組建工作（例如、、、 `npm` `bower` `gulp` `composer` 和 `pip` ），並確定您擁有執行應用程式所需的所有檔案。 如果您想要[直接執行封裝](../articles/app-service/deploy-run-package.md)，則需要此步驟。

在專案中建立所有項目的 ZIP 封存。 針對 `dotnet` 專案，此資料夾是命令的輸出檔案夾 `dotnet publish` 。 下列命令會使用您終端機中的預設工具：

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

