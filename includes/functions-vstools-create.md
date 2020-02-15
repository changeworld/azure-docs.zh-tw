---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3d93d3aa3e4e646f8e054f96f17bbe4a011d422d
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211443"
---
Visual Studio 中的 Azure Functions 專案範本會建立可發行至 Azure 中函式應用程式的專案。 您可以使用函式應用程式將多個函式群組為邏輯單位，以方便管理、部署、調整和共用資源。

1. 在 Visual Studio 中，於 [檔案]  功能表上選取 [新增]   > [專案]  。

1. 在 [建立新專案]  對話方塊中，搜尋 `functions`，選擇 [Azure Functions]  範本，然後選取 [下一步]  。

1. 輸入專案的名稱，然後選取 [建立]  。 函式應用程式名稱必須是有效的 C# 命名空間，因此不會使用底線、連字號或任何其他非英數字元。

1. 在 [建立新的 Azure Functions 應用程式]  中，使用下列選項：

    + **Azure Functions v2 (.NET Core)**
    + **HTTP 觸發程序**
    + **儲存體帳戶**：**儲存體模擬器**
    + **授權層級** **匿名** 

    | 選項      | 建議的值  | 描述                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Functions 執行階段** | **Azure Functions 2.x <br />(.NET Core)** | 此設定會建立使用 Azure Functions 2.x 版執行階段 (支援 .NET Core) 的函式專案。 Azure Functions 1.x 支援 .NET Framework。 如需詳細資訊，請參閱[設定 Azure Functions 執行階段目標版本](../articles/azure-functions/functions-versions.md)。   |
    | **函式範本** | **HTTP 觸發程序** | 此設定會建立 HTTP 要求所觸發的函式。 |
    | **儲存體帳戶**  | **儲存體模擬器** | HTTP 觸發程序不會使用 Azure 儲存體帳戶連線。 其他所有觸發程序類型都需要有效的儲存體帳戶連接字串。 因為 Functions 需要儲存體帳戶，所以當您將專案發佈至 Azure 時，就會指派或建立一個。 |
    | **授權層級** | **匿名** | 建立的函式可以由任何用戶端觸發，而不需提供金鑰。 此授權設定可以讓測試新函式變得簡單。 如需金鑰和授權的詳細資訊，請參閱 [HTTP 和 Webhook 繫結](../articles/azure-functions/functions-bindings-http-webhook.md)中的[授權金鑰](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)。 |
    
    > [!NOTE]
    > 請確定您已將 [授權層級]  設定為 `Anonymous`。 如果您選擇預設層級 `Function` 時，必須在要求中提供[函式金鑰](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)以存取您的函式端點。
    
4. 選取 [建立]  以建立函式專案和由 HTTP 觸發的函式。
