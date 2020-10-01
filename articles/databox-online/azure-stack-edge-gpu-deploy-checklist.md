---
title: 部署 Azure Stack Edge Pro GPU 裝置的預先部署檢查清單 |Microsoft Docs
description: 本文說明在您部署 Azure Stack Edge Pro GPU 裝置之前，可以搜集的資訊。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: 251b15f72c3216ad3ff4b2983c8cbbd2d9ec6579
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618245"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 裝置的部署檢查清單  

本文說明可在 Azure Stack Edge Pro 裝置的實際部署前收集的資訊。 

使用下列檢查清單，以確保您在將 Azure Stack Edge Pro 裝置的訂單，以及在收到裝置之前，擁有這項資訊。 

## <a name="deployment-checklist"></a>部署檢查清單 

| 階段                             | 參數                                                                                                                                                                                                                           | 詳細資料                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| 裝置管理               | <li>Azure 訂用帳戶</li><li>已註冊資源提供者</li><li>Azure 儲存體帳戶</li>|<li>啟用 Azure Stack Edge Pro/Data Box Gateway、擁有者或參與者存取權。</li><li>在 Azure 入口網站中，前往 **Home > 訂用帳戶 > 訂用帳戶 > 資源提供者**。 搜尋 `Microsoft.DataBoxEdge` 並註冊。 如果要 `Microsoft.Devices` 部署 IoT 工作負載，請重複執行。</li><li>需要存取認證</li> |
| 裝置安裝               | 套件中的電源線。 <br>對我們而言，SVE 18/3 纜線針對 125 V 和15安培分級，具有 NEMA 5 15P 至 C13 (輸入輸出) 連接器已寄出。 | 如需詳細資訊，請參閱 [支援的電源線清單（依國家/地區）](azure-stack-edge-technical-specifications-power-cords-regional.md)  |
|                                   | <li>埠1至少有 1 X 1 GbE 的 RJ-45 網路纜線  </li><li> 埠3、埠4、埠5或埠6至少有 1 X 25 GbE SFP + 銅纜線</li>| 客戶需要購買這些纜線。<br>如需裝置網路卡支援的網路纜線、交換器及收發器的完整清單，請參閱 [>cavium FastlinQ 41000 系列互通性對照表](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) 和 [Mellanox 雙埠 25g ConnectX-4 通道網路介面卡相容產品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。| 
| 第一次裝置連接      | <li>可以變更其 IPv4 設定的膝上型電腦。 此膝上型電腦會透過交換器或 USB 連接到乙太網路介面卡來連線至埠1。  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| 裝置登入                      | 裝置系統管理員密碼，介於8到16個字元之間，並包含下列三項：大寫、小寫、數位和特殊字元。                                            | 預設密碼是 *Password1* ，其會在第一次登入時過期。                                                     |
| 網路設定                  | 裝置隨附 2 x 1 GbE、4 x 25 GbE 網路埠。 <li>埠1僅用來設定管理設定。 您可以連接及設定一或多個資料埠。 </li><li> 從埠2到埠6的至少一個資料網路介面必須連線至網際網路 (，且可連線至 Azure) 。</li><li> 支援 DHCP 和靜態 IPv4 設定。 | 靜態 IPv4 設定需要 IP、DNS 伺服器和預設閘道。   |
| 計算網路設定     | <li>Kubernetes 節點需要2個免費、靜態、連續的 Ip，以及1個靜態 IP 來 IoT Edge 服務。</li><li>您要部署的每個額外服務或模組都需要一個額外的 IP。</li>| 僅支援靜態 IPv4 設定。|
|  (選擇性) Web proxy 設定     | <li>Web proxy 伺服器 IP/FQDN，埠 </li><li>Web proxy 使用者名稱、密碼</li> | 計算設定不支援 Web proxy。 |
| 防火牆和埠設定        | 如果使用防火牆，請確定所 [列的 url 模式和埠](azure-stack-edge-system-requirements.md#networking-port-requirements) 可供裝置 ip 使用。 |  |
|  (建議的) 時間設定       | 設定時區、主要 NTP 伺服器、次要 NTP 伺服器。 | 在區域網路上設定主要和次要 NTP 伺服器。<br>如果本機伺服器無法使用，可以設定公用 NTP 伺服器。                                                    |
|  (選擇性的) 補救伺服器設定 | <li>需要在區域網路上補救伺服器 IP 位址，也就是 WSUS 伺服器的路徑。 </li> | 預設會使用公用 windows update server。|
| 裝置設定 | <li>裝置的完整功能變數名稱 (FQDN)  </li><li>DNS 網域</li> | |
|  (選用) 憑證  | 若要測試非生產工作負載，請使用 [ [產生憑證] 選項](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates) <br><br> 如果您攜帶自己的憑證（包括簽署鏈 (s) ，請以適當的格式 [新增憑證](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) 。| 只有當您變更裝置名稱和（或） DNS 網域時，才需要設定憑證。 |
| 啟用  | 需要 Azure Stack Edge Pro/Data Box Gateway 資源的啟用金鑰。    | 一旦產生，金鑰會在3天內到期。 |

<!--
| (Optional) MAC Address            | If MAC address needs to be whitelisted, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>後續步驟

準備部署 [Azure Stack Edge Pro 裝置](azure-stack-edge-gpu-deploy-prep.md)。
