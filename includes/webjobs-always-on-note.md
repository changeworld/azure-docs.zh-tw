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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68424148"
---
> [!NOTE]
> Web 應用程式會在 20 分鐘沒有活動之後逾時。 只有對實際 Web 應用程式所提出的要求會重設計時器。 在 Azure 門戶中查看應用的配置或向高級工具網站 （`https://<app_name>.scm.azurewebsites.net`） 發出請求不會重置計時器。 如果應用運行連續或計畫（計時器觸發器）Web作業，請啟用 **"始終打開**"以確保 Web 作業可靠運行。 這項功能僅適用於基本、標準和進階[定價層](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
