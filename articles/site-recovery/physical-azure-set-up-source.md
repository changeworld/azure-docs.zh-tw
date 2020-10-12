---
title: 使用 Azure Site Recovery 為組態伺服器設定從實體伺服器至 Azure 的災害復原 | Microsoft Docs
description: 本文說明如何為內部部署組態伺服器設定從內部部署實體伺服器至 Azure 的災害復原。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: d2cc3627222bcd8de5a5cb1b5c83b04c5d4ef880
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86130201"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>為組態伺服器設定從實體伺服器至 Azure 的災害復原

這篇文章說明如何設定內部部署環境，開始將執行 Windows 或 Linux 的實體伺服器複寫至 Azure。

## <a name="prerequisites"></a>Prerequisites

本文假設您已經有：
- [Azure 入口網站](https://portal.azure.com "Azure 入口網站")中的復原服務保存庫。
- 可供安裝設定伺服器的實體電腦。
- 如果您已在要安裝設定伺服器的電腦上停用 TLS 1.0，請確定已啟用 TLs 1.2，且 .NET Framework 4.6 版或更新版本已安裝在已啟用強式密碼編譯) 的電腦 (上。 [深入了解](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1)。

### <a name="configuration-server-minimum-requirements"></a>組態伺服器最低需求
下表列出組態伺服器的最低硬體、軟體與網路需求。
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> 組態伺服器不支援 HTTPS 型 Proxy 伺服器。

## <a name="choose-your-protection-goals"></a>選擇您的保護目標

1. 在 Azure 入口網站中，移至 [復原服務]**** 保存庫刀鋒視窗，然後選取您的保存庫。
2. 在保存庫的 [**資源**] 功能表中，按一下 [ **Getting Started**  >  **Site Recovery**  >  **步驟1：準備基礎結構**  >  **保護目標**] 消費者入門 Site Recovery。

    ![選擇目標](./media/physical-azure-set-up-source/choose-goals.png)
3. 在 [保護目標]**** 中選取 [至 Azure]**** 和 [未虛擬化/其他]****，然後按一下 [確定]****。

    ![選擇目標](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>設定來源環境

1. 在 [ **準備來源**] 中，如果您沒有設定伺服器，請按一下 [ **+ 設定伺服器** ] 新增一個。

   ![設定來源](./media/physical-azure-set-up-source/plus-config-srv.png)
2. 在 [ **新增伺服器** ] 分頁中，檢查設定 **伺服器** 是否出現在 [ **伺服器類型**] 中。
4. 下載 Site Recovery 統一安裝的安裝檔案。
5. 下載保存庫註冊金鑰。 執行「整合安裝」時，您需要該註冊金鑰。 該金鑰在產生後會維持 5 天有效。

    ![設定來源](./media/physical-azure-set-up-source/set-source2.png)
6. 在作為組態伺服器的電腦上，執行「Azure Site Recovery 整合安裝」****，以安裝組態伺服器、處理伺服器和主要目標伺服器。

#### <a name="run-azure-site-recovery-unified-setup"></a>執行 Azure Site Recovery 統一安裝

> [!TIP]
> 如果您電腦系統時鐘的時間與當地時間差五分鐘以上，組態伺服器註冊將會失敗。 開始安裝之前，請先將您的系統時鐘與 [時間伺服器](/windows-server/networking/windows-time-service/windows-time-service-top) 同步。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 您可以透過命令列安裝組態伺服器。 [深入了解](physical-manage-configuration-server.md#install-from-the-command-line)。


## <a name="common-issues"></a>常見問題

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>接下來的步驟

下一個步驟是在 Azure 中[設定目標環境](physical-azure-set-up-target.md)。
