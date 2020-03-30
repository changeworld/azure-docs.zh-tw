---
title: 包含檔案
titleSuffix: Azure
description: 包含檔案
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774220"
---
在開始配置之前，安裝並導入所需的模組。 您需要管理員許可權才能在 PowerShell 中安裝模組。

1. 安裝和導入 Az 模組
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. 安裝和導入 Az.對等模組
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 使用下面的命令驗證模組導入正常。
    ```powershell
    Get-Module
    ```
1. 使用以下命令登錄到 Azure 帳戶。
    ```powershell
    Connect-AzAccount
    ```
1. 檢查帳戶的訂閱並選擇要在其中創建對等互連的訂閱。
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 如果還沒有資源組，則必須在創建對等互連之前創建一個資源組。 您可以執行下列命令來這麼做：

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> 如果尚未關聯 ASN 和訂閱，請按照[關聯對等 ASN](../howto-subscription-association-powershell.md)的步驟操作。 這是請求對等互連所必需的。

> [!NOTE]
> 資源組的位置與選擇設置對等互連的位置無關。
&nbsp;
