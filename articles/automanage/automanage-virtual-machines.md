---
title: 適用于虛擬機器的 Azure Automanage
description: 瞭解適用于虛擬機器的 Azure Automanage。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 0d8ce501b951f3543e1baf54c8a52648b13f6e66
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695665"
---
# <a name="azure-automanage-for-virtual-machines"></a>適用于虛擬機器的 Azure Automanage

本文涵蓋適用于虛擬機器的 Azure Automanage 相關資訊，其優點如下：

- 以智慧方式將上線虛擬機器，以選取 Azure 服務的最佳作法
- 每個 Azure 最佳做法都會自動設定每個服務
- 偵測到的漂移和修正監視器
- 提供簡單的體驗 (點、按一下、設定、忘記) 


## <a name="overview"></a>概觀

適用于虛擬機器的 Azure Automanage 是一項服務，可免除探索的需要、瞭解如何上線，以及如何在 Azure 中設定可受益于您虛擬機器的特定服務。 這些服務可協助增強虛擬機器的可靠性、安全性及管理，並被視為 Azure 最佳做法服務，例如 [azure 更新管理](../automation/update-management/overview.md) 和 [Azure 備份](../backup/backup-overview.md) -只需要命名。

將虛擬機器上架至 Azure Automanage 之後，它會自動將每個最佳做法服務設定為其建議的設定。 每項服務的最佳作法各不相同。 其中一個範例可能是 Azure 備份，其中最佳作法可能是每天備份一次虛擬機器，並保留期限為6個月。

當偵測到時，Azure Automanage 也會自動監視漂移並加以修正。 這表示，如果您的虛擬機器上線至 Azure Automanage，我們將不只針對每個 Azure 最佳作法進行設定，但我們將監視您的電腦，以確保它能繼續符合整個生命週期的最佳做法。 如果您的虛擬機器確實偏離或偏離這些實務，我們將會更正它，並將您的機器取回到預期的狀態。

最後，經驗很簡單。


## <a name="prerequisites"></a>必要條件

在您嘗試啟用虛擬機器上的 Azure Automanage 之前，必須考慮幾個必要條件。

- 僅限 Windows Server Vm
- Vm 必須正在執行
- Vm 必須位於支援的區域 (請參閱下面的段落) 
- 使用者必須有正確的許可權 (請參閱下面的段落) 
- Automanage 目前不支援沙箱訂閱

也請務必注意，Automanage 僅支援位於下欄區域的 Windows Vm：西歐、美國東部、美國西部2、加拿大中部、美國中西部、日本東部。

您必須擁有包含 Vm 之資源群組的「 **參與者** 」角色，才能使用現有的 Automanage 帳戶在 vm 上啟用 Automanage。 如果您要使用新的 Automanage 帳戶啟用 Automanage，您需要訂用帳戶的下列許可權： **擁有** 者角色或 **參與者** ，以及 **使用者存取系統管理員** 角色。

> [!NOTE]
> 如果您想要在連線至不同訂用帳戶中工作區的 VM 上使用 Automanage，您必須擁有上述每個訂用帳戶的許可權。

## <a name="participating-services"></a>參與的服務

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="以智慧方式將服務上架。":::

請參閱適用于 [虛擬機器的 Azure Automanage 最佳作法](virtual-machines-best-practices.md) ，以取得參與的 azure 服務完整清單，以及其支援的設定設定檔。

 我們會自動將您帶到這些參與的服務。 這些是我們的最佳作法白皮書，您可以在我們的 [雲端採用架構](/azure/cloud-adoption-framework/manage/azure-server-management)中找到這份白皮書。

針對這些服務，我們將會自動上架、autoconfigure、監視漂移，以及在偵測到漂移時進行監看。


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>在 Azure 入口網站中啟用 Vm 的 Automanage

在 Azure 入口網站中，您可以在現有的虛擬機器上或在建立新的虛擬機器時啟用 Automanage。 如需此程式的簡潔步驟，請參閱 [虛擬機器快速入門的 Automanage](quick-create-virtual-machines-portal.md)。

如果這是您第一次啟用 VM 的 Automanage，您可以在 Azure 入口網站中搜尋 **Automanage – Azure 虛擬機器的最佳做法**。 按一下 [ **在現有的 VM 上啟用**]，選取您要上架的 vm，按一下 [ **選取**]，再按一下 [ **啟用**]，即可完成。

當我們嘗試修復您的 VM 時，您可能需要與此 VM 互動以管理這些服務，但無法執行這項作業。 如果我們成功修復您的 VM，我們會將它恢復合規性，而不會對您發出警示。


## <a name="configuration-profiles"></a>組態設定檔

當您啟用虛擬機器的 Automanage 時，需要設定設定檔。 設定檔是這項服務的基礎。 他們完全定義了哪些服務會讓您的機器上線，以及某些程度的服務設定。

### <a name="default-configuration-profiles"></a>預設設定設定檔

目前有兩個設定檔可用。

- **Azure 虛擬機器的最佳作法-開發/測試** 設定設定檔是專為開發/測試機器所設計。
- **Azure 虛擬機器的最佳作法-生產環境** 設定檔適用于生產環境。

這項差異的原因是因為建議根據工作負載執行的特定服務。 比方說，在實際執行的機器中，我們會自動讓您進入 Azure 備份。 不過，對於開發/測試電腦而言，備份服務會是不必要的成本，因為開發/測試機器通常會對業務造成較低的影響。

### <a name="customizing-a-configuration-profile-using-preferences"></a>使用喜好設定自訂設定檔

