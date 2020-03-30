---
title: 微軟威脅建模工具版本 10/16/2019 - Azure
description: 記載 Threat Modeling Tool 的版本資訊
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 452b44653775a1bcb9456b62e1587b5ff2dff874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552044"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool 更新版本 7.1.61015.1 - 2019 年 10 月 16 日

Microsoft 威脅建模工具 （TMT） 的版本 7.1.61015.1 于 2019 年 10 月 16 日發佈，其中包含以下更改：

- 協助工具增強功能
- 錯誤修正
- Azure 邏輯應用和 Azure 資料資源管理器的新模具

## <a name="notable-bug-fixes"></a>值得注意的錯誤修復

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>改進了與"威脅建模工具 2016"中創建的檔的向後相容性

修復了與"威脅建模工具 2016"中創建的威脅模型檔的打開或顯示相關的幾個錯誤。

## <a name="feature-enhancements"></a>功能增強功能

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Azure 邏輯應用和 Azure 資料資源管理器的新模具

Azure 邏輯應用和 Azure 資料資源管理器的新模具連同其關聯的威脅和緩解措施一起添加到 Azure 模具中。

![Azure 邏輯應用和 Azure 資料資源管理器模具](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>已知問題

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>與預期範圍以外的優先順序值相關的錯誤

一些客戶在打開"威脅建模工具 2016"或自訂範本中創建的檔時報告收到以下錯誤訊息：

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

此問題正在調查中

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
