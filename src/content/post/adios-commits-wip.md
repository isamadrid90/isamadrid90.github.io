---
title: "Adiós a los commits de WIP"
description: "Adiós a los commits de WIP"
publishDate: "9 July 2023"
cuid: cljv8z5t6000l0akx70tde1z8
slug: adios-a-los-commits-wip
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ylveRpZ8L1s/upload/2900799c24da246116848b4256a19463.jpeg
tags: ["intellij-idea", "intellij", "git", "ai-tools"]

---

Después de muchos años trabajando en equipo y desarrollando proyectos complejos soy muy consciente de lo importante que es que mis commits contengan la información necesaria para entender el cambio que he hecho por varias razones:

1. Nos ayudan a entender qué pretendemos conseguir con las lineas que hemos añadido, modificado o eliminado, lo que facilita la revisión de ese código y permite que proporcionemos un feedback más efectivo.

2. Sirven como una forma de documentación, ayudándonos a entender los cambios que se han introducido y, si los evaluamos en conjunto con los commits pasados, nos ayudan a hacernos una idea del contexto en el que se tomaron esas decisiones.

3. Son cruciales cuando vamos a hacer un rollback.


Hay bastantes más pero tampoco quiero dar mucho la brasa con este tema porque hay gente que sabe más que yo y lleva hablando años de esto.

Personalmente, creo que cuanto más tendemos hacia el trabajo asíncrono y a utilizar técnicas como Trunk Base Development los commits cobran más importancia; pero quiero ser sincera, en algunas ocasiones para mi son lo último de lo que me preocupo. Si hay algo fallando en producción es muy probable que mi commit sea algo así "Fix X failing" y me quedo tan a gusto 😎, o si estamos haciendo pair programming y estamos cambiando el driver puede que mi commit sea solo "WIP", incluso a veces si estoy muy frustrada intentando hacer que algo funcione puede que ponga "Another try to make it work" y, la verdad es que esto no es muy útil para el futuro, ni aporta ningún contexto.

## Jetbrains AI assistant al rescate

Quizá la gente que ya lleve algún tiempo trabajando con Copilot o algún otro asistente que utilice Inteligencia Artificial no se sorprenda con esto, en mi caso, mi relación con la AI se basa en preguntarle cosas a chatGPT, probarlas y repetir el proceso hasta que lo que me dice funciona, o mejorar los textos que escribo.

Pero bueno al tema, en la última versión EAP de IntelliJ Ultimate se ha incorporado un asistente que podría poner fin a mis 💩 commits y utilizarlo es muy muy sencillo, especialmente si estas acostumbrada a utilizar el IDE para hacer commits, no es mi caso, a mi me gusta usar la terminal pero estoy cambiando y renovandome 💁‍♀️

Veamos como utilizarlo:

1. Accedemos a la sección de Commit de nuestro IDEA, en mi caso puedo usar los shortcuts Cmd+0 o Cmd+k

   ![Captura de pantalla de IntelliJ IDEA de cómo acceder a la sección de Commit mediante el menú lateral](https://cdn.hashnode.com/res/hashnode/image/upload/v1688892312759/8af4cb47-e4a3-4dd8-aa8b-22bf43469308.png align="center")

2. Una vez ahí seleccionamos los ficheros que queramos commitear, es importante resaltar que lo mejor es ir subiendo pequeños cambios, de esta manera los mensajes serán más completos

   ![Captura de pantalla de la sección de commit de IntelliJ IDEA resaltando la zona con todos los ficheros modificados](https://cdn.hashnode.com/res/hashnode/image/upload/v1688892508698/aa1f61a0-4a80-4353-b197-283628deff79.png align="center")

3. Para escribir el mensaje de commit utilizando el asistente tenemos que pulsar en las estrellas moradas que hay sobre la parte donde escribiriamos

   ![Captura de pantalla de la sección commit de IntelliJ IDEA resaltando las estrells moradas para utilizar el asistente al escribir mensajes de commit](https://cdn.hashnode.com/res/hashnode/image/upload/v1688892680263/5ed6efca-4b89-4f9b-ae99-40acdcb69765.png align="center")

4. Si es la primera vez que usamos el asistente nos pedirá que hagamos Log in, y *voilá!* ya tenemos nuestro commit message detallado, a continuación encontrareís un enlace para visualizar el ejemplo explicado


%[https://youtube.com/shorts/ByXYat8i7B0] 

Podéis encontrar más información sobre el asistente de IntelliJ [en su blog](https://blog.jetbrains.com/idea/2023/06/ai-assistant-in-jetbrains-ides/)
