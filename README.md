# Documentación Oficial para la API de Consulta Única

A continuación se describirá el consumo correcto de la API de Consulta.

## 📖 Información general

- Es necesario mencionar que este es un servicio REST y se consume a traves de un cliente HTTP. Esta pensado para utilizarse sobre un Backend, no se recomienda consumir directamente en Javascript sobre el navegador debido a tema de [CORS](https://developer.mozilla.org/es/docs/Web/HTTP/CORS).
- La autenticación a la API se realiza mediante el uso de una API Key la cual se debe enviar en `Header` del Request HTTP. El `Header` debe llamarse `X-API-KEY`.
- La API Key tiene una longitud de 48 caracteres hexadecimales.
- Para probar la API no es necesaria una API Key, solo debe omitirse el `Header` de `X-API-KEY`, la unica restricción de no tener una API Key es que hay un limite diario de 5 consultas diarias que se restablace cada día a las 12am hora del centro de México (UTC -6).
- En caso de decidirse por adquirir el servicio puede ver nuestros paquetes y precios en el siguiente enlace: <https://bit.ly/cu-services>
- Después de la compra estará recibiendo un correo con su API Key. Si pasan mas de 30 minutos desde su compra y no la ha recibido favor de comunicarse con nosotros mediante alguno de los medios disponibles descritos abajo.
- La URL base para consumir los servicios de la API es: `https://consultaunica.mx/api/v2/{service_name}` mediante el método `POST`.
- Se incluyen ejemplos avanzados de consumo en la documentación que tenemos para Postman: <https://bit.ly/cu-docs-pm>

## 💬 Medios de contacto

En caso de problemas o cualquier tema que quiera tratar, contamos con 3 medios de contacto disponibles:

- Mediante correo electrónico: [contacta.consulta.unica@gmail.com](mailto:contacta.consulta.unica@gmail.com)
- WhatsApp: <https://bit.ly/cu-wa>
- Telegram: <https://bit.ly/cu-tg1>

## 🙌🏼 Servicios disponibles

|Servicio|Utilidad|Precios|
|-|-|-|
|Repuve|Con un número de placa o de serie de un coche devolverá si tiene un reporte de Robo. Incluye el documento oficial expedido por Repuve|<https://bit.ly/cu-srv-repuve>|
|Ifetel|Con un número de teléfono de 10 digitos devolverá que tipo de teléfono es (fijo o móvil), la compañía teléfonica, la lada y la ciudad de registro|<https://bit.ly/cu-srv-ifetel>|
|CURP|Mediante los datos de una persona o con la CURP, devuelve si la CURP es válida o si los datos corresponde a una CURP conforme al sistema de Renapo. Incluye el documento oficial de Renapo que válida la CURP|<https://bit.ly/cu-srv-curp>|
|RFC|Mediante un RFC unicamente verifica si este RFC es válido ante el SAT. Atención: no devuelve ningún documento ni información personal|<https://bit.ly/cu-srv-rfc>|
|Estudios|Con el nombre completo o parcial de una persona verifica si esta cuenta con una cedula profesional registrada ante el Registro Nacional de Profesionistas. Atención: no devuelve ningún documento|<https://bit.ly/cu-srv-estudios>|
|IMSS|Mediante una CURP devuelve, en caso de existir, el NSS ademas de los documento probatorios. Una segunda variante puede devolver la constancia de vigencia de derechos y una tercer llamada puede devolver el documento de semanas cotizadas. Atención: Por lentitud del IMSS, los resultados pueden durar hasta 1 minuto|<https://bit.ly/cu-srv-imss>|
|Afore|Con una CURP se consulta si se cuenta con Afore así como su nombre y medios para ponerse en contacto.|<https://bit.ly/cu-srv-afore>|

## 🚀 Consumo de API

### 🌎 Códigos de respuesta

Códigos de respuesta que devuelven los endpoints de consumo:

|Código|Descripción|Descuenta un uso de API|
|-|-|-|
|`200`|✅ Todo estuvo bien, se devuelve contenido de la consulta|Si|
|`204`|⚠ No se encontraron resultados para los datos ingresados, no es un error|Si|
|`400`|❌ Hay un error con alguno de los datos ingresados|No|
|`404`|❌ No se encontró el servicio|No|
|`429`|❌ Limite alcanzado, se acabaron los usos de la API|No|
|`500`|❌ Error de un servicio externo|No|

Los errores `400`, `429` y `500` devuelve un JSON con la siguiente estructura:

```json
{
  "message": "Mensaje describiendo el error"
}
```

### 🚗 Consumiendo Repuve

- URL de Consumo: `https://consultaunica.mx/api/v2/repuve`
- Método: `POST`
- Headers (solo en caso de tener API Key):

```json
{
  "X-API-KEY": "1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f"
}
```

- Payload JSON de ejemplo, tanto la consulta con número de serie o placa dan el mismo resultado:

Con número de serie:

```json
{
  "vinNumber": "1A2B3C4D5E6F1A2B"
}
```

Con placa:

```json
{
  "carPlate": "1A2B3C4"
}
```

- Respuesta exitosa HTTP 200:

```json
{
  "documentUrl": "https://consultaunica.mx/static/pdf/repuve/1A2B3C4D5E6F1A2B.pdf",
  "carStatus": {
    "pgj": {
      "message": "RECOVERED",
      "code": 3
    },
    "ocra": {
      "message": "OK",
      "code": 1
    }
  },
  "carInfo": {
    "origin": "INDONESIA",
    "complementData": "INDONESIA",
    "type": "DEPORTIVA",
    "brand": "YAMAHA",
    "model": "MT-03",
    "comments": "",
    "axisNumber": "",
    "vinNumber": "1A2B3C4D5E6F1A2B",
    "assemblyPlant": "INDONESIA",
    "doorsNumber": "0",
    "plate": "",
    "platedIn": "SIN INFORMACION",
    "displacement": "123AB",
    "class": "MOTOCICLETA",
    "platedDate": null,
    "lastUpdatedDate": "2017-10-20",
    "registeredIn": "YAMAHA MOTOR DE MÉXICO S.A. DE C.V.",
    "year": 2018,
    "inscriptionDate": null,
    "version": "",
    "inscriptionNumber": "",
    "cylinders": "2 CILINDROS",
    "nciNumber": "1234ABC"
  }
}
```

Para saber si el coche es robado es necesario ingresar al objeto `carStatus.pgj.code` o `carStatus.ocra.code`, si el código es:

- `1`: El coche no tiene reporte de robo.
- `2`: El coche si cuenta con reporte de robo.
- `3`: Se reportó que se ha recuperado el coche.

### 📞 Consumiendo Ifetel

- URL de Consumo: `https://consultaunica.mx/api/v2/ifetel`
- Método: `POST`
- Headers (solo en caso de tener API Key):

```json
{
  "X-API-KEY": "1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f"
}
```

- Payload JSON de ejemplo:

```json
{
  "phoneNumber": "5512345678"
}
```

- Respuesta exitosa HTTP 200:

```json
{
  "phoneCompany": "Telmex",
  "phoneType": "Fijo",
  "registrationCity": "CUAUHTEMOC",
  "lada": "55"
}
```

### 🙋🏻‍♂️ Consumiendo CURP

- URL de Consumo: `https://consultaunica.mx/api/v2/curp`
- Método: `POST`
- Headers (solo en caso de tener API Key):

```json
{
  "X-API-KEY": "1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f"
}
```

- Payload JSON de ejemplo, tanto la consulta con curp o los datos persohnales dan el mismo resultado:

Con CURP:

```json
{
  "type": "validation",
  "validation": {
    "curp": "LOOA531113HTCPBN07"
  }
}
```

Con datos personales:

```json
{
  "type": "request",
  "request": {
    "name": "ANDRES MANUEL",
    "paternalName": "LOPEZ",
    "maternalName": "OBRADOR",
    "birthDate": "1953-11-13",
    "sex": "H",
    "birthEntity": "TC"
  }
}
```

El catálogo de Estados es el siguiente:

|Código|Estado|
|-|-|
|AS|Aguascalientes|
|BC|Baja California|
|BS|Baja California Sur|
|CC|Campeche|
|CL|Coahuila de Zaragoza|
|CM|Colima|
|CS|Chiapas|
|CH|Chihuahua|
|DF|Distrito Federal|
|DG|Durango|
|GT|Guanajuato|
|GR|Guerrero|
|HG|Hidalgo|
|JC|Jalisco|
|MC|Estado de México|
|MN|Michoacan de Ocampo|
|MS|Morelos|
|NT|Nayarit|
|NL|Nuevo León|
|OC|Oaxaca|
|PL|Puebla|
|QT|Queretaro de Arteaga|
|QR|Quintana Roo|
|SP|San Luis Potosi|
|SL|Sinaloa|
|SR|Sonora|
|TC|Tabasco|
|TS|Tamaulipas|
|TL|Tlaxcala|
|VZ|Veracruz|
|YN|Yucatan|
|ZS|Zacatecas|
|NE|Extranjero|

- Respuesta exitosa HTTP 200:

```json
{
  "rcnValid": true,
  "name": "ANDRES MANUEL",
  "probativeDocumentType": "Acta de Nacimiento",
  "probativeDocumentBirthCertificate": {
    "paperNumber": null,
    "bookNumber": null,
    "registrationEntityId": 27,
    "tomeNumber": null,
    "registrationMunicipalityId": 12,
    "actNumber": 1642,
    "registrationMunicipalityName": "MACUSPANA",
    "registrationYear": 1953,
    "registrationEntityName": "TABASCO"
  },
  "probativeDocumentMigratory": null,
  "documentUrl": "https://consultaunica.mx/static/pdf/curp/LOOA531113HTCPBN07.pdf",
  "birthEntity": "TABASCO",
  "maternalName": "OBRADOR",
  "probativeDocumentNaturalization": null,
  "nacionality": "MEXICO",
  "curp": "LOOA531113HTCPBN07",
  "birthDate": "1953-11-13",
  "paternalName": "LOPEZ",
  "sex": "HOMBRE"
}
```

### 🏢 Consumiendo RFC

- URL de Consumo: `https://consultaunica.mx/api/v2/curp`
- Método: `POST`
- Headers (solo en caso de tener API Key):

```json
{
  "X-API-KEY": "1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f"
}
```

- Payload JSON de ejemplo:

Con número de serie:

```json
{
  "rfc": "LOOA531113FI5"
}
```

- Respuesta exitosa HTTP 200:

RFC válido:

```json
{
  "isValid": true,
  "rfc": "LOOA531113FI5"
}
```

RFC no válido:

```json
{
    "isValid": false,
    "rfc": "LOOA531113XXX"
}
```

### Consumiendo Estudios

Working in Progress...

### Consumiendo IMSS

Working in Progress...

### Consumiendo Afore

Working in Progress...