除了我們將您上架的標準服務，我們還可讓您設定特定的喜好設定子集。 這些喜好設定可以在不違反我們最佳作法的設定選項範圍內使用。 例如，在 Azure 備份的案例中，我們將允許您定義備份的頻率，以及它發生在星期幾的哪一天。 不過，我們 *不* 會讓您完全關閉 Azure 備份。

> [!NOTE]
> 在開發/測試設定設定檔中，我們完全不會備份 VM。

您可以透過喜好設定來調整預設設定設定檔的設定。 [在這裡](virtual-machines-custom-preferences.md)瞭解如何建立喜好設定。

> [!NOTE]
> 啟用 Automanage 時，您無法變更 VM 上的設定檔。 您將需要停用該 VM 的 Automanage，然後使用所需的設定設定檔和喜好設定來重新啟用 Automanage。


## <a name="automanage-account"></a>Automanage 帳戶

Automanage 帳戶是安全性內容或執行自動化作業所用的身分識別。 通常，您不需要選取 [Automanage 帳戶] 選項，但如果有委派案例，而您想要將資源的自動化管理分割 (可能是在兩個系統管理員) 之間，此選項可讓您為每個系統管理員定義 Azure 身分識別。

在 Azure 入口網站體驗中，當您在 Vm 上啟用 Automanage 時，[ **啟用 AZURE VM 最佳作法** ] 分頁上會有一個 Advanced 下拉式清單，可讓您指派或手動建立 Automanage 帳戶。

Automanage 帳戶將會被授與 **參與者** 和 **資源原則參與者** 角色給訂用帳戶 (s) 包含) 您上架到 Automanage 的電腦 (s。 您可以跨多個訂用帳戶在電腦上使用相同的 Automanage 帳戶，這會授與 Automanage 帳戶 **參與者** 和 **資源原則參與者** 在所有訂用帳戶上的許可權。

如果您的 VM 連線到另一個訂用帳戶中的 Log Analytics 工作區，則 Automanage 帳戶也會被授與該其他訂用帳戶中的 **參與者** 和 **資源原則參與者** 。

如果您要使用新的 Automanage 帳戶啟用 Automanage，您需要訂用帳戶的下列許可權： **擁有** 者角色或 **參與者** ，以及 **使用者存取系統管理員** 角色。

如果您要使用現有的 Automanage 帳戶來啟用 Automanage，您必須在包含您 Vm 的資源群組上擁有「 **參與者** 」角色。

> [!NOTE]
> 當您停用 Automanage 最佳做法時，Automanage 帳戶對任何相關聯訂用帳戶的許可權將會保留。 若要手動移除許可權，請移至訂用帳戶的 IAM 頁面，或刪除 Automanage 帳戶。 如果 Automanage 帳戶仍在管理任何電腦，就無法刪除該帳戶。


## <a name="status-of-vms"></a>Vm 的狀態

在 Azure 入口網站中，移至 [ **Automanage – Azure 虛擬機器最佳作法** ] 頁面，其中會列出所有自動管理的 vm。 在這裡，您將會看到每部虛擬機器的整體狀態。

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="已設定的虛擬機器清單。":::

針對每個列出的 VM，會顯示下列詳細資料：名稱、設定檔、設定喜好設定、狀態、帳戶、訂用帳戶和資源群組。

[ **狀態** ] 欄可能會顯示下列狀態：
- *進行中* -VM 剛啟用且正在進行設定
- *已設定* -VM 已設定且未偵測到漂移
- *失敗* -VM 已漂移，所以無法補救
- *暫* 止-vm 目前未執行，而且 Automanage 會在 vm 下次執行時嘗試上架或補救 vm

如果您看到 **狀態** 為 [ *失敗*]，您可以透過您的 VM 所在的資源群組進行部署的疑難排解。 移至 [ **資源群組**]，選取您的資源群組，按一下 [ **部署** ]，並在該處查看 *失敗* 的狀態以及錯誤詳細資料。


## <a name="disabling-automanage-for-vms"></a>停用 Vm 的 Automanage

您可能會決定一天停用特定 Vm 上的 Automanage。 比方說，您的電腦正在執行一些超級敏感性的安全工作負載，而且您需要將其鎖定到 Azure 會自然地進行，因此您需要在 Azure 最佳做法之外設定電腦。

若要在 Azure 入口網站中執行這項操作，請移至 [ **Automanage – Azure 虛擬機器最佳作法** ] 頁面，其中會列出所有自動管理的 vm。 選取您要從 Automanage 停用的虛擬機器旁的核取方塊，然後按一下 [ **停用 automanagment** ] 按鈕。

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="在虛擬機器上停用 Automanage。":::

先仔細閱讀所產生快顯視窗中的訊息，然後同意 [停用]。

> [!NOTE]
> 停用 VM 中的 automanagement 會產生下列行為：
>
> - VM 的設定及其上線的服務不會變更。
> - 這些服務所產生的任何費用都會保持計費，並持續產生。
> - 任何 Automanage 行為都會立即停止。


首先，最重要的是，我們不會將虛擬機器從我們上線它並設定的任何服務中關閉。 因此，這些服務所產生的任何費用都會繼續維持計費。 如有必要，您將需要關閉面板。 任何 Automanage 行為都會立即停止。 例如，我們將不再監視 VM 的漂移。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解虛擬機器的 Automanage 提供一種方法，可讓您不需要瞭解、上架及設定最佳作法的 Azure 服務。 此外，如果您從設定設定檔所設定的虛擬機器上線至 Automanage 的機器，我們會自動使其符合規範。

請嘗試在 Azure 入口網站中啟用虛擬機器的 Automanage。

> [!div class="nextstepaction"]
> [針對 Azure 入口網站中的虛擬機器啟用 Automanage](quick-create-virtual-machines-portal.md)