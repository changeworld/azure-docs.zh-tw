---
title: 升級 Microsoft Azure 復原服務（MARS）代理程式
description: 瞭解如何升級 Microsoft Azure 復原服務（MARS）代理程式。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78672822"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>升級 Microsoft Azure 復原服務（MARS）代理程式

在本文中，您將了解如何：

* 識別具有舊版 MARS 代理程式的伺服器
* 更新這些伺服器上的 MARS 安裝

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>識別具有舊版 MARS 代理程式的伺服器

針對 Azure 備份代理程式和 Azure 備份伺服器的安裝：

1. 流覽至復原服務保存庫，其中您的已註冊伺服器可能會由舊版代理程式進行備份。 您可以在 Azure 的 Azure 備份更新警示中，找到具有舊版 Azure 備份代理程式的代表保存庫清單。
1. 在復原服務保存庫的左側 [**設定**] 區段中，選取 [**管理**] 區段下的 [**備份基礎結構**]。
1. 若要探索安裝在 Azure 備份伺服器安裝中的 Azure 備份代理程式，請移至 [**管理伺服器**] 下的 [**備份管理伺服器**]。 這會列出具有 Azure 備份伺服器安裝的伺服器，以及相關 Azure 備份代理程式的版本號碼。

    ![安裝為 Azure 備份 server 安裝一部分的 MARS 代理程式清單](./media/upgrade-mars-agent/backup-management-servers.png)

1. 若要檢查 Microsoft Azure 復原服務（MARS）代理程式安裝的代理程式版本或 Azure 備份代理程式，請移至 [**管理伺服器**] 底下的 [**受保護的伺服器**]。 然後選取 [備份管理類型] 底下的 [ **Azure 備份代理程式**]。 這會列出具有 Azure 備份代理程式安裝的伺服器以及安裝的版本號碼。

    ![已安裝 MARS 代理程式的伺服器清單](./media/upgrade-mars-agent/protected-servers.png)

1. 按一下 [MARS 代理程式安裝] 的 [**代理程式版本**] 欄，或 Azure 備份伺服器安裝的 [ **Azure 備份代理程式版本**] 欄，來排序 [Azure 備份代理程式版本] 欄。

1. 上一個步驟會提供 Azure 備份代理程式的伺服器清單，其版本低於2.0.9083.0 版或代理程式版本列為空白。 這些是 Azure 備份代理程式需要更新的伺服器。

## <a name="update-the-mars-agent-installation-on-the-server"></a>補救伺服器上的 MARS 代理程式安裝

一旦識別出需要 Azure 備份代理程式更新的伺服器，請為每個已識別的伺服器（使用 Azure 備份伺服器或 MARS 代理程式）執行下列步驟。 請先[下載最新版的 Azure 備份代理程式](https://aka.ms/azurebackup_agent)，再依照下列步驟執行。

1. 按一下 [Azure 備份代理程式低於2.0.9083.0 版] 或 [空白] 的資料列。 這會開啟 [伺服器詳細資料] 畫面。

    ![具有過期代理程式版本的受保護伺服器](./media/upgrade-mars-agent/old-agent-version.png)

    ![具有過期代理程式版本的 Azure 備份伺服器](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. 按一下 **[連線]** 以接收與伺服器連線的遠端桌面連線檔案，或透過伺服器上的 [遠端桌面連線] 直接連線到伺服器。

    ![透過遠端桌面連線連接到伺服器](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > 如果列出的伺服器不存在或已解除委任，您可以略過下列其餘步驟，並跳至下一個伺服器。

1. 輸入您的系統管理登入詳細資料，然後登入。

1. 如果您的伺服器或伺服器的 proxy 具有有限的網際網路存取權，請確定伺服器/proxy 上的防火牆設定已設定為允許您所使用之 Azure 雲端適用的 URL：

    Azure 雲端 | URL
    -- | ---
    Azure 雲端（公用） |   `https://login.windows.net`
    Azure 中國世紀雲端   | `https://login.chinacloudapi.cn`
    Azure 美國政府雲端 |   `https://login.microsoftonline.us`
    Azure 德國雲端  |  `https://login.microsoftonline.de`

1. 將 Azure 備份代理程式更新安裝程式複製到伺服器。

    ![將 Azure 備份代理程式更新安裝程式複製到伺服器](./media/upgrade-mars-agent/copy-agent-installer.png)

1. 執行安裝程式。 [Microsoft Azure 復原服務代理程式升級嚮導] 隨即開啟。

    ![Microsoft Azure 復原服務代理程式升級嚮導](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. 按 [下一步]  。

1. 按一下 **[升級]**。

    ![安裝 Microsoft Azure 復原服務代理程式](./media/upgrade-mars-agent/upgrade-installation.png)

1. 最後的確認畫面指出已成功更新 Azure 備份代理程式。

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>適用于 System Center Data Protection Manager （SC DPM）客戶

如果您已在 System Center Data Protection Manager （SC DPM）伺服器上安裝 Azure 備份代理程式，則必須遵循下列步驟來識別您的 DPM 服務器是否需要 Azure 備份代理程式更新：

1. 以系統管理員身分登入您的 SC DPM 服務器。
2. 開啟 DPM 主控台。
3. 在主控台的左下方導覽中，按一下 [**管理**]。
4. 在左側導覽所顯示的資訊中，尋找 Azure 備份代理程式版本資訊。
5. 如果版本低於2.0.9083.0 版，請下載最新的 Azure 備份代理程式安裝程式，並在 DPM 服務器上執行安裝程式，以更新 Azure 備份代理程式。

針對環境中的所有 DPM 服務器，重複上述步驟。

## <a name="next-steps"></a>後續步驟

瞭解如何[使用 AZURE 備份 MARS 代理程式來備份 Windows 機器](backup-windows-with-mars-agent.md)
