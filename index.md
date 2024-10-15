---
title: Instrucciones hospedadas en línea
permalink: index.html
layout: home
---

# Directorio de contenido

Los archivos necesarios para el ejercicio se pueden [DESCARGAR AQUÍ](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/archive/master.zip)

Desplázate hacia abajo para ver las demostraciones. 

## Ejercicio

{% assign Exercise = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %}
| Módulo | Ejercicio |
| --- | --- | 
{% for activity in Exercise  %}| {{ activity.Exercise.module }} | [{{ activity.Exercise.title }}{% if activity.Exercise.type %} - {{ activity.Exercise.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

<!-- ## Demonstrations (under construction)

{% assign demos = site.pages | where_exp:"page", "page.url contains '/Instructions/Demos'" %}
| Demonstration |
| --- | 
{% for activity in demos  %}| [{{ activity.demo.title }}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
-->


