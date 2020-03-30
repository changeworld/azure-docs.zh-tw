---
title: 微軟威脅建模工具版本 02/11/2020 - Azure
description: 記載 Threat Modeling Tool 的版本資訊
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624835"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>威脅建模工具更新版本 7.3.00206.1 - 02/11/2020

Microsoft 威脅建模工具 （TMT） 的版本 7.3.00206.1 于 2020 年 2 月 11 日發佈，其中包含以下更改：

- 錯誤修正

## <a name="notable-bug-fixes"></a>值得注意的錯誤修復

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>與預期範圍以外的優先順序值相關的錯誤

一些客戶在打開"威脅建模工具 2016"或自訂範本中創建的檔時報告收到以下錯誤訊息：

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

此問題已在此版本中得到解決。

## <a name="system-requirements"></a>系統需求

- 支援的作業系統
  - [微軟視窗 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 必要的 .NET 版本
  - [.淨 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)或更高版本
- 其他需求
  - 必須有網際網路連線可接收工具和範本的更新。

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[關於使用工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
