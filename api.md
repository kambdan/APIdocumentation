
# Base de Datos

Esta API permite, realizar búsquedas de un medicamento y sus interacciones con otro medicamento de DrugBanks.
 


## Tech Stack

**Server:** Node, Express, Supabase con Postgrsql

**Deploy:** Supabase con Postgrsql

## Flujo

- Scrapping de Medicamentos

- Extracción de todas las interacciones con otros Medicamentos
- Transformación a formato .json
- Guardar en la base de datos


## API Reference

#### Agregar un medicamento

```http
  POST /api/v1/data/addDrug
```

| Parameter | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `name` | `string` | Nombre del medicamento a ingresar a la base de datos|
| `apiKey` | `string` | **Required**. La clave de la API|

Solo se agrega el medicamento, debido a que la interacción siempre actúan entre dos medicamentos. 
#### Agregar interacción

```http
  POST /api/v1/data/addDrugWithInteractions
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `drug`      | `string` | (Required) Nombre del medicamento principal |
| `interactions`      | `array` | (Required) Array de objetos de las interaciones. |
| `interactions.drug`      | `array` | (Required) Nombre del medicamento con el que interactua. |
| `interactions.interaction`      | `array` | (Required) Descripción de la interacción. |


#### Examples

```javascript
 {
  "drug": "Abatacept",
  "interactions": [
    {
      "drug": "Abemaciclib",
      "interaction": "The metabolism of Abemaciclib can be increased when combined with Abatacept."
    }
  ]
 }
```
#### Response Body:
La respuesta del API dependerá del resultado de la operación. Si hay errores al agregar interacciones, se devolverá un código de estado HTTP 400, de lo contrario, se devolverá un código de estado HTTP 200. En caso de un error interno del servidor, se devolverá un código de estado HTTP 500.
#### HTTP 200(Éxito)
Cuando las interacciones se agregan correctamente, existen dos casos:

1) Cuando no hay ninguna interacción todavia registrado con ese medicamento, además ese medicamento tampoco esta agregado en la base de datos.
```javascript
{
  "success": true,
  "message": "Medicamento e interacciones agregadas con éxito.",
  "errors": [
    {
      "success": true,
      "message": "Interacción entre Abatacept y Yohimbine agregada correctamente: Interacción agregada correctamente."
    }
  ]
}
```
2) Cuando el medicamento con el que interactúa ya está en la base de datos. Pero la interacción aun no está agregada.

``` javascript
{
  "success": true,
  "message": "Medicamento e interacciones agregadas con éxito.",
  "errors": [
    {
      "success": true,
      "message": "Interacción entre Abatacept y Vinflunine agregada correctamente: Interacción agregada correctamente."
    },
    {
      "success": false,
      "message": "Error al agregar la interacción entre Abatacept y Yohimbine: La interacción ya existe en la base de datos."
    }
  ]
}

```
