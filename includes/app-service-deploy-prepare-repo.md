---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836861"
---
## <a name="prepare-your-repository"></a>準備您的存放庫

要從 Azure 應用服務 Kudu 生成伺服器獲取自動生成，請確保存儲庫根目錄在專案中具有正確的檔。

| 執行階段 | 根目錄檔案 |
|-|-|
| ASP.NET (僅限 Windows) | _*.sln_、_*.csproj_ 或 _default.aspx_ |
| ASP.NET Core | _*.sln_ 或 _*.csproj_ |
| PHP | _index.php_ |
| Ruby (僅限 Linux) | _Gemfile_ |
| Node.js | 含啟動指令碼的 _server.js_、_app.js_, 或 _package.json_ |
| Python | .py ，_要求.txt_， 或_運行時.txt_ _ \* _ |
| HTML | _default.htm_、_default.html_、_default.asp_、_index.htm_、_index.html_ 或 _iisstart.htm_ |
| WebJobs | _\<job_name>/運行。擴展\<>"__應用\_資料/作業/_ 連續 Web 作業"或 _"\_應用資料/作業/_ 為觸發 Web 作業觸發"的擴展。 有關詳細資訊，請參閱[庫杜 Web作業文檔](https://github.com/projectkudu/kudu/wiki/WebJobs)。 |
| 函式 | 請參閱 [Azure Functions 的持續部署](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)。 |

若要自訂部署，您可以在儲存機制根路徑中包含 *.deployment* 檔案。 有關詳細資訊，請參閱[自訂部署](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)和[自訂部署腳本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。

> [!NOTE]
> 如果您是在 Visual Studio 中開發，可讓 [Visual Studio 為您建立存放庫](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio)。 使用 Git 可以立即部署該專案。
>

