---
title: Az Azure Cosmos DB SQL állandók
description: További tudnivalók az Azure Cosmos DB SQL állandók
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: ed337c8330eb8b3e4ad97c92cca1d0c5977f9588
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342856"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Az Azure Cosmos DB SQL-lekérdezés állandók  

 Egy állandó, más néven szövegkonstans vagy skaláris, egy meghatározott értéket jelölő szimbólumot. Egy állandó formátumát az érték azt jelenti, hogy függ.  
  
 **Skaláris adattípusokat támogatja:**  
  
|**Típus**|**Értékek sorrendje**|  
|-|-|  
|**Nincs definiálva**|Egyetlen érték: **nem definiált**|  
|**NULL**|Egyetlen érték: **null értékű**|  
|**Logikai érték**|Értékek: **hamis**, **igaz**.|  
|**Szám**|Egy dupla pontosságú lebegőpontos számnál, szabvány IEEE 754.|  
|**Karakterlánc**|Nulla vagy több Unicode karakter sorozata. Karakterláncok egyszeres vagy kettős idézőjelek közé kell tenni.|  
|**Pole**|Nulla vagy több elemek sorrendjét. Minden elem kivételével minden skaláris adattípusú érték lehet **Undefined**.|  
|**Object**|Egy nulla vagy több név/érték párok rendezetlen készlete. Név egy Unicode karakterlánc, kivéve értéke lehet bármely skaláris adattípusú **Undefined**.|  
  
## <a name="bk_syntax"></a>Szintaxis
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="bk_arguments"></a> argumentumok
  
* `<undefined_constant>; Undefined`  
  
  Nincs megadva típusú érték nem definiált jelöli.  
  
* `<null_constant>; null`  
  
  Jelöli **null** típusú érték **Null**.  
  
* `<boolean_constant>`  
  
  Logikai érték típusú konstans jelöli.  
  
* `false`  
  
  Jelöli **hamis** logikai típusú érték.  
  
* `true`  
  
  Jelöli **igaz** logikai típusú érték.  
  
* `<number_constant>`  
  
  Egy állandó jelöli.  
  
* `decimal_literal`  
  
  Tizedes tört literálok lehetnek olyan szám vagy decimális jelölés, vagy tudományos jelölés használatával.  
  
* `hexadecimal_literal`  
  
  Hexadecimális literálok előtagja '0 x' legalább egy hexadecimális számjegy követ használatával értékek, amelyek.  
  
* `<string_constant>`  
  
  Egy karakterlánc típusú konstans jelöli.  
  
* `string _literal`  
  
  Karakterlánc-literálnak sorozata, nulla vagy több Unicode-karaktereket vagy escape-karaktersorozatokat által képviselt Unicode karakterláncokat is. Karakterlánc-literálnak aposztrófok közé kell tenni (aposztróf: ") vagy dupla idézőjel (idézőjel:").  
  
  Következő escape-karaktersorozatokat engedélyezettek:  
  
|**Escape-szekvencia**|**Leírás**|**Unicode-karakter**|  
|-|-|-|  
|\\'|aposztróf (')|U+0027|  
|\\"|idézőjel (")|U+0022|  
|\\\ |fordított solidus (\\)|U+005C|  
|\\/|solidus (/)|U+002F|  
|\b|vissza|U+0008|  
|\f|Lapdobás|U+000C|  
|\n|Sortörés|U+000A|  
|\r|kocsivissza|U+000D|  
|\t|tabulátor|U+0009|  
|\uXXXX|Egy 4 hexadecimális számjegy által meghatározott Unicode-karakter.|U+XXXX|  

## <a name="next-steps"></a>További lépések

- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dokumentumadatok modellezése](modeling-data.md)
