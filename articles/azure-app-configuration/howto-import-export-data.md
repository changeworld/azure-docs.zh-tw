---
title: 使用 Azure 應用配置導入或匯出資料
description: 瞭解如何將資料導入或匯出到 Azure 應用配置或從 Azure 應用配置中
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5b3d6d0561d7d6d6b23cb4f579b0988850da9771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056873"
---
# <a name="import-or-export-configuration-data"></a>匯入或匯出設定資料

Azure 應用配置支援資料導入和匯出操作。 使用這些操作可批量處理配置資料，並在應用配置存儲和代碼專案之間交換資料。 例如，您可以設置一個應用配置存儲用於測試，另一個設置為生產。 您可以在它們之間複製應用程式設定，這樣您就不必輸入資料兩次。

本文提供了使用應用配置導入和匯出資料的指南。 如果您想設置與 GitHub 存儲庫的持續同步，請查看我們的[GitHub 操作](https://aka.ms/azconfig-gha1)。

## <a name="import-data"></a>匯入資料

導入會將配置資料從現有源引入應用配置存儲。 使用導入函數將資料移轉到應用配置存儲或聚合來自多個源的資料。 應用配置支援從 JSON、YAML 或屬性檔導入。

使用[Azure 門戶](https://portal.azure.com)或[Azure CLI](./scripts/cli-import.md)導入資料。 在 Azure 入口網站中執行下列步驟：

1. 流覽到應用配置商店，然後從 **"操作"** 功能表中選擇 **"導入/匯出**"。

1. 在 **"導入**"選項卡上，選擇 **"源服務** > **設定檔**"。

1. 選擇 **"為語言**"並選擇所需的輸入類型。

1. 選擇 **"資料夾**"圖示，然後流覽到要導入的檔。

    ![匯入檔案](./media/import-file.png)

1. 選擇**分隔符號**，並可以選擇輸入**首碼**以用於導入的鍵名稱。

1. 或者，選擇**標籤**。

1. 選擇 **"應用"** 以完成導入。

    ![導入檔已完成](./media/import-file-complete.png)

## <a name="export-data"></a>匯出資料

將存儲在應用配置中的配置資料匯出到另一個目標。 例如，使用匯出功能將資料保存在應用配置存儲區中嵌入在部署期間嵌入的應用程式代碼的檔。

使用[Azure 門戶](https://portal.azure.com)或[Azure CLI](./scripts/cli-export.md)匯出資料。 在 Azure 入口網站中執行下列步驟：

1. 流覽到應用配置商店，然後選擇 **"導入/匯出**"。

1. 在 **"匯出**"選項卡上，選擇 **"目標服務** > **設定檔**"。

1. 可選地輸入**首碼**，並為要匯出的鍵選擇**標籤**和時間點。

1. 選擇**檔案類型** > **分隔符號**。

1. 選擇 **"應用"** 以完成匯出。

    ![匯出檔已完成](./media/export-file-complete.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立 ASP.NET Core Web 應用程式](./quickstart-aspnet-core-app.md)  
