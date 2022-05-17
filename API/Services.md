# 5. Konfiguracja usług

## 5.1. POST /services/p1/{organizationUuid}

Certyfikaty WSS i TLS do komunikacji z P1 (dla praktyki lub podmiotu)

```json
{
  "tls": {
    "certificate": "__base64 encoded p12 file__",
    "password": "p4ssw0rd!"
  },
  "wss": {
    "certificate": "__base64 encoded p12 file__",
    "password": "p4ssw0rd!"
  }
}
```

## 5.2. POST /services/ewus/{practitionerUuid}

Ustawienia eWUŚ dla lekarza

```json
{
  "domain": "01",
  "operatorType": "doctor",
  "providerId": "abc123",
  "doctorId": "abc321",
  "username": "john",
  "password": "p4ssw0rd!"
}
```

## 5.3. POST /services/zus/{practitionerUuid}

Ustawienia ZUS dla lekarza

```json
{
  "certificate": "__base64 encoded pfx file__",
  "password": "p4ssw0rd!"
}
```
