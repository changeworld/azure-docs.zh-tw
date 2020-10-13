---
title: 包含檔案
description: 包含檔案
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009737"
---
> [!NOTE]
> Web 應用程式會在 20 分鐘沒有活動之後逾時。 而只有實際 Web 應用程式所提出的要求可以重設計時器。 在 Azure 入口網站中檢視應用程式的組態，或者對進階工具網站 (`https://<app_name>.scm.azurewebsites.net`) 提出要求，並不會重設計時器。 如果將 Web 應用程式設定為連續執行或已排程 (計時器觸發程序) WebJob，請在 Web 應用程式的 Azure [設定] 頁面上啟用 [一律開啟] 設定，以確保 WebJob 能夠可靠地執行。 這項功能僅適用於基本、標準和進階[定價層](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
