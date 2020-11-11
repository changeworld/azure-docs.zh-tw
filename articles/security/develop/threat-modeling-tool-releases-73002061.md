---
title: Microsoft Threat Modeling Tool 版本 02/11/2020-Azure
description: 記載威脅模型化工具 release 7.3.00206.1 的版本資訊。
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 9f9b162460cd2e7a624c1ad3f992011487d1b795
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516929"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool 更新版本 7.3.00206.1-02/11/2020

Microsoft Threat Modeling Tool (TMT) 的版本7.3.00206.1 已于 11 2020 年2月發行，並包含下列變更：

- Bug 修正

## <a name="notable-bug-fixes"></a>值得注意的錯誤修正

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>在預期範圍之外的優先順序值相關錯誤

某些客戶回報在開啟 "Threat Modeling Tool 2016" 或自訂範本中建立的檔案時，收到下列錯誤訊息：

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

此版本已解決此問題。

## <a name="system-requirements"></a>系統需求

- 支援的作業系統
  - [Microsoft Windows 10 年度更新版](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 或更新版本
- 必要的 .NET 版本
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 或更新版本
- 其他需求
  - 必須有網際網路連線可接收工具和範本的更新。

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](./threat-modeling-tool.md)，其中包含[關於使用工具](./threat-modeling-tool-getting-started.md)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。