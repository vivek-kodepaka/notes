# Fixing SSLHandshakeException in Java (Interview Notes)

## Scenario

While calling an HTTPS API from a Java or Spring Boot application, you
may see:

javax.net.ssl.SSLHandshakeException: PKIX path building failed unable to
find valid certification path

Meaning: The JVM truststore does not trust the server certificate or its
Certificate Authority (CA).

------------------------------------------------------------------------

## Step-by-Step Solution

### Step 1: Verify URL in Browser

Open the same URL in Chrome or another browser:

https://internal-api.company.com

If the browser works but Java fails: - Certificate is valid - JVM
truststore likely missing the CA

------------------------------------------------------------------------

### Step 2: Export Certificate from Browser

In Chrome: 1. Click the lock icon near the URL. 2. Click "Connection is
secure". 3. Click "Certificate is valid". 4. Go to the "Details" tab. 5.
Click "Export". 6. Save the file as: server.crt

Best practice: Export the Root CA or Intermediate CA if available.

------------------------------------------------------------------------

### Step 3: Locate JVM Truststore

Default location: \$JAVA_HOME/lib/security/cacerts

Check Java home:

Windows: echo %JAVA_HOME%

Linux/Mac: echo \$JAVA_HOME

------------------------------------------------------------------------

### Step 4: Import Certificate Using keytool

Command:

keytool -import -trustcacerts -alias internal-api -file server.crt
-keystore cacerts

Default password: changeit

After confirmation: Certificate was added to keystore

------------------------------------------------------------------------

### Step 5: Restart Application

Restart your Java or Spring Boot application so the new certificate is
picked up.

------------------------------------------------------------------------

## Better Production Approach (Recommended)

Instead of modifying global cacerts, create a custom truststore:

keytool -import -alias internal-api -file server.crt -keystore
truststore.jks

Run application with:

-Djavax.net.ssl.trustStore=truststore.jks
-Djavax.net.ssl.trustStorePassword=changeit

Advantages: - Does not affect global JVM configuration - Easier to
manage in microservices

------------------------------------------------------------------------

## Why Browser Works but Java Fails

Browser: - Uses OS truststore - Corporate or internal CA may already be
trusted

Java: - Uses its own cacerts truststore - CA may be missing

------------------------------------------------------------------------

## Useful Debug Command

To view certificate chain from terminal:

openssl s_client -connect host:443 -showcerts

------------------------------------------------------------------------

## Interview Answer (Short Version)

If asked how you resolved SSLHandshakeException:

"We encountered SSLHandshakeException because the JVM truststore did not
contain the CA certificate of the target service. We exported the
certificate from the browser and imported it into the JVM truststore
using keytool. After restarting the application, the TLS handshake
succeeded. In production, we prefer using a custom truststore instead of
modifying the global cacerts."
