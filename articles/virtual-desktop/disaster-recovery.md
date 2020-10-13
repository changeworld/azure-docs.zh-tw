---
title: 設定 Windows 虛擬桌面嚴重損壞修復計畫-Azure
description: 如何為您的 Windows 虛擬桌面部署設定商務持續性和嚴重損壞修復計畫。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 968f82a143872fd282222539ab71a70db488a20d
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935671"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>設定商務持續性和嚴重損壞修復計畫

為了保護您組織的資料安全，您可能需要採用商務持續性和嚴重損壞修復 (BCDR) 策略。 音效 BCDR 策略可讓您的應用程式和工作負載在規劃和未規劃的服務或 Azure 中斷期間保持運作。

Windows 虛擬桌面提供 Windows 虛擬桌面服務的 BCDR，以在中斷期間保留客戶的中繼資料。 當區域發生中斷時，服務基礎結構元件會容錯移轉至次要位置，並繼續正常運作。 您仍然可以存取服務相關的中繼資料，而使用者仍可連接到可用的主機。 只要租使用者環境或主機保持可供存取，終端使用者連線就會保持連線。

若要確保使用者在區域中斷時仍能連線，您需要將虛擬機器 (Vm) 在不同的位置。 在中斷期間，主要網站會容錯移轉至次要位置中的已複寫 Vm。 使用者可以繼續從次要位置存取應用程式，而不會中斷。 在 VM 複寫的最上層，您需要讓使用者身分識別可在次要位置進行存取。 如果您使用的是設定檔容器，則也需要進行複寫。 最後，請確定相依于主要位置資料的商務應用程式，可以與其餘數據進行容錯移轉。

總而言之，為了讓您的使用者在中斷時保持連線，您必須依照下列循序執行下列動作：

- 複寫次要位置中的 Vm。
- 如果您使用的是設定檔容器，請在次要位置設定資料複寫。
- 請確定您在主要位置中設定的使用者身分識別可在次要位置中使用。
- 請確定所有依賴主要位置資料的企業營運應用程式都已容錯移轉至次要位置。

## <a name="vm-replication"></a>VM 複寫

首先，您必須將 Vm 複寫至次要位置。 這樣做的選項取決於您的 Vm 的設定方式：

- 您可以使用 Azure Site Recovery 設定集區和個人主機集區的所有 Vm。 使用此方法時，您只需要設定一個主機集區及其相關的應用程式群組和工作區。
- 您可以在容錯移轉區域中建立新的主機集區，同時讓容錯移轉位置中的所有資源關閉。 針對此方法，您必須在容錯移轉區域中設定新的應用程式群組和工作區。 然後，您可以使用 Azure Site Recovery 計畫來開啟主機集區。
- 您可以建立主機集區，該主機集區是在主要和容錯移轉區域內建的 Vm 填入，同時讓容錯移轉區域中的 Vm 關閉。 在此情況下，您只需要設定一個主機集區及其相關的應用程式群組和工作區。 您可以使用此方法，使用 Azure Site Recovery 計畫來開啟主機集區的電源。

建議您使用 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 來管理其他 azure 位置中的複寫 vm，如 [azure 至 azure 的嚴重損壞修復架構](../site-recovery/azure-to-azure-architecture.md)中所述。 我們特別建議針對個人主機集區使用 Azure Site Recovery，因為 Azure Site Recovery 支援以 [伺服器為基礎和用戶端為基礎的 sku](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems)。

如果您使用 Azure Site Recovery，就不需要手動註冊這些 Vm。 次要 VM 中的 Windows 虛擬桌面代理程式將會自動使用最新的安全性權杖，以連接到最接近它的服務實例。 次要位置中的 VM (工作階段主機) 會自動成為主機集區的一部分。 終端使用者必須在程式期間重新連接，但是除此之外，也沒有其他手動作業。

如果中斷期間有現有的使用者連接，系統管理員必須先結束目前區域中的使用者連線，系統管理員才能開始容錯移轉至次要區域。

