---
title: CLI 範例
description: 尋找部分常見 App Service 案例的 Azure CLI 範例。 了解如何將 App Service 部署或管理工作自動化。
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 07/07/2020
ms.custom: mvc
ms.openlocfilehash: ce10c0e95d77103f386174c7bd0ffd56adff871b
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054537"
---
# <a name="cli-samples-for-azure-app-service"></a>Azure App Service 的 CLI 範例

下表包含使用 Azure CLI 所建置之 Bash 指令碼的連結。

| | |
|-|-|
|**建立應用程式**||
| [建立應用程式並使用 FTP 部署檔案](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 App Service 應用程式，並使用 FTP 將檔案部署至該應用程式。 |
| [建立應用程式並從 GitHub 部署程式碼](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 App Service 應用程式，並從公用 GitHub 存放庫部署程式碼。 |
| [建立可從 GitHub 連續部署的應用程式](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| 建立可從您擁有的 GitHub 存放庫持續發佈的 App Service 應用程式。 |
| [建立應用程式並從本機 Git 存放庫部署程式碼](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 App Service 應用程式，並從本機 Git 存放庫設定程式碼推送。 |
| [建立應用程式並將程式碼部署至預備環境](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | 建立具有部署位置以用於暫存程式碼變更的 App Service 應用程式。 |
| [在 Docker 容器中建立 ASP.NET Core 應用程式](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json) | 在 Linux 上建立 App Service 應用程式，並從 Docker Hub 載入 Docker 映像。 |
| [使用私人端點建立應用程式並將其公開](./scripts/cli-deploy-privateendpoint.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 App Service 應用程式和私人端點 |
|**設定應用程式**||
| [將自訂網域對應至應用程式](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 App Service 應用程式，並使自訂網域名稱與之對應。 |
| [將自訂 TLS/SSL 憑證繫結至應用程式](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 App Service 應用程式，並使自訂網域名稱的 TLS/SSL 憑證與之繫結。 |
|**調整應用程式**||
| [手動調整應用程式](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 App Service 應用程式，並跨 2 個執行個體調整它。 |
| [透過高可用性架構將應用程式調整為全球可用](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | 在兩個不同的地理區域中建立兩個 App Service 應用程式，並使用 Azure 流量管理員讓它們可透過單一端點來使用。 |
|**保護應用程式**||
| [與 Azure 應用程式閘道整合](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 App Service 應用程式，並使用服務端點和存取限制將其與應用程式閘道整合。 |
|**將應用程式連線至資源**||
| [將應用程式連線至 SQL 資料庫](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 App Service 應用程式和 Azure SQL Database 中的資料庫，然後將資料庫連接字串新增至應用程式設定。 |
| [將應用程式連線至儲存體帳戶](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 App Service 應用程式和儲存體帳戶，然後將儲存體連接字串新增至應用程式設定。 |
| [將應用程式連線至 Azure Cache for Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 App Service 應用程式和 Azure Cache for Redis，然後將 Redis 連線詳細資料新增至應用程式設定。 |
| [將應用程式連線至 Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 App Service 應用程式和 Cosmos DB，然後將 Cosmos DB 連線詳細資料新增至應用程式設定。 |
|**備份與還原應用程式**||
| [備份應用程式](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 App Service 應用程式，並為其建立一次性備份。 |
| [建立應用程式的排程備份](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 App Service 應用程式，並為其建立排定的備份。 |
| [從備份還原應用程式](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | 從備份還原 App Service 應用程式。 |
|**監視應用程式**||
| [使用 Web 伺服器記錄監視應用程式](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 App Service 應用程式、為其啟用記錄，並將記錄下載到本機電腦。 |
| | |
