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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67836861"
---
## <a name="prepare-your-repository"></a>準備您的存放庫

若要從 Azure App Service Kudu 組建伺服器取得自動組建，請確定您的存放庫根目錄在您的專案中有正確的檔案。

| 執行階段 | 根目錄檔案 |
|-|-|
| ASP.NET (僅限 Windows) | _*.sln_、_*.csproj_ 或 _default.aspx_ |
| ASP.NET Core | _* .sln_或 _* .csproj_ |
| PHP | _index.php_ |
| Ruby (僅限 Linux) | _Gemfile_ |
| Node.js | 使用啟動腳本_server.js_、 _app.js_或_package.js_ |
| Python | _ \* . .py_、 _requirements.txt_或_runtime.txt_ |
| HTML | _default.htm_、_default.html_、_default.asp_、_index.htm_、_index.html_ 或 _iisstart.htm_ |
| WebJobs | _\<job_name>/run.\<extension>_ 在 [適用于連續 Webjob 的_應用程式 \_ 資料/作業/連續_] 底下，或針對觸發的 webjob 觸發的 [_應用程式 \_ 資料/作業_]。 如需詳細資訊，請參閱[Kudu webjob 檔](https://github.com/projectkudu/kudu/wiki/WebJobs)。 |
| 函數 | 請參閱 [Azure Functions 的持續部署](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)。 |

若要自訂部署，您可以在儲存機制根路徑中包含 *.deployment* 檔案。 如需詳細資訊，請參閱[自訂部署](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)和[自訂部署腳本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。

> [!NOTE]
> 如果您是在 Visual Studio 中開發，可讓 [Visual Studio 為您建立存放庫](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio)。 專案會立即準備好使用 Git 進行部署。
>

