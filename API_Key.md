![amplo-logo](https://amplo.ch/wp-content/uploads/2020/07/28july-normal-1.png)
# JSON Web Token
This page explains how to use JSON Web Tokens (JWT) for authentication.
This authentication method is designed for IoT devices. 
Every request has a unique JWT and requires `client_id` and a `private key` for each device.
You can find your private keys and client ID on the Amplo Platform (https://portal.amplo.ch). 

### Python
`pip install jwt`
```python
import jwt, datetime

def create_jwt(client_id, private_key_file):
    '''
    Python function generating an RSA encrypted JSON Web Token.

    :param string client_id: Client ID for message.
    :param string private_key_file: Path to private key.

    :return string jwt: Encrypted JWT, ready to send along in HTTP Post.
    '''
    # Generate token
    token = {
            'iat': datetime.datetime.utcnow(),
            'exp': datetime.datetime.utcnow() + datetime.timedelta(minutes=15),
            'aud': project_id
    }
    # Load key
    with open(private_key_file, 'r') as f:
        private_key = f.read()
    # Return encrypted token
    return jwt.encode(token, private_key, algorithm='RS256').decode('ascii')
```
### Java
```java
import io.jsonwebtoken.JwtBuilder;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import java.io.Files;
import java.io.Paths;
import java.security.KeyFactory;
import java.security.spec.PKCS8EncodedKeySpec;

private static String createJwt(String clientId, String privateKeyFile) {
    DateTime now = new DateTime();
    /**
    * Takes project ID and private key location to generate an RSA encrypted JWT.
    */
    JwtBuilder jwtBuilder = Jwts.builder()
                                .setIssuedAt(now.toDate())
                                .setExpiration(now.plusMinutes(15).toDate())
                                .setAudience(clientId);
    byte[] keyBytes = Files.readAllByteS(Paths.get(privateKeyFile));
    encodedKey spec = new PKCS8EncodedKeySpec(keyBytes);
    keyFactory kf = KeyFactory.getInstance("RSA");
    return jwtBuilder.signWith(SignatureAlgorithm.RS256, kf.generatePrivate(spec)).compact()
}
```
### C++
You must first install the following packages:
* [Jansson](https://github.com/akheron/jansson)
* [JWT C Library](https://github.com/benmcollins/libjwt)
* [OpenSSL](https://github.com/openssl/openssl)
```C++
#define _XOPEN_SOURCE 500  // NOLINT
#include <stdbool.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#include <unistd.h>
#include "jwt.h"
#include "openssl/ec.h"
#include "openssl/evp.h"

// Gets the issued at and expiry timestamps
static void GetIatExp(char* iat, char* exp, int time_size) {
  time_t now_seconds = time(NULL);
  snprintf(iat, time_size, "%lu", now_seconds);
  snprintf(exp, time_size, "%lu", now_seconds + 15 * 60);
  if (TRACE) {
    printf("IAT: %s\n", iat);
    printf("EXP: %s\n", exp);
  }
}
// Takes client ID and private key location to generate an RSA encrypted JWT.
static char* CreateJwt(const char* private_key_file, const char* client_id) {
  char iat_time[sizeof(time_t) * 3 + 2];
  char exp_time[sizeof(time_t) * 3 + 2];
  uint8_t* key = NULL;  // Stores the Base64 encoded certificate
  size_t key_len = 0;
  jwt_t* jwt = NULL;
  int ret = 0;
  char* out = NULL;

  // Read private key from file
  FILE* fp = fopen(private_key_file, "r");
  if (fp == NULL) {
    printf("Could not open file: %s\n", private_key_file);
    return "";
  }
  fseek(fp, 0L, SEEK_END);
  key_len = ftell(fp);
  fseek(fp, 0L, SEEK_SET);
  key = malloc(sizeof(uint8_t) * (key_len + 1));  // certificate length + \0

  fread(key, 1, key_len, fp);
  key[key_len] = '\0';
  fclose(fp);

  // Get JWT parts
  GetIatExp(iat_time, exp_time, sizeof(iat_time));
  jwt_new(&jwt);

  // Write JWT
  ret = jwt_add_grant(jwt, "iat", iat_time);
  if (ret) {
    printf("Error setting issue timestamp: %d\n", ret);
  }
  ret = jwt_add_grant(jwt, "exp", exp_time);
  if (ret) {
    printf("Error setting expiration: %d\n", ret);
  }
  ret = jwt_add_grant(jwt, "aud", client_id);
  if (ret) {
    printf("Error adding audience: %d\n", ret);
  }
  ret = jwt_set_alg(jwt, JWT_ALG_RS256, key, key_len);
  if (ret) {
    printf("Error during set alg: %d\n", ret);
  }
  out = jwt_encode_str(jwt);
  if (!out) {
    perror("Error during token creation:");
  }
  // Print JWT
  if (TRACE) {
    printf("JWT: [%s]\n", out);
  }

  jwt_free(jwt);
  free(key);
  return out;
}
```
### Node.js
`npm install --save jsonwebtoken`
```js
import jwt from 'jsonwebtoken';

const createJwt = (clientId, privateKeyFile) => {
  // Takes project ID and private key location to generate RSA encrypted JWT.
  const token = {
    iat: parseInt(Date.now() / 1000),
    exp: parseInt(Date.now() / 1000) + 15 * 60,
    aud: clientId,
  };
  const privateKey = fs.readFileSync(privateKeyFile);
  return jwt.sign(token, privateKey, {algorithm: 'RS256'});
};
```