若要將 Windows 虛擬桌面中的使用者中斷連線 (傳統) ，請執行下列 Cmdlet：

```powershell
Invoke-RdsUserSessionLogoff
```

若要在 Azure 整合版的 Windows 虛擬桌面中中斷使用者的連線，請執行下列 Cmdlet：

```powershell
Remove-AzWvdUserSession
```

當您將主要區域中的所有使用者登出之後，您就可以容錯移轉主要區域中的 Vm，並讓使用者連線到次要區域中的 Vm。 如需有關此程式如何運作的詳細資訊，請參閱 [將 Azure vm 複寫至另一個 azure 區域](../site-recovery/azure-to-azure-how-to-enable-replication.md)。

## <a name="virtual-network"></a>虛擬網路

接下來，請考慮中斷期間的網路連線能力。 您必須確定已在次要區域中 (VNET) 設定虛擬網路。 如果您的使用者需要存取內部部署資源，您必須設定此 VNET 來存取它們。 您可以使用 VPN、ExpressRoute 或虛擬 WAN 來建立內部部署連線。

建議您在容錯移轉區域中使用 Azure Site Recovery 設定 VNET，因為它會保留您的主要網路設定，而不需要對等互連。

## <a name="user-identities"></a>使用者身分識別

接下來，請確定網域控制站可在次要位置上使用。 

有三種方式可讓網域控制站保持可用：

   - 在次要位置有 Active Directory 網網域控制站
   - 使用內部部署 Active Directory 網網域控制站
   - 使用[Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)複寫 Active Directory 網網域控制站

## <a name="user-and-app-data"></a>使用者和應用程式資料

如果您使用的是設定檔容器，下一步是在次要位置設定資料複寫。 您有五個選項可以儲存 FSLogix 設定檔：

   - 儲存空間直接存取 (S2D)
   - 具有額外磁片磁碟機的網路磁碟機機 (VM) 
   - Azure 檔案
   - Azure NetApp Files
   - 用於複寫的雲端快取

如需詳細資訊，請參閱 [Windows 虛擬桌面中 FSLogix 設定檔容器的儲存體選項](store-fslogix-profile.md)。

如果您要為設定檔設定嚴重損壞修復，您可以選擇下列選項：

   - 設定原生 Azure 複寫 (例如，Azure 檔案儲存體標準儲存體帳戶複寫、Azure NetApp Files 複寫或 Azure 檔案儲存體同步處理檔案伺服器) 。
    
     >[!NOTE]
     >NetApp replication 會在您第一次設定之後自動完成。 您可以使用 Azure Site Recovery 方案來新增前置腳本和後置腳本，以容錯移轉非 VM 資源 Azure 儲存體資源。

   - 為應用程式和使用者資料設定 FSLogix Cloud Cache。
   - 只針對應用程式資料設定嚴重損壞修復，以確保隨時都能存取商務關鍵資料。 使用這個方法時，您可以在中斷結束後取出使用者資料。

讓我們來看看如何設定 FSLogix 來設定每個選項的嚴重損壞修復。

### <a name="fslogix-configuration"></a>FSLogix 設定

如果您設定 FSLogix 的登錄專案，FSLogix 代理程式可以支援多個設定檔位置。

若要設定登錄專案：

1. 開啟 [ **登錄編輯程式**]。
2. 移至 [**電腦**  >  **HKEY_LOCAL_MACHINE**  >  **軟體**  >  **FSLogix**  >  **設定檔**]。
   
     > [!div class="mx-imgBorder"]
     > ![[登錄編輯程式] 中 [設定檔] 視窗的螢幕擷取畫面。 已選取 [VHDLocation]。](media/regedit-profiles.png)

3. 以滑鼠右鍵按一下 **VHDLocations** ，然後選取 [ **編輯多字串**]。

     > [!div class="mx-imgBorder"]
     > ![[編輯多字串] 視窗的螢幕擷取畫面。 值資料會列出 Centrual 美國和美國東部的位置。](media/multi-string-edit.png)

