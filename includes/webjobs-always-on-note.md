---
title: 包含檔案
description: 包含檔案
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "68424148"
---
> [!NOTE]
> Web 應用程式會在 20 分鐘沒有活動之後逾時。 只有對實際 Web 應用程式所提出的要求會重設計時器。 在 Azure 入口網站中查看應用程式的設定，或對 advanced tools 網站（）提出要求，並 `https://<app_name>.scm.azurewebsites.net` 不會重設計時器。 如果您的應用程式執行連續或排程（計時器觸發程式） Webjob，請啟用**Always On**以確保 webjob 能夠可靠地執行。 這項功能僅適用於基本、標準和進階[定價層](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
