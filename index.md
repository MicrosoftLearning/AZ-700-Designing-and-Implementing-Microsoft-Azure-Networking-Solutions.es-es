---
title: Instrucciones hospedadas en línea
permalink: index.html
layout: home
---

# Directorio de contenido

Los archivos necesarios para el ejercicio se pueden [DESCARGAR AQUÍ](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/archive/master.zip)

A continuación se enumeran hipervínculos a cada uno de los ejercicios.

## Ejercicio

{% assign Exercise = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %}
| Módulo | Ejercicio |
| --- | --- | 
{% for activity in Exercise  %}| {{ activity.Exercise.module }} | [{{ activity.Exercise.title }}{% if activity.Exercise.type %} - {{ activity.Exercise.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

## Demostraciones (en construcción)

{% assign demos = site.pages | where_exp:"page", "page.url contains '/Instructions/Demos'" %}
| Módulo | Demostración |
| --- | --- |
{% for activity in demos  %}| {{ activity.demo.module }} | [{{ activity.demo.title }}{% if activity.demo.type %} - {{ activity.demo.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

