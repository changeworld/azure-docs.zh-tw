---
title: Microsoft Threat Modeling Tool 版本 03/22/2020-Azure
description: 記載 Threat Modeling Tool 的版本資訊
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80146858"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool 更新版本 7.3.00316.1-03/22/2020

Microsoft Threat Modeling Tool （TMT）的版本7.3.00316.1 已于 22 2020 年3月發行，並包含下列變更：

- 協助工具增強功能
- Bug 修正
- 新的 DiagramReader 功能

## <a name="notable-bug-fixes"></a>值得注意的 bug 修正

### <a name="exporting-the-threat-list-to-csv"></a>將威脅清單匯出至 CSV

匯出至 CSV 函數不一致地選取要匯出威脅清單中的哪些欄位。 現在，威脅清單中的所有欄位都會匯出到 CSV 檔案。 

### <a name="ux-bugs"></a>UX bug

- 主要工作流程中的 [說明] 功能表（[建立]/[開啟]/[分析]）和範本編輯器體驗現在具有一致的功能表選項。
- [樣板] 窗格中的搜尋列現在已加入標準游標，並已新增適當的標籤。

## <a name="new-features"></a>新功能

### <a name="diagramreader-feature-has-been-added"></a>已新增 DiagramReader 功能

當模型開啟時，主要功能表中已加入新的 DiagramReader 功能。 這項功能會將模型的圖形表示轉換為以文字為基礎的敘述。 

## <a name="system-requirements"></a>系統需求

- 支援的作業系統：
  - [Microsoft Windows 10 年度更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)版（含）以後版本
- 需要 .NET 版本：
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)或更新版本
- 其他需求：
  - 接收工具和範本更新的網際網路連線

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[關於使用工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
