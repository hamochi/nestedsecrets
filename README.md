# nestedsecrets
A way to nest secrets in AWS secret manger. Import one secret into an other.

Example of nested secrets:

name: service1_db
```json
{
  "db_username": "user1",
  "db_password": "jas3d90sf0%scdWE/sksj9"
}
```


name: service1_app
```json
{
  "{service1_db}" : "",
  "api_key": "lj453jk#€%#7569g0+"
}
```

above would output:
```json
{
  "db_username": "user1",
  "db_password": "jas3d90sf0%scdWE/sksj9",
  "api_key": "lj453jk#€%#7569g0+"
}
```

name: service2
```json
{
  "service1_endpoint": "https://service1.myservice.com",
  "service1_key": "${service1_app.api_key}"
}
```

above would output:
```json
{
  "service1_endpoint": "https://service1.myservice.com",
  "service1": "lj453jk#€%#7569g0+"
}
```