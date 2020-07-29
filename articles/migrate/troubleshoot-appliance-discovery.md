---
title: 針對 Azure Migrate 設備部署和探索進行疑難排解
description: 取得部署 Azure Migrate 設備及探索機器的協助。
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: eafe13adb5b37de2de2bc4eb8bf15c775af0b039
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171861"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>針對 Azure Migrate 設備和探索進行疑難排解

本文可協助您針對部署[Azure Migrate](migrate-services-overview.md)設備時的問題進行疑難排解，並使用設備來探索內部部署機器。


## <a name="whats-supported"></a>支援的項目？

請[參閱](migrate-appliance.md)設備支援需求。


## <a name="invalid-ovf-manifest-entry"></a>「OVF 資訊清單專案無效」

如果您收到「提供的資訊清單檔案無效：不正確 OVF 資訊清單專案」錯誤，請執行下列動作：

1. 藉由檢查其雜湊值，確認已正確下載 Azure Migrate 設備 OVA 檔案。 [深入了解](./tutorial-prepare-vmware.md)。 如果雜湊值不相符，請再次下載 OVA 檔案，然後重試部署。
2. 如果部署仍然失敗，而且您使用 VMware vSphere 用戶端來部署 OVF 檔案，請嘗試透過 vSphere web 用戶端進行部署。 如果部署仍然失敗，請嘗試使用不同的網頁瀏覽器。
3. 如果您使用 vSphere web 用戶端，並嘗試在 vCenter Server 6.5 或6.7 上部署它，請嘗試直接在 ESXi 主機上部署 OVA：
   - 使用 web 用戶端（HTTPs://<*主機 IP 位址*>/ui）直接連接到 ESXi 主機（而不是 vCenter Server）。
   - 在 [**首頁**清查] 中，選取 [檔案] [  >  **Inventory** **File**  >  **部署 OVF 範本**]。 流覽至 OVA 並完成部署。
4. 如果部署仍然失敗，請連絡 Azure Migrate 的支援。

## <a name="cant-connect-to-the-internet"></a>無法連線到網際網路

如果設備機器位於 proxy 後方，就會發生這種情況。

