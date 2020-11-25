---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010443"
---
若要在 Azure 中開啟函式應用程式的串流記錄：

1. 選取 F1 來開啟命令選擇區，然後搜尋並執行命令 **Azure Functions:Start Streaming Logs**。

1. 在 Azure 中選取您的函數應用程式，然後選取 [是] 以啟用函數應用程式的應用程式記錄功能。

1. 在 Azure 中觸發您的函數。 請注意，記錄資料會顯示在 Visual Studio Code 的 [輸出] 視窗中。

1. 當您完成時，請記得執行命令 **Azure Functions:Stop Streaming Logs** 以停用函數應用程式的記錄功能。