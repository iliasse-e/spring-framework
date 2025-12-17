# Spring Framework / Spring Boot

## Introduction

Spring framework est une réponse aux limites que pose JAVA EE.

Ces limites sont :

- Des API se révèlent souvent assez complexes *(Le modèle d’architecture sur lequel se base J2EE a pour objectif de laisser aux serveurs d’application la responsabilité de fournir les services prenant en charge la sécurité, la gestion des transactions, la gestion des accès aux services externes (comme les bases de données)… Le développeur d’application n’a plus à gérer directement ces aspects, mais il doit tout de même maîtriser ces problématiques et surtout connaître les différentes API nécessaires pour pouvoir développer des composants conformes à cette plate-forme)*.

- Un serveur centralisé, monolithique, exigeant en ressources et peu adapté à des redémarrages fréquents.

- Une remise à jour (qu'impose la vagaue du cloud computing et microservices) qui n'a pas été apporté.

Spring Framework propose de bâtir des applications qui embarquent elles-mêmes les services dont elles ont besoin.
Spring est un conteneur léger, contenant un seul conteneur, là ou JEE se compose de plusieurs conteneur (Servlet, EJB, JPA, ...).

Modulaire : Il permet d'intégrer tel ou tel module du Spring Framework et de laisser de côté ceux qui ne sont pas nécessaires (parfait pour les microservices).

Spring se base sur l'IoC et l'AOP, il fourni aussi de nombreux Design Pattern (ex: Singleton pour @Service ou @DataSource) pour offrir au développeur le soin de se concentrer sur le code métier.

## Repositories

[Spring boot](https://github.com/iliasse-e/e-banking-backend)

[Spring cloud](https://github.com/iliasse-e/microcommerce)

[Spring security](https://github.com/iliasse-e/spring-security)
