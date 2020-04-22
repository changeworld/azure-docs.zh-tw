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
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678460"
---
在開始配置之前,請安裝並導入所需的模組。 您需要管理員許可權才能在 PowerShell 中安裝模組。

1. 安裝並導入 Az 模組。
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. 安裝並導入 Az.對等模組。
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 使用此指令檢查模組是否正確匯入:
    ```powershell
    Get-Module
    ```
1. 使用以下命令登入您的 Azure 帳戶：
    ```powershell
    Connect-AzAccount
    ```
1. 檢查帳戶的訂閱,並選擇要在其中創建對等互連的訂閱。
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 如果還沒有資源組,則必須在創建對等互連之前創建一個資源組。 您可以執行下列命令來這麼做：

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> 如果您尚未關聯 ASN 和訂閱,請按照[關聯對等 ASN](../howto-subscription-association-powershell.md)中的步驟操作。 請求對等互連需要此操作。

> [!NOTE]
> 資源組的位置與選擇設置對等互連的位置無關。
&nbsp;
