---
title: 升級 Microsoft Azure 恢復服務 （MARS） 代理
description: 瞭解如何升級 Microsoft Azure 恢復服務 （MARS） 代理。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672822"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>升級 Microsoft Azure 恢復服務 （MARS） 代理

在本文中，您將了解如何：

* 使用早期版本的 MARS 代理標識伺服器
* 更新這些伺服器上的 MARS 安裝

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>使用早期版本的 MARS 代理標識伺服器

對於 Azure 備份代理和 Azure 備份伺服器的安裝：

1. 導航到恢復服務保存庫，其中已註冊伺服器，這些伺服器可能由舊版本的代理進行備份。 您可以在 Azure 備份更新警報中找到具有較舊 Azure 備份代理的代表性保存庫清單。
1. 在恢復服務保存庫的左側**設置**部分，在 **"管理**"部分下選擇**備份基礎結構**。
1. 要發現作為 Azure 備份伺服器安裝的一部分安裝的 Azure 備份代理，請轉到**管理伺服器**下的**備份管理伺服器**。 這將列出具有 Azure 備份伺服器安裝的伺服器以及關聯的 Azure 備份代理的版本號。

    ![作為 Azure 備份伺服器安裝的一部分安裝的 MARS 代理清單](./media/upgrade-mars-agent/backup-management-servers.png)

1. 要檢查 Microsoft Azure 恢復服務 （MARS） 代理安裝或 Azure 備份代理的代理版本，請轉到**管理伺服器**下的**受保護伺服器**。 然後在備份管理類型下選擇**Azure 備份代理**。 這將列出具有 Azure 備份代理安裝的伺服器以及安裝的版本號。

    ![安裝了 MARS 代理的伺服器清單](./media/upgrade-mars-agent/protected-servers.png)

1. 通過按一下 MARS 代理安裝的 **"代理版本**"列或 Azure 備份伺服器安裝的**Azure 備份代理版本**列，對 Azure 備份代理版本列進行排序。

1. 上一步將為您提供具有低於 2.0.9083.0 或代理版本列為空白的 Azure 備份代理的伺服器的清單。 這些是需要更新 Azure 備份代理的伺服器。

## <a name="update-the-mars-agent-installation-on-the-server"></a>補救伺服器上的 MARS 代理安裝

標識需要 Azure 備份代理更新的伺服器後，請對每個標識的伺服器執行以下步驟（使用 Azure 備份伺服器或 MARS 代理）。 [按照以下步驟下載最新版本的 Azure 備份代理](https://aka.ms/azurebackup_agent)。

1. 按一下 Azure 備份代理低於 2.0.9083.0 或空白的行。 這將打開伺服器詳細資訊螢幕。

    ![具有過期代理版本的受保護伺服器](./media/upgrade-mars-agent/old-agent-version.png)

    ![具有過期代理版本的 Azure 備份伺服器](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. 按一下"**連接"** 以接收遠端桌面連線檔以與伺服器連接或通過伺服器上的遠端桌面連線直接連接到伺服器。

    ![通過遠端桌面連線連接到伺服器](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > 如果列出的伺服器不存在或已停用，則可以忽略下面的其餘步驟，並跳到下一個伺服器。

1. 輸入您的管理登錄詳細資訊並登錄。

1. 如果伺服器或伺服器的代理的 Internet 訪問受限，請確保伺服器/代理上的防火牆設置配置為允許適合您使用的 Azure 雲的 URL：

    Azure 雲端 | URL
    -- | ---
    Azure 雲（公共） |   `https://login.windows.net`
    Azure 中國 21Vianet 雲   | `https://login.chinacloudapi.cn`
    Azure 美國政府雲 |   `https://login.microsoftonline.us`
    Azure 德國雲端  |  `https://login.microsoftonline.de`

1. 將 Azure 備份代理更新安裝程式複製到伺服器。

    ![將 Azure 備份代理更新安裝程式複製到伺服器](./media/upgrade-mars-agent/copy-agent-installer.png)

1. 執行安裝程式。 打開 Microsoft Azure 恢復服務代理升級嚮導。

    ![微軟 Azure 恢復服務代理升級嚮導](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. 按 [下一步]****。

1. 按一下 **[升級]**。

    ![安裝微軟 Azure 恢復服務代理](./media/upgrade-mars-agent/upgrade-installation.png)

1. 最終確認螢幕指示 Azure 備份代理已成功更新。

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>適用于系統中心資料保護管理器 （SC DPM） 客戶

如果在系統中心資料保護管理器 （SC DPM） 伺服器上安裝了 Azure 備份代理，則需要按照以下步驟確定 DPM 服務器是否需要 Azure 備份代理更新：

1. 以管理員身份登錄到 SC DPM 服務器。
2. 打開 DPM 主控台。
3. 按一下主控台左下角導航中的**管理**。
4. 在左側導航中顯示的資訊中，查找 Azure 備份代理版本資訊。
5. 如果版本低於 2.0.9083.0，請下載最新的 Azure 備份代理安裝程式，並在 DPM 服務器上運行安裝程式以更新 Azure 備份代理。

對環境中的所有 DPM 服務器重複上述步驟。

## <a name="next-steps"></a>後續步驟

瞭解如何使用[Azure 備份 MARS 代理備份 Windows 電腦](backup-windows-with-mars-agent.md)
