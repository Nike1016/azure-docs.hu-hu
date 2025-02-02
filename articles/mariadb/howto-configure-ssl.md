---
title: Az SSL-kapcsolat konfigurálása a Azure Database for MariaDBhoz való biztonságos csatlakozáshoz
description: Útmutatás a Azure Database for MariaDB és a társított alkalmazások megfelelő konfigurálásához az SSL-kapcsolatok megfelelő használatához
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/02/2019
ms.openlocfilehash: e57371bb7598a92f35dd4fd0ec22a55fad722987
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360499"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>Az SSL-kapcsolat konfigurálása az alkalmazásban a Azure Database for MariaDBhoz való biztonságos csatlakozáshoz
A Azure Database for MariaDB támogatja a Azure Database for MariaDB-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="obtain-ssl-certificate"></a>SSL-tanúsítvány beszerzése
Töltse le az SSL protokollon keresztüli kommunikációhoz szükséges tanúsítványt a [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) Azure Database for MariaDB-kiszolgálóról, és mentse a tanúsítványt a helyi meghajtóra (ez az oktatóanyag az c:\ssl-t használja például).
**A Microsoft Internet Explorer és a Microsoft Edge esetében:** A letöltés befejezése után nevezze át a tanúsítványt a BaltimoreCyberTrustRoot. CRT. PEM névre.

## <a name="bind-ssl"></a>SSL kötése

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Csatlakozás a kiszolgálóhoz a MySQL Workbench használatával SSL-en keresztül
Konfigurálja a MySQL Workbench-t a biztonságos SSL-kapcsolathoz. 

1. Az új kapcsolat beállítása párbeszédablakban navigáljon az **SSL** lapra. 

1. Frissítse az **SSL használata** mezőt a kötelező értékre.

1. Az **SSL-hitelesítésszolgáltató fájlja:** mezőben adja meg a **BaltimoreCyberTrustRoot. CRT. PEM**fájljának helyét. 
    
    ![SSL-konfiguráció mentése](./media/howto-configure-ssl/mysql-workbench-ssl.png)

A meglévő kapcsolatok esetében a jobb gombbal a kapcsolat ikonjára kattintva, majd a szerkesztés lehetőséget választva lehet kötni az SSL-t. Ezután navigáljon az **SSL** lapra, és kösse a tanúsítvány-fájlt.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Kapcsolódás a kiszolgálóhoz a MySQL CLI-vel SSL használatával
Az SSL-tanúsítvány kötésének másik módja a MySQL parancssori felület használata a következő parancsok végrehajtásával. 

```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> A MySQL parancssori felület Windows rendszeren való használatakor hibaüzenetet `SSL connection error: Certificate signature check failed`kaphat. Ha ez történik, cserélje le `--ssl-mode=REQUIRED --ssl-ca={filepath}` a `--ssl`paramétereket a következőre:.

## <a name="enforcing-ssl-connections-in-azure"></a>Az SSL-kapcsolatok kényszerítése az Azure-ban 
### <a name="using-the-azure-portal"></a>Az Azure Portal használata
A Azure Portal használatával keresse fel a Azure Database for MariaDB-kiszolgálót, majd kattintson a **kapcsolatbiztonsági**elemre. A váltógomb használatával engedélyezheti vagy letilthatja az **SSL-kapcsolat érvényesítése** beállítást, majd kattintson a **Mentés**gombra. A Microsoft azt javasolja, hogy mindig engedélyezze az **SSL-kapcsolat betartatása** beállítást a fokozott biztonság érdekében.
![engedélyezés – SSL](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Az **SSL-kényszerítési** paramétert engedélyezheti vagy letilthatja az Azure CLI-ben engedélyezett vagy letiltott értékek használatával.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>Az SSL-kapcsolat ellenőrzése
Futtassa a MySQL **status** parancsot annak ellenőrzéséhez, hogy az SSL használatával csatlakozott-e a MariaDB-kiszolgálóhoz:
```sql
status
```
Ellenőrizze, hogy a kapcsolat titkosítva van-e a kimenet áttekintésével, amelynek a következőknek kell megjelennie:  **SSL: A használatban lévő rejtjel a AES256-SHA** 

## <a name="sample-code"></a>Mintakód
Ha biztonságos kapcsolatot szeretne létesíteni az alkalmazásból az SSL-kapcsolaton keresztül Azure Database for MariaDB, tekintse meg az alábbi kódrészleteket:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mariadb.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mariadb.database.azure.com',
                       ssl={'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
