---
title: Liqen API reference

language_tabs:
  - javascript

includes:
  - errors

search: false
---

# Liqen API

- Autenticación
- Recurso `questions`
- Recurso `articles`
- Recurso `facts`
- Recurso `annotations`

# Autenticación

TBA

# Anotaciones

```json
{
	"@context": "http://www.w3.org/ns/anno.jsonld",
	"type": "Annotation",
	"annotation_id": "1",
	"id": "<API_ROOT>/annotations/1",
	"body": {
		"id": "<ONTOLOGY_ROOT>"
	},
	"target": {
		"source": "<ARTICLES_ROOT>/articles/1",
		"type": "FragmentSelector",
		"value": "p1",
		"refinedBy": {
			"type": "TextQuoteSelector",
			"prefix": "1.- ",
			"exact": "Turquía",
			"suffix": " es el país que más refugiados alberga --2.541.352--, con una población de unos 80 millones de personas."
		}
	},
	"dimension": "what"
}
```

El recurso **Annotation** describe una Anotación, un fragmento de texto con valor semántico. Se compone de tres elementos fundamentales:

1. Un fragmento de texto del que se infiere una información.
2. Un concepto al que se refiere el fragmento de texto.
3. Una dimensión a la que se responde con el fragmento.

Se debe entender que el recurso Anotación es una *extensión* o subclase del objeto [Web Annotation][w3-annotation-model].

## Atributos

Estos son los atributos del recurso Annotation. La mayoría de atributos pertenecen al modelo de datos descrito en la [especificación W3C de Web Annotation][w3-annotation-model].

En tanto que la Anotación Liqen es una extensión de *Web Annotation*, todos los atributos que tiene dicho objeto, también pueden llegar a pertenecer a la Anotación de Liqen.

Los siguientes atributos son obligatorios en el objeto Anotación de Liqen pero no pertenecen a Web Annotation:

Atributo|Tipo|Descripción
--------|----|-----------
annotation_id|string|Identificador de la anotación.
dimension    |string|Dimensión a la que se responde con el fragmento de texto. Valores admitidos: `what`, `who`, `where`, `when`, `why`.

Los siguientes atributos, que pertenecen al modelo de Web Annotation, son obligatorios en el objeto Anotación de Liqen:

Atributo|Tipo|Descripción
--------|----|-----------
@context|string|Contexto LD-JSON. Tiene el valor fijo `http://www.w3.org/ns/anno.jsonld`.
type    |string|Tipo de recurso. Tiene el valor fijo `Annotation`.
id      |string|IRI que referencia a la propia anotación (a sí mismo).
body    |object|Concepto al que se refiere el fragmento de texto.
body.id |string|IRI del concepto, normalmente referencia a un concepto de una ontología
target  |object|Fragmento del texto que tiene el valor del concepto. Explicado a continuación.

## Atributo `target`. Seleccionar un fragmento de texto

El **fragmento de texto** se debe definir de manera inequívoca mediante la propiedad `target` del objeto Anotación en tres niveles:

