---
title: Microsoft Azure 復原服務 (MARS) 代理程式升級
description: 瞭解如何 Microsoft Azure 復原服務 (MARS) 代理程式升級。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: a1ee26db962781643e9599069282647658301bac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89181471"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure 復原服務 (MARS) 代理程式升級

在本文中，您將了解如何：

* 使用舊版 MARS 代理程式識別伺服器
* 更新這些伺服器上的 MARS 安裝

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>使用舊版 MARS 代理程式識別伺服器

針對 Azure 備份代理程式和 Azure 備份 server 的安裝：

1. 流覽至復原服務保存庫，其中您的已註冊伺服器可能會由舊版的代理程式進行備份。 您可以在 Azure 備份從 Azure 更新警示中找到具有舊版 Azure 備份代理程式的代表性保存庫清單。
1. 在 [復原服務保存庫] 的 [左側**設定**] 區段中，選取 [**管理**] 區段下的 [**備份基礎結構**]。
1. 若要探索 Azure 備份 server 安裝過程中安裝的 Azure 備份代理程式，請移至 [**管理伺服器**] 下的 [**備份管理伺服器**]。 這會列出具有 Azure 備份 server 安裝的伺服器，以及相關 Azure 備份代理程式的版本號碼。

    ![安裝為 Azure 備份 server 安裝一部分的 MARS 代理程式清單](./media/upgrade-mars-agent/backup-management-servers.png)

1. 若要檢查 Microsoft Azure 復原服務的代理程式版本 (MARS) 代理程式安裝或 Azure 備份代理程式，請移至 [**管理伺服器**] 底下的 [**受保護的伺服器**]。 然後選取 [備份管理類型] 下的 **Azure 備份代理程式** 。 這會列出已安裝 Azure 備份代理程式的伺服器，以及安裝的版本號碼。

    ![已安裝 MARS 代理程式的伺服器清單](./media/upgrade-mars-agent/protected-servers.png)

1. 選取 MARS 代理程式安裝的 [ **代理程式版本** ] 資料行或 Azure 備份伺服器安裝的 [ **Azure 備份代理程式版本** ] 欄，以排序 Azure 備份代理程式版本資料行。

1. 上一個步驟會提供您 Azure 備份代理程式的伺服器清單，而此代理程式的版本低於2.0.9083.0 版或代理程式版本列為空白。 這些是需要更新 Azure 備份代理程式的伺服器。

## <a name="update-the-mars-agent-installation-on-the-server"></a>補救伺服器上的 MARS 代理程式安裝

一旦識別出需要 Azure 備份代理程式更新的伺服器之後，請使用 Azure 備份 server 或 MARS 代理程式) ，針對每個已識別的伺服器 (執行下列步驟。 請先[下載最新版的 Azure 備份代理程式](https://aka.ms/azurebackup_agent)，然後再遵循下列步驟。

1. 選取 Azure 備份代理程式低於2.0.9083.0 版或空白的資料列。 這會開啟 [伺服器詳細資料] 畫面。

    ![使用過期代理程式版本的受保護伺服器](./media/upgrade-mars-agent/old-agent-version.png)

    ![使用過期代理程式版本的 Azure 備份伺服器](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. 選取 **[連線]** 以接收遠端桌面連線檔案，以與伺服器連接，或直接透過伺服器上的遠端桌面連線連接到伺服器。

    ![透過遠端桌面連線連接到伺服器](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > 如果列出的伺服器不存在或已解除委任，您可以忽略下列其餘步驟，並跳至下一個伺服器。

1. 輸入您的系統管理登入詳細資料並登入。

1. 如果您的伺服器或伺服器的 proxy 具有有限的網際網路存取權，請確定已設定伺服器/proxy 上的防火牆設定，以允許適用于您所使用之 Azure 雲端的 URL：

    Azure 雲端 | URL
    --- | ---
    Azure 雲端 (公用)  |   `https://login.windows.net`
    Azure 中國世紀雲端   | `https://login.chinacloudapi.cn`
    Azure US Government Cloud |   `https://login.microsoftonline.us`
    Azure 德國雲端  |  `https://login.microsoftonline.de`

1. 將 Azure 備份代理程式更新安裝程式複製到伺服器。

    ![將 Azure 備份代理程式更新安裝程式複製到伺服器](./media/upgrade-mars-agent/copy-agent-installer.png)

1. 執行安裝程式。 [Microsoft Azure 復原服務代理程式升級嚮導] 隨即開啟。

    ![Microsoft Azure 復原服務代理程式升級嚮導](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. 選取 [下一步]  。

1. 選取 [ **升級**]。

    ![Microsoft Azure 復原服務代理程式的安裝](./media/upgrade-mars-agent/upgrade-installation.png)

1. 最後一個確認畫面會指出已成功更新 Azure 備份代理程式。

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>System Center Data Protection Manager (SC DPM) 客戶

如果您已在 System Center Data Protection Manager (SC DPM) 伺服器上安裝 Azure 備份代理程式，則需要遵循下列步驟來識別您的 DPM 服務器是否需要 Azure 備份代理程式更新：

1. 以系統管理員身分登入您的 SC DPM 服務器。
2. 開啟 DPM 主控台。
3. 在主控台的左下方導覽中選取 [ **管理** ]。
4. 在顯示在左側導覽的資訊中，尋找 Azure 備份代理程式版本資訊。
5. 如果版本低於2.0.9083.0 版，請下載最新的 Azure 備份代理程式安裝程式，並在 DPM 服務器上執行安裝程式，以更新 Azure 備份代理程式。

針對您環境中的所有 DPM 服務器重複上述步驟。

## <a name="next-steps"></a>後續步驟

瞭解如何 [使用 AZURE 備份 MARS 代理程式來備份 Windows 機器](backup-windows-with-mars-agent.md)
