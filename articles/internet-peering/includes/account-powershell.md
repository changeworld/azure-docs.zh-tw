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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678460"
---
開始設定之前，請安裝並匯入必要的模組。 您需要系統管理員許可權，才能在 PowerShell 中安裝模組。

1. 安裝並匯入 Az 模組。
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. 安裝並匯入 Az. 對等模組。
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 使用下列命令，確認已正確匯入模組：
    ```powershell
    Get-Module
    ```
1. 使用以下命令登入您的 Azure 帳戶：
    ```powershell
    Connect-AzAccount
    ```
1. 檢查帳戶的訂用帳戶，然後選取您要在其中建立對等互連的訂閱。
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 如果您還沒有資源群組，您必須在建立對等互連之前建立一個。 您可以執行下列命令來這麼做：

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> 如果您尚未與您的 ASN 和訂用帳戶相關聯，請遵循[關聯對等 asn](../howto-subscription-association-powershell.md)中的步驟。 需要此動作才能要求對等互連。

> [!NOTE]
> 資源群組的位置與您選擇設定對等互連的位置無關。
&nbsp;