1. El recurso. La propiedad `source` debe indicar una IRI permanente del recurso al que pertenece el fragmento de texto
2. El fragmento. Se debe seleccionar un párrafo o sección del documento con un selector de tipo [Fragment](https://www.w3.org/TR/annotation-model/#fragment-selector), [XPath](https://www.w3.org/TR/annotation-model/#xpath-selector) o [CSS](https://www.w3.org/TR/annotation-model/#css-selector) según se indica en las secciones correspondientes de la especificación. Se deben usar las propiedades `type` y `value`.
3. Texto dentro del fragmento. Se debe seleccionar una frase dentro del fragmento utilizando la propiedad `refinedBy`. Esta propiedad a su vez debe ser un selector de tipo [Text Quote](https://www.w3.org/TR/annotation-model/#text-quote-selector).

**Ejemplo**. Supongamos en la URI `http://example.com/article/1` encontramos el extracto:

> En un lugar de la Mancha, de cuyo nombre no quiero acordarme, no ha mucho tiempo que vivía un hidalgo de los de lanza en astillero, adarga antigua, rocín flaco y galgo corredor. Una olla de algo más vaca que carnero, salpicón...

Cuyo HTML es:

```html
<p id="p1">En un lugar de la Mancha, de cuyo nombre no quiero acordarme, no ha mucho tiempo que vivía un hidalgo de los de lanza en astillero, adarga antigua, rocín flaco y galgo corredor. Una olla de algo más vaca que carnero, salpicón...</p>
```

Y queremos resaltar el texto "adarga". El objeto que representa esta selección (y por tanto el valor de la propiedad `target` de la anotación) es:

```json
{
	"source": "http://example.com/article/1",
	"selector": {
		"type": "FragmentSelector",
		"value": "p1",
		"refinedBy": {
			"type": "TextQuoteSelector",
			"prefix": "lanza en astillero, ",
			"exact": "adarga",
			"suffix": " antigua"
		}
	}
}
```

## Obtener un listado de anotaciones

> Ejemplo de petición

```shell
curl "<API_ROOT>/annotations/"
```

### HTTP Request

`GET /annotations`

### Response

Array de anotaciones. Devuelve un máximo de 10 elementos.

```json
[
	{
		"id": "1",
		"link": 
	}
]
```

## Obtener una anotación

> Ejemplo de petición

```shell
curl "<API_ROOT>/annotations/1"
```

### HTTP Request

`GET /annotations/<ID>`

### URL Parameters

Parameter|Description
---------|-----------
ID       |ID de la anotación que se quiere obtener

## Crear una anotación

`POST /annotations `

El cuerpo de la petición debe un JSON con, al menos, todos los atributos indicados en el epígrafe **Atributos**

---

# Hechos




---

---

---

---


# Recurso Preguntas

```json
{
	"id": "1",
	"title": "¿Qué país ha acogido el mayor número de refugiados en 2016?",
	"tags": [
		{
			"title": "País",
			"dimension": "where",
			"link": "http://example.com/ontology#country"
		}
	]
}
```

Es un objeto que representa una pregunta planteada en Liqen. La pregunta se compone de los siguientes atributos

- **id**. Identificador de la pregunta.
- **title**. Título de la pregunta.
- **tags**. Conceptos que deben responder a la pregunta.
- **tags.dimension**. Dimensión del concepto. Puede tomar uno de los los valores `what`, `where`, `when`, `who` o `why`.
- **tags.link**. Referencia al concepto. Debe ser una IRI, normalmente una IRI de un término de una ontología.
- **tags.title**. Título del concepto.

## Obtener todas las preguntas

> Ejemplo de petición

```shell
curl "<API_ROOT>/questions"
```

> Ejemplo de respuesta

```json
[
	{
		"id": "1"
		"link": "/questions/1",	
	},
	{
		"id": "2"
		"link": "/questions/1",
	}
]
```

Retorna una lista de todas preguntas planteadas en Liqen. Incluyendo parámetros a la petición GET se pueden filtrar los objetos que se desea recibir.

La lista recibida es una lista de objetos que tiene al menos dos propiedades:

- **id**. Identificador único de la pregunta
- **link**. Referencia al recurso

### HTTP Request

`GET /questions`

### Query Parameters

Parameter    |Default|Description
-------------|-------|------------
annotation_id|null   |Filtrar solo las preguntas a las que ha respondido una anotación

## Obtener una pregunta sola

> Ejemplo de petición

```shell
curl "<API_ROOT>/questions/1"
```

> Ejemplo de respuesta

```json
{
	"id": "1",
	"title": "¿Qué país ha acogido el mayor número de refugiados en 2016?",
	"tags": [
		{
			"title": "País",
			"dimension": "where",
			"link": "<ONTOLOGY_ROOT>#country"
		}
	]
}
```

### HTTP Request

`GET /questions/<ID>`

### URL Parameters

Parameter|Description
---------|-----------
ID       |ID de la pregunta que se quiere obtener

# Articles

TBA

# Facts

El recurso `fact` se define como una agrupación de anotaciones que hacen referencia a un mismo hecho.

Un hecho incluye al menos una anotación.

## Obtener todos las hechos

```shell
curl "<API_ROOT>/facts"
```

> Ejemplo de respuesta JSON

```json
[
	{
		"id": "1"
		"link": "/facts/1",
	},
	{
		"id": "2"
		"link": "/facts/2",
	}
]
```

### HTTP Request

`GET /facts`

## Obtener un hecho

```shell
curl "<API_ROOT>/facts/1"
```

> Ejemplo de respuesta JSON

```json
{
	"id": "1",
	"link": "/answers/1",
	"article": "1",
	"annotations": [
		{
			"@context": "http://www.w3.org/ns/anno.jsonld",
			"type": "Annotation",
			"id": "<API_ROOT>/annotations/1",
			"body": {
				"id": "<ONTOLOGY_ROOT>"
			},
			"target": {
				"source": "<ARTICLES_ROOT>/articles/1",
				"type": "FragmentSelector",
				"value": "p1",
				"refinedBy": {
					"type": "TextQuoteSelector",
					"prefix": "1.- ",
					"exact": "Turquía",
					"suffix": " es el país que más refugiados alberga --2.541.352--, con una población de unos 80 millones de personas."
				}
			},
			"dimension": "what"
		}
	]
}
```

[w3-annotation-model]: https://www.w3.org/TR/annotation-model/ "Web Annotation Data Model"