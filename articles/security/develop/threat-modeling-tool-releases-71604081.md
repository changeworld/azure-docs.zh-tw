---
title: 微軟威脅建模工具版本 4/9/2019
titleSuffix: Azure
description: 記載 Threat Modeling Tool 的版本資訊
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269719"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>威脅建模工具更新版本 7.1.60408.1 - 2019/4/9

Microsoft 威脅建模工具 （TMT） 的版本 7.1.60408.1 于 2019 年 4 月 9 日發佈，其中包含以下更改：

- Azure 金鑰保存庫和 Azure 流量管理器的新模具
- TMT 版本號現在顯示在主畫面上
- 已更新支援連結
- 錯誤修正

## <a name="feature-changes"></a>功能變更

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Azure 金鑰保存庫和 Azure 流量管理器的新模具

![Azure 金鑰保存庫模具](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Azure 金鑰保存庫和 Azure 流量管理器的新模具和威脅已添加到 Azure 模具集中。 基於 Azure 模具集打開模型時，將提示使用者更新與模型關聯的範本。 還可以通過使用"檔"功能表中的"應用範本"命令並重新應用最新的 Azure 雲服務.tb7 檔，手動啟動基於 Azure 模具集的更新模型。

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>TMT 版本號現在顯示在主畫面上

為了便於訪問，威脅建模工具的用戶端版本現在顯示在應用程式的主畫面上。

![Azure 金鑰保存庫模具](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>已更新支援連結

該工具中的所有支援連結都已更新，以指示使用者訪問[tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com)MSDN 論壇，而不是 MSDN 論壇。

## <a name="system-requirements"></a>系統需求

- 支援的作業系統
  - [微軟視窗 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 必要的 .NET 版本
  - [.淨 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)或更高版本
- 其他需求
  - 必須有網際網路連線可接收工具和範本的更新。

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](threat-modeling-tool.md)，其中包含[關於使用工具](threat-modeling-tool-getting-started.md)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
