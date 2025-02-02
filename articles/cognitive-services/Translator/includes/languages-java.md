---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: d37efdaf7e8f2b2b2cb6d3c5fcc90e166feab96a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968733"
---
## <a name="prerequisites"></a>Előfeltételek

* [JDK 7-es vagy újabb verzió](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)

## <a name="initialize-a-project-with-gradle"></a>Projekt inicializálása a Gradle

Kezdjük egy munkakönyvtár létrehozásával ehhez a projekthez. Futtassa a következő parancsot a parancssorból (vagy a terminálból):

```console
mkdir get-languages-sample
cd get-languages-sample
```

Ezután egy Gradle-projektet fog inicializálni. Ez a parancs a Gradle nélkülözhetetlen Build-fájljait hozza létre, ami a `build.gradle.kts`legfontosabb: a, amelyet futásidőben használ az alkalmazás létrehozásához és konfigurálásához. Futtassa ezt a parancsot a munkakönyvtárból:

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL**kiválasztására, válassza a **Kotlin**lehetőséget.

## <a name="configure-the-build-file"></a>A Build fájl konfigurálása

Keresse `build.gradle.kts` meg és nyissa meg kedvenc ide-vagy szövegszerkesztővel. Ezután másolja a következő Build-konfigurációba:

```java
plugins {
    java
    application
}
application {
    mainClassName = "GetLanguages"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Vegye figyelembe, hogy ez a minta a HTTP-kérések OkHttp, valamint a JSON kezelésére és elemzésére szolgáló Gson-függőségekkel rendelkezik. Ha többet szeretne megtudni a Build konfigurációkról, tekintse meg az [új Gradle-buildek létrehozását](https://guides.gradle.org/creating-new-gradle-builds/)ismertető témakört.

## <a name="create-a-java-file"></a>Hozzon létre egy Java-fájlt

Hozzon létre egy mappát a minta alkalmazáshoz. A munkakönyvtárból futtassa a következőt:

```console
mkdir -p src/main/java
```

Ezután a mappában hozzon létre egy nevű `GetLanguages.java`fájlt.

## <a name="import-required-libraries"></a>Szükséges kódtárak importálása

Nyissa meg `GetLanguages.java` és adja hozzá a következő importálási utasításokat:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```

## <a name="define-variables"></a>Változók meghatározása

Először létre kell hoznia egy nyilvános osztályt a projekthez:

```java
public class GetLanguages {
  // All project code goes here...
}
```

Adja hozzá a következő sorokat `GetLanguages` a osztályhoz:

```java
String url = "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0";
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek `Ocp-Apim-Subscription-Region` is szerepelnie kell. [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Ügyfél létrehozása és kérelem készítése

Adja hozzá ezt a sort `GetLanguages` a osztályhoz a következő `OkHttpClient`létrehozásához:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Ezután hozzon létre egy `GET` kérést.

```java
// This function performs a GET request.
public String Get() throws IOException {
    Request request = new Request.Builder()
            .url(url).get()
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Függvény létrehozása a válasz elemzéséhez

Ez az egyszerű függvény elemzi és prettifies a Translator Text szolgáltatás JSON-válaszát.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés a kérelem elkészítése és a válasz beolvasása. Adja hozzá ezeket a sorokat a projekthez:

```java
public static void main(String[] args) {
    try {
        GetLanguages getLanguagesRequest = new GetLanguages();
        String response = getLanguagesRequest.Get();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ekkor készen áll a minta alkalmazás futtatására. A parancssorból (vagy a terminál-munkamenetből) navigáljon a munkakönyvtár gyökeréhez, és futtassa a következőt:

```console
gradle build
```

A létrehozás befejeződése után futtassa a következőket:

```console
gradle run
```

## <a name="sample-response"></a>Mintaválasz

Keresse meg az ország/régió rövidítést ebben a [listában](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a Translator Text API.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
