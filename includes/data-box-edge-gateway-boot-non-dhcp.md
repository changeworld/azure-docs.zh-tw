---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174647"
---
如果在非 DHCP 環境中啟動，請按照以下步驟為數據盒閘道部署虛擬機器。

1. [連接到設備的 Windows PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsIpAddress`Cmdlet 列出在虛擬裝置上啟用的網路介面。 如果您的裝置有已啟用的單一網路介面，系統指派給該介面的預設名稱會是 `Ethernet`。

    下面的示例顯示了此 Cmdlet 的用法：

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. 使用 `Set-HcsIpAddress` Cmdlet 來設定網路。 請參閱下列範例：

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

