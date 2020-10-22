---
title: 針對 Azure Site Recovery 中的 VMware vCenter 探索失敗進行疑難排解
description: 本文說明如何針對 Azure Site Recovery 中的 VMware vCenter 探索失敗進行疑難排解。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: 1a8471305af93194ccae7b0928685e10d4d64726
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366646"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>針對 vCenter Server 探索失敗進行疑難排解

本文可協助您針對 VMware vCenter 探索失敗時所發生的問題進行疑難排解。

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>MaxSnapShots 屬性中的非數值

在9.20 之前的版本中，vCenter 會在針對 VM 上的屬性屬性抓取非數值時中斷連線  `snapshot.maxSnapShots` 。

此問題是由錯誤 ID 95126 所識別。

```output
ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
System.FormatException: Input string was not in a correct format.
    at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
    at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
    at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
```

若要解決問題：

- 識別 VM，並將值設定為 vCenter)  (VM 編輯設定的數值。

Or

- 將設定伺服器升級至9.20 版或更新版本。

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>VCenter 連接的 Proxy 設定問題

vCenter Discovery 會接受系統使用者所設定的系統預設 proxy 設定。 DRA 服務會在使用整合安裝程式或 OVA 範本安裝設定伺服器時，接受使用者提供的 proxy 設定。 

一般情況下，會使用 proxy 來與公用網路通訊;例如與 Azure 通訊。 如果已設定 proxy，且 vCenter 在本機環境中，它將無法與 DRA 進行通訊。

當發生此問題時，會發生下列情況：

- \<vCenter>因為發生錯誤，所以無法連線到 vCenter server：遠端伺服器傳回錯誤： (503) 伺服器無法使用
- \<vCenter>因為發生錯誤，所以無法連線到 vCenter server：遠端伺服器傳回錯誤：無法連線到遠端伺服器。
- 無法連接到 vCenter/ESXi 伺服器。

若要解決問題：

下載 [PsExec 工具](/sysinternals/downloads/psexec)。 

使用 PsExec 工具來存取系統使用者內容，並判斷是否已設定 proxy 位址。 然後，您可以使用下列程式，將 vCenter 新增至略過清單。

針對探索 proxy 設定：

1. 使用 PsExec 工具在系統使用者內容中開啟 IE。
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. 修改 Internet Explorer 中的 proxy 設定，以略過 vCenter IP 位址。
3. 重新開機 tmanssvc 服務。

針對 DRA proxy 設定：

1. 開啟命令提示字元，然後開啟 Microsoft Azure Site Recovery Provider] 資料夾。
 
    **cd C:\Program Files\Microsoft Azure Site Recovery 提供者**

3. 從命令提示字元中，執行下列命令。
   
   **DRCONFIGURATOR.EXE/configure/AddBypassUrls [新增 vCenter 時所提供的 vCenter Server 的 IP 位址/FQDN]**

4. 重新開機 DRA 提供者服務。

## <a name="next-steps"></a>後續步驟

[管理 VMware VM 災害復原的設定伺服器](./vmware-azure-manage-configuration-server.md#refresh-configuration-server)