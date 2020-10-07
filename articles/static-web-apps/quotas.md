---
title: Azure 靜態 Web Apps 預覽版中的配額
description: 了解與 Azure 靜態 Web Apps 預覽版相關聯的配額
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 6fa70eba5875c32d41fa1cfc8daf9b1cdf8f19df
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "83599106"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Azure 靜態 Web Apps 預覽版中的配額

Azure 靜態 Web Apps 預覽版有下列配額。

> [!IMPORTANT]
> Azure 靜態 Web Apps 處於公開預覽狀態，不適用於生產環境。

| 功能                     | 免費方案        |
|-----------------------------|------------------|
| 內含頻寬          | 每個月 100 GB |
| 超額頻寬           | 無法使用      |
| 每個 Azure 訂用帳戶的應用程式 | 10               |
| 應用程式大小                    | 100 MB           |
| 生產前環境 | 1                |
| 自訂網域              | 1                |
| 授權<br><br>具有自訂角色和路由規則 | 最多 25 個受邀的使用者和指派的角色 |
| Azure Functions             | 可用        |
| SLA                         | None             |

## <a name="github-storage"></a>GitHub 儲存體

GitHub 動作和套件會使用 GitHub 儲存體，其具有本身的一組配額。 當您建立 Azure 靜態 Web Apps 網站時，GitHub 會儲存您的網站成品，即使在部署之後亦然。

如需詳細資訊，請參閱下列資源：

- [管理動作儲存空間](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [關於 GitHub 動作的計費](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [管理 GitHub 動作的消費限制](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>後續步驟

- [概觀](overview.md)
