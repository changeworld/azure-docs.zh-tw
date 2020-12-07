---
title: 準備要使用 Azure Migrate 進行遷移的 Windows Server 2003 伺服器
description: 瞭解如何準備 Windows Server 2003 伺服器，以 Azure Migrate 進行遷移。
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: f8d3dea970d07d951467a44661e12000ba413f72
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753737"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>準備 Windows Server 2003 電腦以進行遷移

本文說明如何準備執行 Windows Server 2003 的機器，以遷移至 Azure。 


> [!NOTE]
> [Windows Server 2003 延伸支援](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) 已于2015年7月14日結束。  Azure 支援團隊會持續協助您針對在 Azure 上執行 Windows Server 2003 的問題進行疑難排解。 不過，這項支援僅限於不需要 OS 層級疑難排解或修補程式的問題。 建議您將應用程式遷移至執行較新版本 Windows Server 的 Azure 實例，以確保您有效地運用 Azure 雲端的彈性和可靠性。 但是，如果您仍然選擇將 Windows Server 2003 遷移至 Azure，則可以使用 Azure Migrate：伺服器遷移工具（如果您的 Windows Server 是在 VMware 或 Hyper-v 上執行的 VM）。


- 您可以使用無代理程式遷移將 [Hyper-v vm](tutorial-migrate-hyper-v.md) 和 [VMware Vm](tutorial-migrate-vmware.md) 遷移至 Azure。
- 為了在遷移之後連線到 Azure Vm，Hyper-v Integration Services 必須安裝在 Azure VM 上。 預設不會安裝 Windows Server 2003 電腦。
- 沒有安裝 Hyper-v Integration Services 的直接下載連結，因此您需要執行下列動作：
    - 針對沒有安裝它的 Hyper-v Vm，您可以解壓縮安裝檔案，以便在執行 Windows Server 2012 R2/Windows Server 2012 （具有 Hyper-v 角色）的電腦上 Integration Services，然後將安裝程式複製到 Windows Server 2003 電腦。 在執行 Windows Server 2016 的電腦上無法使用安裝檔案。
    - 針對 VMware Vm，您會建立啟動工作，以在 Azure VM 于遷移後啟動時安裝 Integration Services。


## <a name="install-on-hyper-v-vms"></a>在 Hyper-v Vm 上安裝

在遷移之前，請檢查是否已安裝 Hyper-v Integration Services，然後視需要進行安裝。

1. 遵循 [這些指示](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) 來檢查是否已安裝。
2. 如果未安裝，請使用 Hyper-v 角色登入執行 Windows Server 2012 R2/Windows Server 2012 的電腦。
3. 流覽至位於 **C:\Windows\System32\vmguest.iso** 的安裝檔案，然後掛接檔案。
2. 將安裝資料夾複製到 Windows Server 2003 電腦，並安裝 Integration Services。
4. 安裝之後，您可以保留 Integration Services 中的預設設定。 

## <a name="install-on-vmware-vms"></a>在 VMware Vm 上安裝

1. 使用 Hyper-v 角色登入執行 Windows Server 2012 R2/Windows Server 2012 的電腦。
2. 流覽至位於 **C:\Windows\System32\vmguest.iso** 的安裝檔案，然後掛接檔案。
3. 將安裝資料夾複製到 VMware VM。
4. 從 VM 上的命令列執行 ```gpedit.msc``` 。
5. 開啟 [**電腦**  >  **設定 Windows 設定**  >  **腳本] (啟動/關機)**。
6. 在 [**啟動**  >  **新增**  >  **腳本名稱**] 中，輸入 setup.exe 位址。
7. 在遷移至 Azure 之後，腳本會在 Azure VM 第一次啟動時執行。
8. 手動重新開機 Azure VM。 開機診斷中會出現一個快顯視窗，指出需要重新開機。
9. 在執行腳本並將 Hyper-v Integration Services 安裝在 Azure VM 之後，您可以從啟動中移除腳本。
10. 安裝之後，您可以保留 Integration Services 中的預設設定。 

## <a name="next-steps"></a>後續步驟

- 檢查 [VMware](migrate-support-matrix-vmware-migration.md) 和 [hyper-v](migrate-support-matrix-hyper-v-migration.md) vm 的遷移需求。
- 遷移 [VMware](server-migrate-overview.md) 和 [hyper-v](tutorial-migrate-hyper-v.md) vm。
