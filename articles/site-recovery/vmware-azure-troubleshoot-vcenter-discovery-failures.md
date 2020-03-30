---
title: 在 Azure 網站恢復中排除 VMware vCenter 發現失敗
description: 本文介紹如何在 Azure 網站恢復中排除 VMware vCenter 發現故障的疑難排解。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74091245"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>排除 vCenter 伺服器發現失敗

本文可説明您解決 VMware vCenter 發現失敗導致的問題。

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>maxSnapShots 屬性中的非數值

在 9.20 之前的版本中，vCenter 在 VM 上檢索屬性的非`snapshot.maxSnapShots`數值時斷開連接。

此問題由錯誤 ID 95126 標識。

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
若要解決此問題：

- 標識 VM 並將值設置為數值（vCenter 中的 VM 編輯設置）。

Or

- 將佈建服務器升級到版本 9.20 或更高版本。

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>vCenter 連接的代理配置問題

vCenter 發現遵循系統使用者配置的系統預設代理設置。 DRA 服務尊重使用者使用統一設置安裝程式或 OVA 範本安裝佈建服務器期間提供的代理設置。 

通常，代理用於與公共網路通信;例如與 Azure 通信。 如果配置了代理，並且 vCenter 位於本地環境中，則它將無法與 DRA 通信。

遇到此問題時，會出現以下情況：

- 由於錯誤，無法訪問\<vCenter 伺服器 vCenter>：遠端伺服器返回錯誤：（503） 伺服器不可用
- 由於錯誤，無法訪問\<vCenter 伺服器 vCenter>：遠端伺服器返回錯誤：無法連接到遠端伺服器。
- 無法連接到 vCenter/ESXi 伺服器。

若要解決此問題：

下載[PsExec 工具](https://aka.ms/PsExec)。 

使用 PsExec 工具訪問系統使用者上下文並確定是否配置了代理位址。 然後，您可以使用以下過程將 vCenter 添加到旁路清單中。

對於發現代理配置：

1. 使用 PsExec 工具在系統使用者上下文中打開 IE。
    
    psexec -s -i "%程式檔%\互聯網資源管理器\iexplore.exe"

2. 修改 Internet 資源管理器中的代理設置以繞過 vCenter IP 位址。
3. 重新開機 tmansvc 服務。

對於 DRA 代理配置：

1. 打開命令提示符並打開 Microsoft Azure 網站恢復提供程式資料夾。
 
    **cd C：\程式檔\微軟 Azure 網站恢復提供程式**

3. 從命令提示符中，運行以下命令。
   
   **Drconfigurator。EXE /配置 /添加旁路 Urls [添加 vCenter 時提供的 vCenter 伺服器 IP 位址/FQDN]**

4. 重新開機 DRA 提供程式服務。

## <a name="next-steps"></a>後續步驟

[管理 VMware VM 災害復原的設定伺服器](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
