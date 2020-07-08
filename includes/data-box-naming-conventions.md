---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "66244649"
---
| 單位                                       | 慣例   |
|----------------------------------------------|-----------------------------------------------------|
| 區塊 Blob 和分頁 Blob 的容器名稱 | 必須是長度介於 3 到 63 個字元的有效 DNS 名稱。 <br>  必須以字母或數字開頭。 <br> 只能包含小寫字母、數字和連字號 (-)。 <br> 每個連字號 (-) 的前後都必須緊鄰字母或數字。 <br> 名稱中不允許連續的連字號。 |
| Azure 檔案的共用名稱                  | 同上                                          |
| Azure 檔案的目錄和檔案名稱     |<li> 保留大小寫、不區分大小寫而且長度不得超過 255 個字元。 </li><li> 不能以正斜線 (/) 結尾。 </li><li>如果有的話，則會自動移除。 </li><li> 不允許使用下列字元：<code>" \\ / : \| < > * ?</code></li><li> 保留的 URL 字元必須正確逸出。 </li><li> 不允許有不合法的 URL 路徑字元。 UE000 之類的程式碼點 \\ 不是有效的 Unicode 字元。 也不允許某些 ASCII 或 Unicode 字元，例如控制字元（0x00 到0x1F、 \\ u0081 等）。 如需在 HTTP/1.1 中控管 Unicode 字串的規則，請參閱 RFC 2616 第 2.2 節：基本規則和 RFC 3987。 </li><li> 不允許下列檔案名： LPT1、LPT2、LPT3、LPT4、LPT5、LPT6、LPT7、LPT8、LPT9、COM1、COM2、COM3、COM4、COM5、COM6、COM7、COM8、COM9、PRN、AUX、NUL、CON、CLOCK $、點字元（.）和兩個點字元（.）。</li>|
| 區塊 Blob 和分頁 Blob 的 Blob 名稱      | </li><li>Blob 名稱會區分大小寫，而且可以包含字元的任意組合。 </li><li>Blob 名稱長度必須介於 1 到 1,024 個字元之間。 </li><li>保留的 URL 字元必須正確逸出。 </li><li>構成 Blob 名稱的路徑區段數目不可超過 254 個。 路徑線段是連續分隔符號字元 (例如，正斜線 '/') 之間的字串，會對應到虛擬目錄的名稱。</li> |
