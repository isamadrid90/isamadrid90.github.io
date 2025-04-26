---
title: "Refactorizando Kotlin con AI Assitant"
description: "Refactorizando Kotlin con AI Assitant"
publishDate: "6 August 2023"
cuid: clkztfm2m000109l4er95bvad
slug: refactorizando-kotlin-con-ai-assitant
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/-rF4kuvgHhU/upload/3ec4d1a6108935f050d3cd5b06e72935.jpeg
tags: ["ai", "intellij", "kotlin", "jetbrains"]

---

Ya escribí hace unas semanas sobre cómo el nuevo asistente de IntelliJ puede ayudarte a escribir mensajes de commit muy completos.

%[https://isabeliita90.hashnode.dev/adios-a-los-commits-wip] 

Hoy os quiero contar cómo puede ayudarnos a refactorizar nuestro código para aprovechar las funcionalidades de Kotlin al máximo 🚀

---

Imaginad que tenemos una data class Pet como esta:

![data class Pet con attributos name: String, color: String, type: String, breed: String](https://cdn.hashnode.com/res/hashnode/image/upload/v1690440858488/121fcf1b-75bc-45c5-b0d7-f375f8bc0433.png align="center")

Además tenemos un repositorio PetRepository para leerla de base de datos:

![interfaz PetRepository con una función findByName que recibe un name: String y devuelve Pet o null](https://cdn.hashnode.com/res/hashnode/image/upload/v1690440975978/0e1ecaf9-be0b-4e76-a684-896530d91c69.png align="center")

Y tenemos el siguiente caso de uso:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691314322619/9f417058-f01c-49c6-ab88-294ce009f5cb.png align="center")

El código que tenemos en el caso de uso consulta el repositorio, utilizando la interfaz PetRepository, si encuentra el nombre que hemos pedido guarda la información en una variable que utilizará después, en caso de no encontrarlo lanza una excepción.

Hasta aquí todo bien; pero no estamos aprovechando en lenguaje al máximo, vamos a refactorizar nuestro caso de uso utilizando [scope functions](https://kotlinlang.org/docs/scope-functions.html). Las scope functions son 5 funciones de Kotlin (let, run, apply, also y with) que nos permiten ejecutar código dentro del scope de un objeto, a mi me parecen muy útiles para casos como este porque nos permiten reducir el número de variables temporales y hacer el código más conciso. He profundizado más en las scope functions en el curso [Introducción a Kotlin](https://pro.codely.com/library/introduccion-a-kotlin-tu-primera-app-174088/about/) que hice con [Codely](https://codely.com/); pero si te gustaría que hablase más de esto dímelo en los comentarios 😊

## ¿Cómo hacemos el cambio?

Las scope functions y su uso no son triviales, por eso este tipo de refactors pueden ser complicados al principio.

Aquí vamos a ayudarnos del nuevo plugin [AI Assistant](https://plugins.jetbrains.com/plugin/22282-ai-assistant) de Jetbrains, para hacerlo seguiremos los siguientes pasos:

1. Seleccionamos el código que queremos refactorizar y lo copiamos.

2. Le pedimos a Jetbrains AI Assistant que refactorice el código utilizando scope functions y pegamos lo que hemos copiado antes.

3. Una vez que tengamos el resultado, podemos aplicar el cambio sobre nuestro código simplemente seleccionándolo otra vez y utilizando el botón de la parte superior derecha del snippet que nos ha generado el asistente.

   ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691347136535/799991d4-a96d-431c-8e16-96d9b0d16fa9.png align="center")


Eso sería todo, es muy fácil, a continuación os dejo un video para que lo veáis también.

%[https://youtu.be/qxA4toCmtuw] 

En este ejemplo nos recomienda utilizar la función *let* porque es muy útil para objetos que pueden ser null que es justo el caso del ejemplo; pero podríamos pedirle que utilizase otra scope function y aplicar el cambio igual que hemos hecho antes.

%[https://youtu.be/CQnmkVvvCbE] 

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">El ejemplo está hecho en un scratch, mi objetivo es hacer una serie de artículos haciendo que sea un proyecto completo.</div>
</div>

Si este ejemplo te ha resultado útil y te gustaría leer más cosas sobre este tema puedes seguirme en [hasnode](https://hashnode.com/@isabeliita90), [twitter](https://twitter.com/isabeliita90), [mastodon](https://mas.to/@isabeliita90), sería genial saber que os gustan estas cosas para seguir escribiendo sobre ellas .
