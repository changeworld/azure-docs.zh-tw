---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 164620bdcee7ac546468354f999dcb3ad96ecf4b
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/12/2020
ms.locfileid: "84730976"
---
Visual Studio 中的 Azure Functions 專案範本會建立可發行至 Azure 中函式應用程式的專案。 您可以使用函式應用程式將多個函式群組為邏輯單位，以方便管理、部署、調整和共用資源。

1. 在 Visual Studio 功能表中，選取 [檔案]  >  [新增]  >  [專案]。

1. 在 [建立新專案] 的搜尋方塊中輸入*函式*，選擇 [Azure Functions] 範本，然後選取 [下一步]。

1. 在 [設定您的新專案] 中，輸入專案的**專案名稱**，然後選取 [建立]。 函式應用程式名稱必須是有效的 C# 命名空間，因此不會使用底線、連字號或任何其他非英數字元。

1. 針對 [建立新的 Azure Functions 應用程式] 設定，請使用下表中的值：

    | 設定      | 值  | 描述                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Functions 執行階段** | **Azure Functions v3 <br />(.NET Core)** | 此值會建立使用 Azure Functions 3.x 版執行階段 (支援 .NET Core 3.x) 的函式專案。 Azure Functions 1.x 支援 .NET Framework。 如需詳細資訊，請參閱 [Azure Functions 執行階段版本概觀](../articles/azure-functions/functions-versions.md)。   |
    | **函式範本** | **HTTP 觸發程序** | 此值會建立 HTTP 要求所觸發的函式。 |
    | **儲存體帳戶 (AzureWebJobsStorage)**  | **儲存體模擬器** | 因為 Azure Function 需要儲存體帳戶，所以當您將專案發佈至 Azure 時，就會指派或建立一個。 HTTP 觸發程式不會使用 Azure 儲存體帳戶連接字串；所有其他觸發程式類型都需要有效的 Azure 儲存體帳戶連接字串。  |
    | **授權層級** | **匿名** | 建立的函式可以由任何用戶端觸發，而不需提供金鑰。 此授權設定可以讓測試新函式變得簡單。 如需金鑰和授權的詳細資訊，請參閱 [授權金鑰](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)和 [HTTP 和 Webhook 繫結](../articles/azure-functions/functions-bindings-http-webhook.md)。 |
    
    
    ![Azure Functions 專案設定](./media/functions-vs-tools-create/functions-project-settings.png)

    請確定您已將 [授權層級] 設定為 [匿名]。 如果您選擇**函式**的預設層級，必須在要求中提供[函式金鑰](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)以存取您的函式端點。

1. 選取 [建立] 以建立函式專案和 HTTP 觸發程序函數。
