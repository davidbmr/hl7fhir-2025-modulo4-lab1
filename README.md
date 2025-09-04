# 🧪 Laboratorio: Trabajando con CodeSystem, ValueSet y ConceptMap en FHIR R5

## 🎯 Objetivo del laboratorio
Aprender a crear, consultar e integrar recursos terminológicos en FHIR:

- Definir un **CodeSystem** propio.  
- Crear un **ValueSet** que use ese CodeSystem.  
- Construir un **ConceptMap** que relacione conceptos entre dos sistemas de codificación.  
- Ejecutar operaciones comunes (`$validate-code`, `$expand`, `$translate`).  

---

## 🔹 Paso 1: Crear un CodeSystem

1. Abre tu editor de texto o Postman/Insomnia.  
2. Define un recurso CodeSystem sencillo:  

```json
{
  "resourceType": "CodeSystem",
  "url": "http://hl7peru.org/fhir/CodeSystem/local-lab-tests", 
  "version": "1.0.0",
  "name": "LocalLabTests",
  "status": "active",
  "content": "complete",
  "concept": [
    { "code": "HB", "display": "Hemoglobina" },
    { "code": "GLU", "display": "Glucosa en sangre" },
    { "code": "CHOL", "display": "Colesterol total" }
  ]
}
```
3. Envía el recurso a tu servidor FHIR en la colección `CodeSystem`.

## 🔹 Paso 2: Crear un ValueSet

1. Define un ValueSet que referencie tu CodeSystem:
   
```json
{
  "resourceType": "ValueSet",
  "id": "vs-basic-lab-tests",
  "url": "http://hl7peru.org/fhir/ValueSet/vs-basic-lab-tests",
  "version": "1.0.0",
  "name": "BasicLabTests",
  "status": "active",
  "compose": {
    "include": [
      {
        "system": "http://hl7peru.org/fhir/CodeSystem/local-lab-tests"
      }
    ]
  }
}

```
2. Cárgalo en el servidor FHIR.
3. Prueba la operación $expand para ver los conceptos incluidos:

```http
GET [base]/ValueSet/vs-basic-lab-tests/$expand
```
## 🔹 Paso 3: Crear un ConceptMap

1. Relaciona tus códigos locales con códigos de LOINC (ejemplo):
```json
{
  "resourceType": "ConceptMap",
  "url": "http://hl7peru.org/fhir/ConceptMap/cm-lab-to-loinc",
  "version": "1.0.0",
  "name": "LabToLoinc",
  "status": "active",
  "group": [
    {
      "source": "http://hl7peru.org/fhir/CodeSystem/local-lab-tests",
      "target": "http://loinc.org",
      "element": [
        {
          "code": "HB",
          "target": [
            {
              "code": "718-7",
              "display": "Hemoglobin [Mass/volume] in Blood",
              "equivalence": "equivalent"
            }
          ]
        },
        {
          "code": "GLU",
          "target": [
            {
              "code": "2345-7",
              "display": "Glucose [Mass/volume] in Blood",
              "equivalence": "equivalent"
            }
          ]
        }
      ]
    }
  ]
}
```
  
2. Envía el recurso al servidor.

## 🔹 Paso 4: Probar operaciones de validación y traducción

1. Validar un código en tu ValueSet:
```http
GET [base]/ValueSet/$validate-code?url=http://hl7peru.org/fhir/ValueSet/vs-basic-lab-tests&code=GLU
```

2. Traducir un código usando el ConceptMap:
```http
GET [base]/ConceptMap/$translate?url=http://hl7peru.org/fhir/ConceptMap/cm-lab-to-loinc&code=HB
```
---

# ✅ Resultado esperado

- Haber creado **3 recursos terminológicos** en tu servidor FHIR.  
- Entender cómo funcionan las operaciones **$expand**, **$validate-code** y **$translate**.  
- Relacionar **códigos locales** con terminologías estándar como **LOINC**.  

