---
title: Azure Arc 啟用的伺服器 (preview) 代理程式的新功能
description: 本文提供 Azure Arc 啟用的伺服器 (preview) 代理程式的版本資訊。 針對許多摘要問題，有其他詳細資料的連結。
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236637"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Azure Arc 啟用的伺服器 (preview) 代理程式的新功能

Azure Arc 啟用的伺服器 (預覽版) 連線的機器代理程式會持續獲得改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正

## <a name="august-2020"></a>2020 年 8 月

版本：0.11

- 支援 Ubuntu 20.04。

- 延伸模組部署的可靠性改進。

### <a name="known-issues"></a>已知問題

如果您使用舊版的 Linux 代理程式並將它設定為使用 proxy 伺服器，則需要在升級之後重新設定 proxy 伺服器設定。 若要這樣做，請執行 `sudo azcmagent_proxy add http://proxyserver.local:83`。

## <a name="next-steps"></a>後續步驟

在評估或啟用啟用 Arc 的伺服器之前 (預覽) 跨多個混合式電腦，請參閱 [連線的機器代理程式總覽](agent-overview.md) 以瞭解需求、代理程式的相關技術詳細資料，以及部署方法。