4. 在 [ **數值資料** ] 欄位中，輸入您想要使用的位置。
5. 完成後，選取 [確定]。

如果第一個位置無法使用，FSLogix 代理程式會自動容錯移轉至第二個位置，依此類推。

建議您將 FSLogix 代理程式設定為主要區域中的次要位置路徑。 當主要位置關機之後，FLogix 代理程式會複寫為 VM Azure Site Recovery 複寫的一部分。 當複寫的 Vm 準備就緒之後，代理程式會自動嘗試指向次要區域的路徑。

例如，假設您的主要工作階段主機 Vm 位於美國中部區域，但您的設定檔容器位於美國中部區域，以確保效能考慮。

在此情況下，您會使用美國中部的儲存體路徑來設定 FSLogix 代理程式。 您可以將工作階段主機 Vm 設定為在美國西部複寫。 一旦美國中部的路徑失敗，代理程式會嘗試為美國西部的儲存體建立新的路徑。

### <a name="s2d"></a>S2D

由於 S2D 會在內部跨區域處理複寫，因此您不需要手動設定次要路徑。

### <a name="network-drives-vm-with-extra-drives"></a>具有額外磁片磁碟機的網路磁碟機機 (VM) 

如果您使用 Azure Site Recovery （例如工作階段主機 Vm）來複寫網路儲存體 Vm，則復原會保留相同的路徑，這表示您不需要重新設定 FSlogix。

### <a name="azure-files"></a>Azure 檔案

Azure 檔案儲存體支援您在建立儲存體帳戶時可指定的跨區域非同步複寫。 如果 Azure 檔案儲存體的非同步本質已經涵蓋您的嚴重損壞修復目標，則您不需要進行其他設定。

如果您需要同步複寫以將資料遺失降到最低，建議您改為使用 FSLogix Cloud Cache。

>[!NOTE]
>本節未涵蓋 Azure 檔案儲存體的容錯移轉驗證機制。

### <a name="azure-netapp-files"></a>Azure NetApp Files

若要深入瞭解 Azure NetApp Files，請參閱 [建立 Azure Netapp files 的複寫對等互連](../azure-netapp-files/cross-region-replication-create-peering.md)。

## <a name="app-dependencies"></a>應用程式相依性

最後，請確定依賴位於主要區域之資料的任何商務應用程式都可以容錯移轉至次要位置。 此外，請務必設定應用程式在新位置中的工作所需的設定。 例如，如果其中一個應用程式相依于 SQL 後端，請務必複寫次要位置中的 SQL。 您應該將應用程式設定為使用次要位置做為容錯移轉程式的一部分或作為其預設設定。 您可以根據 Azure Site Recovery 方案來建立應用程式相依性的模型。 若要深入瞭解，請參閱 [關於復原方案](../site-recovery/recovery-plan-overview.md)。

## <a name="disaster-recovery-testing"></a>嚴重損壞修復測試

在您完成設定嚴重損壞修復之後，您會想要測試您的計畫，以確定它可以運作。

以下是一些如何測試方案的建議：

- 如果測試 Vm 有網際網路存取權，則會接管任何現有的工作階段主機以進行新的連線，但與原始工作階段主機的所有現有連接都會保持作用中狀態。 請確定執行測試的系統管理員會在測試計劃之前，先登出所有作用中的使用者。 
- 您應該只在維護期間執行完整的嚴重損壞修復測試，不會干擾您的使用者。 您也可以使用驗證環境中的主機集區來進行測試。 
- 確定您的測試涵蓋所有商務關鍵應用程式。
- 建議您一次最多隻容錯移轉到100個 Vm。 如果您的 Vm 數目超過此數目，建議您在10分鐘內將它們分開進行容錯移轉。

## <a name="next-steps"></a>後續步驟

如果您有關于如何在規劃中斷的情況下維護資料安全的問題，請參閱我們的 [安全性指南](security-guide.md)。