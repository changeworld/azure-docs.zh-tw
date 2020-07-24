---
title: 核心 IO 作業 |Microsoft Azure 對應
description: 瞭解如何使用空間 IO 模組中的核心程式庫，有效率地讀取和寫入 XML 和分隔的資料。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7e142fe348a339e4903d692cad569015edb69f75
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124471"
---
# <a name="core-io-operations"></a>核心 IO 作業

除了提供讀取空間資料檔的工具之外，空間 IO 模組還會公開核心基礎程式庫，以快速且有效率地讀取和寫入 XML 和分隔的資料。

`atlas.io.core`命名空間包含兩個低層級的類別，可以快速地讀取和寫入 CSV 和 XML 資料。 這些基類會增強空間 IO 模組中的空間資料讀取器和寫入器。 您可以隨意使用它們來新增其他讀取和寫入 CSV 或 XML 檔案的支援。
 
## <a name="read-delimited-files"></a>讀取分隔的檔案

`atlas.io.core.CsvReader`類別會讀取包含分隔資料集的字串。 此類別提供兩種讀取資料的方法：

- 函式 `read` 會讀取完整的資料集，並傳回字串的二維陣列，代表分隔資料集的所有儲存格。
- 函 `getNextRow` 式會讀取分隔資料集中的每一行文字，並傳回字串陣列，代表該行資料集內的所有儲存格。 在處理下一個資料列之前，使用者可以處理資料列，並處置該資料列中任何不需要的記憶體。 因此，函式的記憶體效率較高。

根據預設，讀取器會使用逗號字元做為分隔符號。 不過，分隔符號可以變更為任何單一字元，或設為 `'auto'` 。 當設定為時 `'auto'` ，讀取器會分析字串中的第一行文字。 然後，它會從下表中選取最常見的字元來當做分隔符號使用。

| 分隔符號 | 字元 |
| :-- | :-- |
| Comma (逗號) | `,` |
| 索引標籤 | `\t` |
| Pipe | `|` |

此讀取器也支援文字限定詞，這些辨識符號是用來處理包含分隔符號的資料格。 引號（ `'"'` ）字元是預設的文字辨識符號，但可以變更為任何單一字元。

## <a name="write-delimited-files"></a>寫入分隔的檔案

會 `atlas.io.core.CsvWriter` 以分隔字串的形式寫入物件的陣列。 任何單一字元都可以用來做為分隔符號或文字辨識符號。 預設的分隔符號是逗號（ `','` ），而預設的文字辨識符號是引號（ `'"'` ）字元。

若要使用此類別，請遵循下列步驟：

- 建立類別的實例，並選擇性地設定自訂分隔符號或文字辨識符號。
- 使用函數或函式將資料寫入類別 `write` `writeRow` 。 針對函 `write` 式，傳遞代表多個資料列和資料格的二維物件陣列。 若要使用 `writeRow` 函數，請傳遞物件的陣列，代表具有多個資料行的資料列。
- 呼叫函式 `toString` 以取出分隔字串。 
- （選擇性）呼叫 `clear` 方法，讓寫入器可重複使用並減少其資源配置，或呼叫 `delete` 方法來處置寫入器實例。

> [!Note]
> 寫入的資料行數目會限制為傳遞給寫入器之資料的第一列中的儲存格數目。

## <a name="read-xml-files"></a>讀取 XML 檔案

在 `atlas.io.core.SimpleXmlReader` 剖析 XML 檔案時，類別的速度比更快 `DOMParser` 。 不過， `atlas.io.core.SimpleXmlReader` 類別需要 XML 檔案的格式正確。 格式不正確的 XML 檔案（例如遺漏結束記號）可能會導致錯誤。

下列程式碼示範如何使用類別，將 `SimpleXmlReader` XML 字串剖析為 JSON 物件，並將它序列化為所需的格式。

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>寫入 XML 檔案

`atlas.io.core.SimpleXmlWriter`類別會以記憶體有效率的方式寫入格式正確的 XML。

下列程式碼示範如何使用 `SimpleXmlWriter` 類別來產生格式正確的 XML 字串。

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

上述程式碼所產生的 XML 看起來會像下面這樣。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [支援的資料格式詳細資料](spatial-io-supported-data-format-details.md)