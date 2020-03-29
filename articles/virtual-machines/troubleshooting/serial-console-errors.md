---
title: Azure 串列主控台錯誤 |微軟文檔
description: Azure 串列主控台中的常見錯誤
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060697"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Azure 串列主控台中的常見錯誤
Azure 串列主控台中有一組已知錯誤。 這是這些錯誤的清單，並針對這些錯誤執行緩解步驟。

## <a name="common-errors"></a>常見錯誤

錯誤                             |   降低
:---------------------------------|:--------------------------------------------|
Azure 串列主控台需要啟用引導診斷。 按一下此處為虛擬機器配置啟動診斷。 | 確保 VM 或虛擬機器規模集已啟用[啟動診斷](boot-diagnostics.md)。 如果在虛擬機器縮放集實例上使用串列主控台，請確保實例具有最新的模型。
Azure 串列主控台需要虛擬機器運行。 使用上面的"開始"按鈕啟動虛擬機器。  | VM 或虛擬機器規模集實例必須處於啟動狀態才能訪問串列主控台（不得停止或處理 VM）。 確保 VM 或虛擬機器規模集實例正在運行，然後重試。
未為此訂閱啟用 Azure 串列主控台，請與訂閱管理員聯繫以啟用。 | 可以在訂閱級別禁用 Azure 串列主控台。 如果您是訂閱管理員，則可以[啟用和禁用 Azure 串列主控台](./serial-console-enable-disable.md)。 如果您不是訂閱管理員，則應聯繫訂閱管理員瞭解後續步驟。
存取此 VM 的開機診斷儲存體帳戶時，遇到「禁止」回應。 | 請確定開機診斷沒有帳戶防火牆。 必須要有可存取的開機診斷儲存體帳戶，序列主控台才能運作。 根據設計，序列主控台無法與開機診斷儲存體帳戶上啟用的儲存體帳戶防火牆搭配使用。
您沒有可使用此 VM 與序列主控台的必要權限。 請確定您至少具有「虛擬機器參與者」角色權限。| 串列主控台訪問要求您在 VM 或虛擬機器規模集中具有參與者級別或以上存取權限。 有關詳細資訊，請參閱[概述頁](serial-console-overview.md)。
找不到用於此 VM 上啟動診斷的存儲帳戶''。 驗證是否為此 VM 啟用了啟動診斷，此存儲帳戶尚未刪除，並且您有權訪問此存儲帳戶。 | 仔細檢查您尚未刪除 VM 或虛擬機器規模集的啟動診斷存儲帳戶
與 VM 的串列主控台連接遇到錯誤："錯誤請求" （400） | 如果您的引導診斷 URI 不正確，則可能發生這種情況。 例如，使用"HTTP://"而不是"HTTPs://"。 啟動診斷 URI 可以通過以下命令進行修復：`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
您沒有寫入此 VM 的啟動診斷存儲帳戶所需的許可權。 請確保您至少具有 VM 參與者許可權 | 串列主控台訪問需要在引導診斷存儲帳戶上訪問參與者級別。 有關詳細資訊，請參閱[概述頁](serial-console-overview.md)。
無法確定引導診斷存儲帳戶*&lt;存儲帳戶的"&gt;存儲帳戶名稱*"的資源組。 請確認已針對此 VM 啟用開機診斷，且您具有此儲存體帳戶的存取權。 | 串列主控台訪問需要在引導診斷存儲帳戶上訪問參與者級別。 有關詳細資訊，請參閱[概述頁](serial-console-overview.md)。
此 VM 的預配尚未成功。 請確保已完全部署 VM 並重試串列主控台連接。 | VM 或虛擬機器規模集可能仍在預配中。 請稍等片刻，然後重試。
Web 通訊端已關閉或無法開啟。 | 您可能需要向`*.console.azure.com`添加防火牆存取權限。 一個更詳細的但更長的方法是允許防火牆訪問[微軟 Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)，該範圍會定期更改。
串列主控台不適用於使用 Azure 資料湖存儲 Gen2 具有階層命名空間的存儲帳戶。 | 這是階層命名空間的已知問題。 要緩解，請確保不會使用 Azure 資料湖存儲 Gen2 創建 VM 的啟動診斷存儲帳戶。 此選項只能在創建存儲帳戶時設置。 您可能需要創建單獨的引導診斷存儲帳戶，而不啟用 Azure 資料湖存儲 Gen2 來緩解此問題。
與 VM 的串列主控台連接遇到錯誤："禁止"（訂閱未啟用） - 訂閱名稱未定義，id\<訂閱 id>處於未啟用狀態未定義 | 如果使用者已在其中創建其雲外殼存儲帳戶的訂閱已被禁用，則可能會出現此問題。 為了緩解，啟動雲外殼並[執行在當前](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1)訂閱中為雲外殼重新預配備份存儲帳戶所需的步驟。

## <a name="next-steps"></a>後續步驟
* 瞭解有關 Linux VM 的[Azure 串列主控台](./serial-console-linux.md)的資訊
* 瞭解有關 Windows VM 的[Azure 串列主控台](./serial-console-windows.md)的資訊