- 如果 proxy 需要授權認證，請務必提供這些認證。
- 如果您使用以 URL 為基礎的防火牆 proxy 來控制輸出連線能力，請將[這些 url](migrate-appliance.md#url-access)新增至允許清單。
- 如果您使用攔截 proxy 來連線到網際網路，請使用[這些步驟](./migrate-appliance.md)將 proxy 憑證匯入至應用裝置 VM。

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>無法從應用裝置 web 應用程式登入 Azure

如果您使用不正確的 Azure 帳戶登入 Azure，就會出現「抱歉，將您登入時發生問題」錯誤。 發生此錯誤的原因有幾個：

- 如果您使用政府雲端入口網站的使用者帳號憑證，登入公用雲端的設備 web 應用程式。
- 如果您使用私人雲端入口網站的使用者帳號憑證，登入政府雲端的設備 web 應用程式。

請確定您使用的是正確的認證。

##  <a name="datetime-synchronization-error"></a>日期/時間同步處理錯誤

有關日期和時間同步處理（802）的錯誤指出伺服器時鐘可能未與目前時間同步超過五分鐘。 變更收集器 VM 上的時鐘時間以符合目前的時間：

1. 在虛擬機器上開啟系統管理命令提示字元。
2. 若要檢查時區，請執行**w32tm/tz**。
3. 若要同步處理時間，請執行**w32tm/resync**。


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

如果您收到此連接錯誤，您可能無法連接到 vCenter Server *Servername*.com：9443。 錯誤詳細資料指出沒有接聽 `https://\*servername*.com:9443/sdk` 的端點可以接受該訊息。

- 檢查您是否正在執行最新版本的設備。 如果不是，請將設備升級至[最新版本](./migrate-appliance.md)。
- 如果問題仍然發生在最新版本中，設備可能無法解析指定的 vCenter Server 名稱，或指定的埠可能錯誤。 根據預設，如果未指定埠，收集器會嘗試連接到埠號碼443。

    1. 從設備 Ping *Servername*.com。
    2. 如果步驟1失敗，請嘗試使用 IP 位址連接到 vCenter server。
    3. 識別要連接到 vCenter Server 的正確埠號碼。
    4. 確認 vCenter Server 已啟動且正在執行。


## <a name="error-6005260039-appliance-might-not-be-registered"></a>錯誤60052/60039：設備可能未註冊

- 如果用來註冊應用裝置的 Azure 帳戶許可權不足，就會發生錯誤60052：「設備可能未成功註冊至 Azure Migrate 專案」。
    - 請確定用來註冊應用裝置的 Azure 使用者帳戶至少具有訂用帳戶的參與者許可權。
    - [深入瞭解](./migrate-appliance.md#appliance---vmware)必要的 Azure 角色和許可權。
- 如果註冊失敗，因為找不到用來註冊應用裝置的 Azure Migrate 專案，錯誤60039「可能無法成功地將設備註冊到 Azure Migrate 專案」。
    - 在 [Azure 入口網站中，檢查項目是否存在於資源群組中。
    - 如果專案不存在，請在您的資源群組中建立新的 Azure Migrate 專案，然後重新註冊設備。 [瞭解如何](./how-to-add-tool-first-time.md#create-a-project-and-add-a-tool)建立新的專案。

## <a name="error-6003060031-key-vault-management-operation-failed"></a>錯誤60030/60031： Key Vault 管理操作失敗

如果您收到錯誤60030或60031「Azure Key Vault 管理作業失敗」，請執行下列動作：
- 請確定用來註冊應用裝置的 Azure 使用者帳戶至少擁有訂用帳戶的參與者許可權。
- 請確定帳戶可存取錯誤訊息中指定的金鑰保存庫，然後再次嘗試操作。
- 如果問題持續發生， 請連絡 Microsoft 支援服務。
- [深入瞭解](./migrate-appliance.md#appliance---vmware)必要的 Azure 角色和許可權。

## <a name="error-60028-discovery-couldnt-be-initiated"></a>錯誤60028：無法起始探索

錯誤60028：「無法起始探索，因為發生錯誤。 指定的主機或叢集清單的操作失敗，表示因為存取或抓取 VM 資訊時發生問題，所以無法在錯誤中所列的主機上啟動探索。 已成功新增其餘的主機。

- 使用 [**新增主機**] 選項，再次新增錯誤中所列的主機。
- 如果發生驗證錯誤，請檢查補救指引以修正錯誤，然後再次嘗試 [**儲存並啟動探索**] 選項。

## <a name="error-60025-azure-ad-operation-failed"></a>錯誤60025： Azure AD 操作失敗 
錯誤60025：「Azure AD 操作失敗。 當用來起始探索的 Azure 使用者帳戶不同于用來註冊應用裝置的帳戶時，建立或更新 Azure AD 應用程式時發生錯誤。 請執行下列其中一項：

- 確定起始探索的使用者帳戶與用來註冊應用裝置的帳戶相同。
- 針對探索作業失敗的使用者帳戶，提供 Azure Active Directory 的應用程式存取權限。
- 刪除先前為 Azure Migrate 專案所建立的資源群組。 建立另一個資源群組以重新開機。
- [深入瞭解](./migrate-appliance.md#appliance---vmware)Azure Active Directory 應用程式許可權。


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>錯誤50004：無法連接到主機或叢集

錯誤50004：「無法連接到主機或叢集，因為無法解析伺服器名稱。 如果設備的 Azure DNS 服務無法解析您提供的叢集或主機名稱，則會發生 WinRM 錯誤碼： 0x803381B9 "。

- 如果您在叢集上看到此錯誤，請叢集 FQDN。
- 對於叢集中的主機，您可能也會看到此錯誤。 這表示設備可以連接到叢集，但叢集會傳回不是 Fqdn 的主機名稱。 若要解決此錯誤，請新增 IP 位址和主機名稱的對應，以更新設備上的 hosts 檔案：
    1. 以系統管理員身分開啟 [記事本]。
    2. 開啟 C:\Windows\System32\Drivers\etc\hosts 檔案。
    3. 在資料列中新增 IP 位址和主機名稱。 針對您看到此錯誤的每個主機或叢集重複此動作。
    4. 儲存並關閉主機檔案。
    5. 使用設備管理應用程式，檢查設備是否可連線至主機。 30分鐘後，您應該會在 Azure 入口網站中看到這些主機的最新資訊。

## <a name="discovered-vms-not-in-portal"></a>探索到的 Vm 不在入口網站中

如果探索狀態為「正在探索」，但在入口網站中尚未看到 Vm，請等候幾分鐘：
- VMware VM 需要大約15分鐘的時間。
- 針對每個新增的 Hyper-v VM 探索主機，大約需要兩分鐘的時間。

如果您等待且狀態不會變更，請**選取 [** **伺服器**] 索引標籤上的 [重新整理]。這應該會顯示探索到的伺服器計數 Azure Migrate：伺服器評估和 Azure Migrate：伺服器遷移。

如果沒有作用，而且您正在探索 VMware 伺服器：

- 請確認您指定的 vCenter 帳戶已正確設定許可權，且至少有一個 VM 的存取權。
- 如果 vCenter 帳戶具有在 vCenter VM 資料夾層級授與的存取權，Azure Migrate 就無法探索 VMware Vm。 [深入瞭解](set-discovery-scope.md)範圍探索。

## <a name="vm-data-not-in-portal"></a>VM 資料不在入口網站中

如果探索到的 Vm 不會出現在入口網站中，或 VM 資料已過期，請等候幾分鐘的時間。 最多需要30分鐘的時間，探索到的 VM 設定資料中的變更才會出現在入口網站中。 可能需要幾個小時的時間，應用程式資料的變更才會出現。 如果在這段時間之後沒有任何資料，請嘗試重新整理，如下所示

1. 在 [**伺服器**  >  **Azure Migrate Server 評估**] 中，選取 **[總覽**]。
2. 在 [**管理**] 下，選取 [**代理程式健全狀況**]。
3. 選取 [重新整理**代理程式**]。
4. 等待重新整理作業完成。 您現在應該會看到最新的資訊。

## <a name="deleted-vms-appear-in-portal"></a>已刪除的 Vm 會出現在入口網站中

如果您刪除 Vm，而且它們仍會出現在入口網站中，請等候30分鐘。 如果仍然出現，請重新整理，如上所述。

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>我在實體伺服器上看不到某些網路介面卡的效能資料

如果實體伺服器已啟用 Hyper-v 虛擬化，就會發生這種情況。 由於產品的差距，網路輸送量會在探索到的虛擬網路介面卡上捕捉。

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>錯誤：上傳的檔案不是預期的格式
某些工具的地區設定建立的 CSV檔案是以分號做為分隔符號。 請變更設定，確保分隔符號為逗號。

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>我已匯入 CSV，但看到「探索進行中」
如果因為驗證失敗而導致 CSV 上傳失敗，則會顯示此狀態。 請再次嘗試匯入 CSV。 您可以下載上一次上傳的錯誤報告，按照檔案中的補救指示來修正錯誤。 可在 [探索機器] 頁面上的 [匯入詳細資料] 區段下載錯誤報告。

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>即使在更新來賓認證之後，還是看不到應用程式詳細資料
應用程式探索會每24小時執行一次。 如果您想要立即查看詳細資料，請如下所示重新整理。 這可能需要幾分鐘的時間，視 [否] 而定。 探索到的 Vm。

1. 在 [**伺服器**  >  **Azure Migrate Server 評估**] 中，選取 **[總覽**]。
2. 在 [**管理**] 下，選取 [**代理程式健全狀況**]。
3. 選取 [重新整理**代理程式**]。
4. 等待重新整理作業完成。 您現在應該會看到最新的資訊。

## <a name="unable-to-export-application-inventory"></a>無法匯出應用程式清查
請確定從入口網站下載清查的使用者具有訂用帳戶的參與者許可權。

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>找不到可完成驗證的適當驗證方法（publickey）
以金鑰為基礎的驗證將無法使用密碼驗證。

## <a name="common-app-discovery-errors"></a>常見的應用程式探索錯誤

Azure Migrate 支援使用 Azure Migrate：伺服器評估來探索應用程式、角色和功能。 目前僅支援 VMware 的應用程式探索。 [深入瞭解](how-to-discover-applications.md)設定應用程式探索的需求和步驟。

一般應用程式探索錯誤摘要于表格中。 

**錯誤** | **原因** | **動作**
--- | --- | ---
9000：無法偵測到 VMware 工具狀態。     |   VMWare 工具可能未安裝或已損毀。    |   請確定已在 VM 上安裝並執行 VMware 工具。
9001：未安裝 VMware 工具。     |   VMWare 工具可能未安裝或已損毀。    |   請確定已在 VM 上安裝並執行 VMware 工具。
9002： VMware 工具未執行。   |   VMWare 工具可能未安裝或已損毀。    |   請確定已在 VM 上安裝並執行 VMware 工具。
9003：不支援來賓 VM 探索的作業系統類型。    |   在伺服器上執行的作業系統不是 Windows 也不是 Linux。    |   支援的作業系統類型僅限 Windows 和 Linux。 如果伺服器確實是 Windows 或 Linux，請檢查 vCenter Server 中指定的作業系統類型。
9004： VM 不在執行中。     |   VM 已關閉電源。  |   請確定 VM 已開啟電源。
9005：不支援來賓 VM 探索的作業系統類型。    |   不支援來賓 VM 探索的作業系統類型。     |   支援的作業系統類型僅限 Windows 和 Linux。
9006：從來賓下載中繼資料檔案的 URL 是空的。     |   如果探索代理程式未如預期般運作，就可能發生這種情況。    |   此問題應該會自動解決 in24 小時。 如果問題持續發生，請連絡 Microsoft 支援服務。
9007：找不到在來賓 VM 中執行探索工作的進程。   |   如果探索代理程式未正常運作，就可能發生這種情況。   |   此問題應該會在24小時內自動解決。 如果問題持續發生，請連絡 Microsoft 支援服務。
9008：無法抓取來賓 VM 進程狀態。   |   問題可能是內部錯誤所造成。   |   此問題應該會在24小時內自動解決。 如果問題持續發生，請連絡 Microsoft 支援服務。
9009： Windows UAC 已防止伺服器上執行探索工作。  |   伺服器上的 Windows 使用者帳戶控制（UAC）設定受到限制，並防止探索已安裝的應用程式。  |   在伺服器上的 [使用者帳戶控制] 設定中，將 UAC 設定設為較低兩個層級的其中一個。
9010： VM 已關閉電源。     |   VM 已關閉電源。  |   請確定 VM 已開啟電源。
9011：在來賓 VM 檔案系統中找不到發現的中繼資料檔案。    |   問題可能是內部錯誤所造成。   |   此問題應該會在24小時內自動解決。 如果問題持續發生，請連絡 Microsoft 支援服務。
9012：探索到的中繼資料檔案是空的。     |   問題可能是內部錯誤所造成。   |   此問題應該會在24小時內自動解決。 如果問題持續發生，請連絡 Microsoft 支援服務。
9013：已為每個登入建立新的暫存設定檔。    |   針對 VMware VM 的每個登入都會建立新的暫存設定檔。    |   請聯絡 Microsoft 支援服務以取得解決方法。
9014：無法從來賓 VM 檔案系統取得中繼資料。     |   沒有 ESXi 主機的連線能力    |   請確定設備可連線至執行 VM 的 ESXi 主機上的埠443
9015： vCenter 使用者帳戶未啟用來賓作業角色   |   VCenter 使用者帳戶未啟用來賓作業角色。   |   請確定已在 vCenter 使用者帳戶上啟用來賓作業角色。
9016：因為來賓作業代理程式已過期，所以無法探索。   |   Vmware 工具未正確安裝或不是最新版本。    |   請確認 VMware 工具已正確安裝且處於最新狀態。
9017：在 VM 上找不到具有探索到之中繼資料的檔案。  |   問題可能是內部錯誤所造成。   |   請聯絡 Microsoft 支援服務以取得解決方法。
9018：來賓 Vm 中未安裝 PowerShell。  |   在來賓 VM 中無法使用 PowerShell。    |   在來賓 VM 中安裝 PowerShell。
9019：因為來賓 VM 操作失敗而無法探索。     |   VM 上的 VMware 來賓操作失敗。    |   請確認 VM 認證有效，而且來賓 VM 認證中提供的使用者名稱是 UPN 格式。
9020：拒絕檔案建立許可權。    |   與使用者或群組原則相關聯的角色正在限制使用者在資料夾中建立檔案    |   檢查來賓使用者所提供的是否具有資料夾中檔案的 create 許可權。 請參閱伺服器評估中的**通知**，以取得資料夾的名稱。
9021：無法在系統暫存路徑中建立檔案。     |   VMware 工具會報告系統暫存路徑，而不是使用者的暫存路徑。    |   將您的 vmware 工具版本升級至10287以上（NGC/VI 用戶端格式）。
9022：拒絕存取 WMI 物件。    |   與使用者或群組原則相關聯的角色會限制使用者存取 WMI 物件。  |   請連絡 Microsoft 支援。
9023：無法執行 PowerShell，因為 SystemRoot 環境變數值是空的。    |   針對來賓 VM，SystemRoot 環境變數的值是空的。     |   請聯絡 Microsoft 支援服務以取得解決方法。
9024：無法探索，因為 TEMP 環境變數值是空的。    |   針對來賓 VM，TEMP 環境變數的值是空的。   |   請連絡 Microsoft 支援。
9025：來賓 Vm 中的 PowerShell 已損毀。  |   來賓 VM 中的 PowerShell 已損毀。    |   在來賓 VM 中重新安裝 PowerShell，並確認可以在來賓 VM 上執行 PowerShell。
9026：無法在 VM 上執行來賓作業。  |   VM 狀態不允許在 VM 上執行來賓作業。   |   請聯絡 Microsoft 支援服務以取得解決方法。
9027：來賓操作代理程式未在 VM 中執行。   |   無法連絡在虛擬機器內執行的來賓作業代理程式。    |   請聯絡 Microsoft 支援服務以取得解決方法。
9028：因為 VM 中的磁片儲存空間不足，所以無法建立檔案。     |   磁片上的空間不足。   |   請確定 VM 的磁片儲存體中有足夠的空間可用。
9029：無法在提供的來賓 VM 認證上存取 powershell。   |   存取 Powershell 無法供使用者使用。     |   請確定在設備上新增的使用者可以存取來賓 VM 上的 PowerShell。
9030：無法收集已探索的中繼資料，因為 ESXi 主機已中斷連線。     |   ESXi 主機處於中斷連線的狀態。   |   請確認執行 VM 的 ESXi 主機已連線。
9031：無法收集探索到的中繼資料，因為 ESXi 主機沒有回應。   |   遠端主機處於無效狀態。    |   請確定執行 VM 的 ESXi 主機正在執行且已連線。
9032：因為發生內部錯誤，所以無法探索。   |   問題可能是內部錯誤所造成。   |   請聯絡 Microsoft 支援服務以取得解決方法。
9033：無法探索，因為 VM 使用者名稱包含不正確字元。     |   在使用者名稱中偵測到不正確字元。   |   請再次提供 VM 認證，以確保沒有不正確字元。
9034：提供的使用者名稱不是 UPN 格式。    |   使用者名稱不是 UPN 格式。  |   請確認使用者名稱的格式為 [使用者主體名稱（UPN）]。
9035：因為 Powershell 語言模式未設定為「完整語言」，所以無法探索。  |   來賓 VM 中的 Powershell 語言模式未設定為完整語言。   |   確定 PowerShell 語言模式設定為 [完整語言]。
10000：不支援作業系統類型。   |   在伺服器上執行的作業系統不是 Windows 也不是 Linux。    |   支援的作業系統類型僅限 Windows 和 Linux。
10001：在設備上找不到伺服器探索的腳本。    |   探索未如預期般運作。   |   請聯絡 Microsoft 支援服務以取得解決方法。
10002：探索工作尚未及時完成。     |   探索代理程式未如預期般運作。     |   此問題應該會在24小時內自動解決。 如果問題持續發生，請連絡 Microsoft 支援服務。
10003：執行探索工作的進程已結束，但發生錯誤。    |   執行探索工作的進程已結束，但發生錯誤。  |   此問題應該會在24小時內自動解決。 如果問題仍然持續發生，請洽詢 Microsoft 支援服務。
10004：未提供客體作業系統類型的認證。  |   Azure Migrate 設備中未提供存取此 OS 類型之電腦的認證。    |   在設備上新增機器的認證
10005：提供的認證無效。   |   為設備提供用來存取伺服器的認證不正確。  |   更新設備中提供的認證，並確定可以使用認證來存取伺服器。
10006：認證存放區不支援來賓 OS 類型。  |   在伺服器上執行的作業系統不是 Windows 也不是 Linux。    |   支援的作業系統類型僅限 Windows 和 Linux。
10007：無法處理探索到的中繼資料。    |   嘗試還原序列化 JSON 時發生錯誤。    |   請聯絡 Microsoft 支援服務以取得解決方法。
10008：無法在伺服器上建立檔案。    |  問題可能是內部錯誤所造成。    |   請聯絡 Microsoft 支援服務以取得解決方法。
10009：無法將探索到的中繼資料寫入伺服器上的檔案。  |   問題可能是內部錯誤所造成。   |   請聯絡 Microsoft 支援服務以取得解決方法。




## <a name="next-steps"></a>後續步驟
為[VMware](how-to-set-up-appliance-vmware.md)、 [hyper-v](how-to-set-up-appliance-hyper-v.md)或[實體伺服器](how-to-set-up-appliance-physical.md)設定設備。
