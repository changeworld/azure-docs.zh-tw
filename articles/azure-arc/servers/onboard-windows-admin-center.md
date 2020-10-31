---
title: 從 Windows Admin Center 將混合式機器連線至 Azure
description: 在本文中，您將瞭解如何使用 Windows Admin Center Azure Arc 啟用的伺服器，安裝代理程式並將機器連線到 Azure。
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: d47e3dd4434d719f890b64e4cdfb12a189c0632a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133588"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>從 Windows Admin Center 將混合式機器連線至 Azure

您可以藉由手動執行一組步驟，為環境中的一或多部 Windows 電腦啟用 Azure Arc 啟用的伺服器。 或者，您可以使用 [Windows Admin Center](/windows-server/manage/windows-admin-center/understand/what-is) 部署已連線的機器代理程式，並註冊您的內部部署伺服器，而不需要在此工具之外執行任何步驟。

## <a name="prerequisites"></a>Prerequisites

* 啟用 Arc 的伺服器-請檢查 [必要條件](agent-overview.md#prerequisites) ，並確認您的訂用帳戶、Azure 帳戶和資源符合需求。

* Windows Admin Center-請參閱 [準備您的環境](/windows-server/manage/windows-admin-center/deploy/prepare-environment) 以部署和 [設定 Azure 整合 ](/windows-server/manage/windows-admin-center/azure/azure-integration)的需求。

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 您要管理的目標 Windows 伺服器必須具備網際網路連線能力，才能存取 Azure。

### <a name="security"></a>安全性

此部署方法會要求您在目標 Windows 電腦或伺服器上擁有系統管理員許可權，才能安裝及設定代理程式。 您也必須是「 [**閘道使用者**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) 」角色的成員。

## <a name="deploy"></a>部署

請執行下列步驟，以設定啟用 Arc 的伺服器的 Windows 伺服器。

1. 登入 Windows Admin Center。

1. 從 [ **總覽** ] 頁面上的 [連接] 清單中，從 [已連接的 Windows 伺服器] 清單中選取要連接的伺服器。

1. 從左側窗格中選取 [ **Azure 混合式服務** ]。

1. 在 [ **azure 混合式服務** ] 頁面上，選取 [ **探索 azure 服務** ]。

1. 在 [ **探索 azure 服務** ] 頁面上，于 [ **利用 azure 原則和解決方案來管理您的伺服器] Azure Arc** 中，選取 [ **設定** ]。

1. 如果系統提示您向 Azure 進行驗證，請在 [ **伺服器的 Settings\Azure Arc** ] 頁面上選取 [ **開始** 使用]。

1. 在 [ **將伺服器連接到 Azure]** 頁面上，提供下列各項：

    1. 在 [ **azure 訂** 用帳戶] 下拉式清單中，選取 azure 訂用帳戶。
    1. 針對 [ **資源群組** ]，請選取 [ **新增** ] 以建立新的資源群組，或在 [ **資源群組** ] 下拉式清單中，選取要註冊及管理機器的現有資源群組。
    1. 在 [ **區域** ] 下拉式清單中，選取要儲存伺服器中繼資料的 Azure 區域。
    1. 如果電腦或伺服器透過 proxy 伺服器進行通訊，以連線到網際網路，請選取 [ **使用 proxy 伺服器** ] 選項。 指定 proxy 伺服器的 IP 位址或名稱，以及機器將用來與 proxy 伺服器通訊的埠號碼。

1. 選取 [設定] 以繼續 **設定** 已啟用 Azure Arc 之伺服器的 Windows 伺服器。

Windows server 將會連線至 Azure、下載連線的機器代理程式、安裝該代理程式，並向 Azure Arc 啟用的伺服器註冊。 若要追蹤進度，請選取功能表中的 [ **通知** ]。

若要確認已連線的機器代理程式安裝，請在 Windows Admin Center 從左側窗格中選取 [ [**事件**](/windows-server/manage/windows-admin-center/use/manage-servers#events) ]，以查看應用程式事件記錄檔中的 *MsiInstaller* 事件。

## <a name="verify-the-connection-with-azure-arc"></a>驗證與 Azure Arc 的連線

在安裝好代理程式並將其設定為連線至已啟用 Azure Arc 的伺服器之後，請移至 Azure 入口網站以確認伺服器已成功連線。 在 [Azure 入口網站](https://portal.azure.com)中檢視您的電腦。

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="電腦成功連線" border="false":::

## <a name="next-steps"></a>後續步驟

* 疑難排解資訊可在「連線的 [機器代理程式疑難排解指南](troubleshoot-agent-onboard.md)」中找到。

* 了解如何使用 [Azure 原則](../../governance/policy/overview.md)，針對例如 VM [來賓設定](../../governance/policy/concepts/guest-configuration.md)、確認機器回報至預期的 Log Analytics 工作區、使用 [Azure 監視器與 VM](../../azure-monitor/insights/vminsights-enable-policy.md) 啟用監視等等項目，管理您的機器。

* 深入了解 [Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)。 當您想要收集作業系統和工作負載監視資料、使用自動化 runbook 或功能（例如更新管理）進行管理時，或使用其他 Azure 服務（例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md)）時，需要適用于 Windows 和 Linux 的 Log Analytics 代理程式。