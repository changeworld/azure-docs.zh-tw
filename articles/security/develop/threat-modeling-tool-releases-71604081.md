---
title: Microsoft Threat Modeling Tool 版本4/9/2019
titleSuffix: Azure
description: 記載 Threat Modeling Tool 的版本資訊
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78269719"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool 更新版本 7.1.60408.1-4/9/2019

Microsoft Threat Modeling Tool （TMT）的版本7.1.60408.1 已于 9 2019 年4月發行，並包含下列變更：

- Azure Key Vault 和 Azure 流量管理員的新樣板
- TMT 版本號碼現在會顯示在主畫面上
- 支援連結已更新
- Bug 修正

## <a name="feature-changes"></a>功能變更

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Azure Key Vault 和 Azure 流量管理員的新樣板

![Azure Key Vault 樣板](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

已將 Azure Key Vault 和 Azure 流量管理員的新樣板和威脅新增至 Azure 樣板集。 開啟以 Azure 樣板集為基礎的模型時，系統會提示使用者更新與模型相關聯的範本。 您也可以使用 [檔案] 功能表中的 [套用範本] 命令，手動起始以 Azure 樣板集為基礎的模型，並重新套用最新的 Azure 雲端服務 tb7 檔案。

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>TMT 版本號碼現在會顯示在主畫面上

Threat Modeling Tool 的用戶端版本現在會顯示在應用程式的主畫面上，以方便存取。

![Azure Key Vault 樣板](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>支援連結已更新

此工具內的所有支援連結都已更新，可將使用者導向至， [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) 而不是 MSDN 論壇。

## <a name="system-requirements"></a>系統需求

- 支援的作業系統
  - [Microsoft Windows 10 年度更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)版（含）以後版本
- 必要的 .NET 版本
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)或更新版本
- 其他需求
  - 必須有網際網路連線可接收工具和範本的更新。

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](threat-modeling-tool.md)，其中包含[關於使用工具](threat-modeling-tool-getting-started.md)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
