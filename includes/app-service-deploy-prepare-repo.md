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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
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
| Node.js | 使用啟動腳本的_server .js_、 _node.js_或_package. json_ |
| Python | . .py、_需求 .txt_或_runtime .txt_ _ \* _ |
| HTML | _default.htm_、_default.html_、_default.asp_、_index.htm_、_index.html_ 或 _iisstart.htm_ |
| WebJobs | _\<job_name>/run。\< _ [_應用程式\_資料/作業]/_ [連續 webjob] 底下的擴充功能>，或針對觸發的 webjob_觸發的應用\_程式資料/作業_。 如需詳細資訊，請參閱[Kudu webjob 檔](https://github.com/projectkudu/kudu/wiki/WebJobs)。 |
| 函式 | 請參閱 [Azure Functions 的持續部署](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)。 |

若要自訂部署，您可以在儲存機制根路徑中包含 *.deployment* 檔案。 如需詳細資訊，請參閱[自訂部署](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)和[自訂部署腳本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。

> [!NOTE]
> 如果您是在 Visual Studio 中開發，可讓 [Visual Studio 為您建立存放庫](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio)。 專案會立即準備好使用 Git 進行部署。
>

