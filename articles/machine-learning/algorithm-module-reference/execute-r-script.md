---
title: 'R-szkript végrehajtása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használható az r-parancsfájl végrehajtása a Azure Machine Learning szolgáltatásban az R-kód futtatásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7de3dd016700d10ef5fac97fada5cd23c2f4e1cb
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558314"
---
# <a name="execute-r-script"></a>R-szkript végrehajtása

Ez a cikk azt ismerteti, hogyan használható az **r-parancsfájl végrehajtása** a Visual Interface-kísérletben az r-kód futtatásához.

Az R használatával olyan feladatokat hajthat végre, amelyeket jelenleg nem támogat a meglévő modulok, például a következők: 
- Egyéni adatátalakítások létrehozása
- Saját mérőszámok használata az előrejelzések kiértékeléséhez
- Modellek létrehozása a Visual Interface önálló moduljaiként nem implementált algoritmusok használatával

## <a name="r-version-support"></a>R-verziók támogatása

A Azure Machine Learning szolgáltatás vizuális felülete az R-k CRAN (átfogó R Archive Network) eloszlását használja. A jelenleg használt verzió a CRAN 3.5.1-es verziója.

## <a name="supported-r-packages"></a>Támogatott R-csomagok

Az R-környezet előre telepítve van több mint 100 csomaggal. A teljes listát az [előre telepített R-csomagok](#pre-installed-r-packages)című szakaszban találja.

A következő kódot is hozzáadhatja bármelyik **Execute R script** modulhoz, és megtekintheti a telepített csomagokat.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>R-csomagok telepítése
További R-csomagok telepítéséhez használja a `install.packages()` metódust. Ügyeljen arra, hogy megadja a CRAN-tárházat. A csomagok minden **végrehajtási r script** modulhoz telepítve vannak, és nem oszthatók meg más **végrehajtási r-parancsfájl** -modulok között.

Ez a példa bemutatja az állatkert telepítését:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
    > Győződjön meg arról, hogy a csomag már létezik a telepítés előtt, hogy elkerülje az ismételt telepítést. Például `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` a fenti mintakód esetében. Előfordulhat, hogy a telepítés megismétlése webszolgáltatási kérés időtúllépését okozhatja.     

## <a name="how-to-configure-execute-r-script"></a>Az R-szkript végrehajtásának konfigurálása

Az **R-szkript végrehajtása** modul olyan mintakód-kódot tartalmaz, amelyet kiindulási pontként használhat. Az R- **parancsfájl végrehajtása** modul konfigurálásához adja meg a végrehajtandó bemeneteket és kódokat.

![R-modul](media/module/execute-r-script.png)

A Visual Interface-ben tárolt adatkészletek automatikusan egy R-adatkeretre lesznek konvertálva, ha ezzel a modullal vannak betöltve.

1.  Adja hozzá az **R-szkript végrehajtása** modult a kísérlethez.

  

1. Csatlakoztasson a parancsfájlhoz szükséges összes bemenetet. A bemenetek nem kötelezőek, és tartalmazhatnak adatokat és további R-kódokat is.

    * **DataSet1 elemet**: Az első bemenetre `dataframe1`hivatkozzon. A bemeneti adatkészletet CSV-ként, TSV-ként, ARFF-ként, vagy egy Azure Machine Learning adatkészlet összekapcsolásával kell formázni.

    * **Dataset2**: A második bemenetre `dataframe2`hivatkozzon. Ezt az adatkészletet CSV-ként, TSV-ként, ARFF-fájlként vagy Azure Machine Learning-adatkészletként kell formázni.

    * **Parancsfájl-csomag**: A harmadik bemenet a ZIP-fájlokat is elfogadja. A tömörített fájl több fájlt és több fájltípust is tartalmazhat.

1. Az **r-szkript** szövegmezőbe írja be vagy illessze be az érvényes R-szkriptet.

    Az első lépésekhez az R- **szkript** szövegmező előre ki van töltve a mintakód segítségével, amelyet szerkeszthet vagy lecserélheti.
    
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.

  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

 * A szkriptnek tartalmaznia kell egy nevű `azureml_main`függvényt, amely a modul belépési pontja.

 * A belépési pont függvény legfeljebb két bemeneti argumentumot tartalmazhat: `Param<dataframe1>` és`Param<dataframe2>`
 
   > [!NOTE]
    > Az **R-szkript végrehajtása** modulnak átadott adatok a következőre `dataframe2`hivatkoznak `dataframe1` : és, amely `dataset1`eltér a Azure Machine learning Studiotól ( `dataset2`Studio-hivatkozás:). Győződjön meg arról, hogy a bemeneti adatok megfelelően vannak referneced a szkriptben.  
 
    > [!NOTE]
    >  Előfordulhat, hogy a meglévő R-kódnak kisebb módosításokra van szüksége a Visual Interface-kísérletben való futtatáshoz. Például a CSV formátumban megadott bemeneti adatokat explicit módon át kell alakítani egy adatkészletbe, mielőtt használni lehetne a kódban. Az R nyelvben használt adatok és oszlopok különböző módokon különböznek a vizualizációs felületen használt adatok és oszlopok típusaitól.

1.  **Véletlenszerű mag**: Adjon meg egy értéket, amelyet az R-környezetben kell használni véletlenszerű mag értékként. Ez a paraméter egyenértékű az R `set.seed(value)` -kódban való meghívással.  

1. Futtassa a kísérletet.  

## <a name="results"></a>Results (Eredmények)

Az **r-szkriptek végrehajtása** modulok több kimenetet is visszaadhatnak, de r-adatkeretként kell megadni őket. Az adatkereteket a rendszer automatikusan átalakítja a Visual Interface-adatkészletekbe más modulok kompatibilitása érdekében.

Az R standard üzeneteit és hibáit a rendszer visszaadja a modul naplójába.

## <a name="sample-scripts"></a>Mintaszkriptek

A kísérletet többféleképpen is kiterjesztheti egyéni R-szkriptek használatával.  Ez a szakasz a gyakori feladatokhoz tartalmaz mintakód-kódot.


### <a name="add-r-script-as-an-input"></a>R-szkript hozzáadása bemenetként

Az **r-szkript végrehajtása** modul bemenetként támogatja a tetszőleges r-parancsfájlokat. Ehhez a ZIP-fájl részeként fel kell tölteni őket a munkaterületre.

1. Ha R-kódot tartalmazó ZIP-fájlt szeretne feltölteni a munkaterületre, kattintson az **új**, majd az **adatkészlet**elemre, majd válassza a **helyi fájlból** és a **zip** -fájlból lehetőséget.  

1. Ellenőrizze, hogy a tömörített fájl elérhető-e a **mentett** adatkészletek listájában.

1.  Kapcsolja össze az adatkészletet a **parancsfájl-csomag** bemeneti portjával.

1. A ZIP-fájlban található összes fájl elérhető a kísérlet futási ideje alatt. 

    Ha a parancsfájl-köteg fájlja tartalmaz egy címtár-struktúrát, a rendszer megőrzi a struktúrát. Azonban módosítania kell a kódot, hogy a könyvtár **./script csomagot** adja meg az elérési útra.

### <a name="process-data"></a>Adatok feldolgozása

Az alábbi példa bemutatja, hogyan méretezhető és normalizálható a bemeneti adatok:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>ZIP-fájl beolvasása bemenetként

Ez a minta bemutatja, hogyan használható a ZIP-fájlban található adatkészlet bemenetként az **R-szkript végrehajtása** modulba.

1. Hozzon létre egy CSV formátumú adatfájlt, és nevezze el "mydatafile. csv" néven.
1. Hozzon létre egy ZIP-fájlt, és adja hozzá a CSV-fájlt az archívumhoz.
1. Töltse fel a tömörített fájlt a Azure Machine Learning munkaterületre. 
1. Az eredményül kapott adatkészlet csatlakoztatása az R- **szkript végrehajtásához** tartozó modul **ScriptBundle** -bemenetéhez.
1. A következő kód használatával olvassa be a CSV-adatait a tömörített fájlból.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Sorok replikálása

Ez a minta bemutatja, hogyan replikálhat pozitív rekordokat egy adatkészletben a minta kiegyensúlyozásához:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>R-objektumok átadása az R-parancsfájl-modulok végrehajtása között

Az r-objektumokat a belső szerializálási mechanizmus használatával lehet átadni az **r szkript végrehajtása** modul példányai között. Ez a példa feltételezi, hogy át kívánja helyezni az r `A` -objektumot két **végrehajtási r parancsfájl** -modul között.

1. Adja hozzá az első **végrehajtási R** -parancsfájlt a kísérlethez, és írja be a következő kódot az **r-szkript** szövegmezőbe egy szerializált `A` objektum létrehozásához a modul kimeneti adattáblájában:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Az egész típusra történő explicit konverzió történik, mivel a szerializálási függvény az R `Raw` formátumban jeleníti meg az adatokat, amelyet a vizualizációs felület nem támogat.

1. Vegyen fel egy második példányt az **R szkript végrehajtása** modulhoz, és kapcsolja az előző modul kimeneti portjához.

1. Írja be a következő kódot az **R-szkript** szövegmezőbe az objektum `A` bemeneti adatok táblából való kinyeréséhez. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Előre telepített R-csomagok

A használható előre telepített R-csomagok aktuális listája:

|              |            | 
|--------------|------------| 
| Csomag      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| alap         | 3.5.1      | 
| base64enc    | 0,1 – 3      | 
| BH           | 1.69.0 – 1   | 
| kötés        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0 – 6      | 
| rendszerindítás         | 1.3 – 22     | 
| seprű        | 0.5.2      | 
| hívó        | 3.2.0      | 
| kalap jel        | 6.0 – 84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| Osztály        | 7.3 – 15     | 
| CLI          | 1.1.0      | 
| Cliper        | 0.6.0      | 
| fürt      | 2.0.7 – 1    | 
| codetools    | 0.2 – 16     | 
| színtér   | 1.4 – 1      | 
| fordítóprogram     | 3.5.1      | 
| zsírkréta       | 1.3.4      | 
| Curl         | 3.3        | 
| az adattábla   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7 – 2      | 
| értékelés     | 0,14       | 
| fansi        | 0.4.0      | 
| Forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| külföldi      | 0.8-71     | 
| FS           | 1.3.1      | 
| GData        | 2.18.0     | 
| generikus     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0 – 18     | 
| ragasztó         | 1.3.1      | 
| Gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| grafikus     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| tevékenységnaplóiban        | 2.1.0      | 
| magas szintű        | 0,8        | 
| HMS          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| IPRED        | 0,9 – 9      | 
| Iterátornak    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 23e – 15    | 
| kötés        | 1,23       | 
| címkézés     | 0,3        | 
| rácsos      | 0,20 – 38    | 
| láva         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| Markdown     | 1          | 
| MASS         | 7.3 – 51.4   | 
| Mátrix       | 1.2 – 17     | 
| módszerek      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| modellező       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1 – 140    | 
| a beoltás         | 7.3 – 12     | 
| numDeriv     | 2016.8-1.1 | 
| OpenSSL      | 1.4        | 
| párhuzamos     | 3.5.1      | 
| Pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| haladás     | 1.2.2      | 
| PS           | 1.3.0      | 
| a dorombolás        | 0.3.2      | 
| quadprog     | 1.5 – 7      | 
| quantmod     | 0,4 – 15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1 – 2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| olvasó        | 1.3.1      | 
| readxl       | 1.3.1      | 
| receptek      | 0.1.5      | 
| visszavágót      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1,12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1,13       | 
| ROCR         | 1.0 – 7      | 
| rpart        | 4.1 – 15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| mérlegek       | 1.0.0      | 
| választó      | 0,4 – 1      | 
| térbeli      | 7.3 – 11     | 
| spline      | 3.5.1      | 
| SQUAREM      | 2017.10 – 1  | 
| statisztikák        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| karakterlánc      | 1.3.1      | 
| túlélési     | 2.44 – 1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| megtakarító        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0,13       | 
| eszközök        | 3.5.1      | 
| tseries      | 0,10 – 47    | 
| TTR          | 0.23 – 4     | 
| UTF8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| kanóc      | 0,3 – 2      | 
| a        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| XTS          | 0,11 – 2     | 
| YAML         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| állatkert          | 1.8-6      | 

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
