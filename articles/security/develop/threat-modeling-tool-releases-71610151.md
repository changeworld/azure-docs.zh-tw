---
title: Microsoft Threat Modeling Tool 版本 10/16/2019-Azure
description: 記載 Threat Modeling Tool 的版本資訊
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 410da29d8c119111d1ec9b4e60bf8fb1ca661f70
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259378"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool 更新版本 7.1.61015.1 - 2019 年 10 月 16 日

 (TMT) 的 Microsoft Threat Modeling Tool 版本7.1.61015.1 已于 16 2019 年10月發行，其中包含下列變更：

- 協助工具增強功能
- Bug 修正
- Azure Logic Apps 和 Azure 資料總管的新樣板

## <a name="notable-bug-fixes"></a>值得注意的 bug 修正

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>已改善在 "Threat Modeling Tool 2016" 中建立之檔案的回溯相容性

已修正與開啟或顯示在 "Threat Modeling Tool 2016" 中建立的威脅模型檔案相關的幾個 bug。

## <a name="feature-enhancements"></a>功能增強

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Azure Logic Apps 和 Azure 資料總管的新樣板

Azure Logic Apps 和 Azure 資料總管的新樣板已新增至 Azure 樣板，及其相關聯的威脅和緩和措施。

![Azure Logic Apps 和 Azure 資料總管樣板](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>已知問題

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>與預期範圍外的優先順序值相關的錯誤

某些客戶在開啟 "Threat Modeling Tool 2016" 或自訂範本中建立的檔案時，回報收到下列錯誤訊息：

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

此問題正在調查中

## <a name="system-requirements"></a>系統需求

- 支援的作業系統
  - [Microsoft Windows 10 年度更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)版（含）以後版本
- 必要的 .NET 版本
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)或更新版本
- 其他需求
  - 必須有網際網路連線可接收工具和範本的更新。

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[關於使用工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
