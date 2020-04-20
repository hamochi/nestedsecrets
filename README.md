# Nested Secrets
A way to nest secrets in AWS secret manger. Embed one secret into an other.

## Usage
#### Embed secrets 
secret name: service1_db
```json
{
  "db_username": "user1",
  "db_password": "jas3d90sf0%scdWE/sksj9"
}
```

secret name: service1_app
```json
{
  "db_username" : "${service1_db.db_username}",
  "db_password" : "${service1_db.db_password}",
  "api_key": "lj453jk#€%#7569g0+"
}
```

```go
package main

import (
	"encoding/json"
	"fmt"
	"github.com/hamochi/nestedsecrets"
	"log"
)

// service1_app from above example
type Secrets struct {
	DBUser     string `json:"db_username"`
	DBPassword string `json:"db_password"`
	APIKey     string `json:"api_key"`
}

func main() {
	result, err := nestedsecrets.Load("service1_app")
	if err != nil {
		log.Fatal(err)
	}

	var s Secrets
	err = json.Unmarshal(result, &s)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Printf("%+v", s)
}
```

output (in json):
```json
{
  "db_username": "user1",
  "db_password": "jas3d90sf0%scdWE/sksj9",
  "api_key": "lj453jk#€%#7569g0+"
}
```

#### Raw embed

secret name: pub_key:
```text
-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEA0TqlveKKlc2MFvEmuXJi
LGBsY1t4ML4uiRADGSZlnc+7Ugv3h+MCjkkwOKiOdsNo8k4KSBIG5GcQfKYOOd17
AJvqCL6cGQbaLuqv0a64jeDm8oO8/xN/IM0oKw7rMr/2oAJOgIsfeXPkRxWWic9A
VIS++H5Qi2r7bUFX+cqFsyUCAwEAAQ==
-----END PUBLIC KEY-----
```

secret name: private_key:
```text
-----BEGIN RSA PRIVATE KEY-----
MIICXAIBAAKBgQCW0PEHRPo+zGRJnxMurwzJdAso7vU9uMew9FeE2r3Ntr7hmzus
HpWvYHs3DWnWGkx38rgrstKSdQoPmiSPR+3C5cDh9AOoMDIp/7eLlTAgblBCYgOo
ul7JMxANs3n+d/8OFR1f1tE22W2JDwpTW7PKv7/7+Qi0gt3wwme91xa5eQIDAQAB
AoGAUj8H8srvHaghWPTjZSYZdxNNsrj+DPbkD+BwigcwUytB1Y7ub+MOBmI7T76E
5TnaUd+QCGSw6SXioreREIJNapGEqnx/Q0Ofew8TUCyjx9kUWZi7F7Rt6pPCamjt
gTw3G17TikevMcnNYgp9RCgc0K3PSJT3D+VXS/LU90cJq8ECQQDOjH3yHxwGeJc7
UxYVlgJyQ/zbxEBWDw7Cx1OiqZ4xZWUT+ojP0Tfg0NWb6ibpXk2r+60A9yoHq6j9
9b1i7cRFAkEAuuyPx0JwlGcnGwu7JnfKDvwt0m0/MdDspwediJsgV53f+Ppu//Dx
hslBRmCQHWeBLyhmNBh/PzwgjppT6khlpQJBAMrv4KLui/OTsks4dD9bNyYbxKoM
pYWon8EiZPsyGlrAG9LIWnmNP5CkvuvE4Nxzqm7rBW0oa2RD9fMn6h70K90CQF6x
WGTpE38agoUA/F4QcRwGRHBFR65E2J4FDZkWfNvl7oCC3ZhqxvAxJBAy+s7qkFcD
cEMT5eJy40tagwg8hMECQB6FlhL+1KyhJXmDMkTw5MqHWC+nj99GhpYPmtoXrlps
wKGElLSd3J4rEjm+SqWI7B2HOFDEA4s5NMz+1PvHy+M=
-----END RSA PRIVATE KEY-----
```

secret name: envfile
````text
APP_NAME="My App"
PUB_KEY="${pub_key.RAW}"
PRIVATE_KEY="${private_key.RAW}"
````

```go
package main

import (
	"encoding/json"
	"fmt"
	"github.com/hamochi/nestedsecrets"
	"log"
)

func main() {
	result, err := nestedsecrets.Load("envfile")
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println(string(result))
}
```