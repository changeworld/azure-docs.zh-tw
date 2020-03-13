---
title: 使用 Azure 應用程式組態匯入或匯出資料
description: 瞭解如何在 Azure 應用程式組態匯入或匯出資料
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 2c074cbd99620a482b18cbe2dfcce8f987d78bd5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278268"
---
# <a name="import-or-export-configuration-data"></a>匯入或匯出設定資料

Azure 應用程式組態支援資料匯入和匯出作業。 使用這些作業來處理大量的設定資料，並在您的應用程式組態存放區和程式碼專案之間交換資料。 例如，您可以設定一個應用程式組態存放區進行測試，另一個用於生產環境。 您可以在它們之間複製應用程式設定，如此一來，您就不需要輸入資料兩次。

本文提供使用應用程式組態匯入和匯出資料的指南。

## <a name="import-data"></a>匯入資料

[匯入] 會將設定資料從現有來源帶入應用程式組態存放區。 使用匯入函式，將資料移轉至應用程式組態存放區，或匯總多個來源的資料。 應用程式組態支援從 JSON、YAML 或 properties 檔案匯入。

使用[Azure 入口網站](https://portal.azure.com)或[Azure CLI](./scripts/cli-import.md)來匯入資料。 在 Azure 入口網站中執行下列步驟：

1. 流覽至您的應用程式組態存放區，然後從 [**作業**] 功能表中選取 [匯**入/匯出**]。

1. 在 [匯**入**] 索引標籤上，選取 [**來源服務** > **設定檔**]。

1. 針對 [語言] 選取，然後選取您想**要的**輸入類型。

1. 選取**資料夾**圖示，然後流覽至要匯入的檔案。

    ![匯入檔案](./media/import-file.png)

1. 選取**分隔符號**，並選擇性地輸入要用於匯入索引鍵名稱的**前置**詞。

1. （選擇性）選取**標籤**。

1. 選取 **[** 套用] 以完成匯入。

    ![匯入檔案已完成](./media/import-file-complete.png)

## <a name="export-data"></a>匯出資料

Export 會將儲存在應用程式組態中的設定資料寫入另一個目的地。 例如，使用 export 函式，將應用程式組態存放區中的資料儲存至在部署期間與應用程式代碼內嵌的檔案。

使用[Azure 入口網站](https://portal.azure.com)或[Azure CLI](./scripts/cli-export.md)來匯出資料。 在 Azure 入口網站中執行下列步驟：

1. 流覽至您的應用程式組態存放區，然後選取 [匯**入/匯出**]。

1. 在 [**匯出**] 索引標籤上，選取 [**目標服務**] > **設定檔**。

1. 選擇性地輸入**前置**詞，然後選取**標籤**和金鑰的時間點以進行匯出。

1. 選取 > **分隔符號**的**檔案類型**。

1. 選取 **[** 套用] 以完成匯出。

    ![匯出檔案已完成](./media/export-file-complete.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立 ASP.NET Core Web 應用程式](./quickstart-aspnet-core-app.md)  
