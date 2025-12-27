# Spring Framework / Spring Boot

*Notes issues de la documentation du blog : https://gayerie.dev/docs/spring/*

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


## L’inversion de contrôle

L’inversion de contrôle est liée étroitement au principe d’inversion de dépendance. Le principe d’inversion de dépendance est un des cinq principes SOLID.

### L’injection de dépendance

L’injection de dépendance est un mécanisme simple à mettre en œuvre dans le cadre de la programmation objet et qui permet de diminuer le couplage entre deux ou plusieurs objets.

Dans l'exemple ci dessous :

```java
public class ReservationSalleService {

  private ReservationSalleDao reservationSalleDao;

  public void reserver(ReservationSalle reservationSalle) {
    // faire un traitement nécessaire
    // (par exemple la validation de la réservation)

    // sauvegarder la réservation
    reservationSalleDao.sauver(reservationSalle);
  }

}
```

Le code ci-dessus, nous conduit à dire que pour réaliser son travail, la classe `ReservationSalleService` est dépendante d’objets des classes `ReservationSalleDao` et ReservationSalle. L’objet de type `ReservationSalleDao` est particulièrement intéressant car il est déclaré comme attribut de la classe.
Cela signifie que cet objet fait partie de **l’état** de notre service et nous conduit à nous poser un problème fondamental en architecture logicielle : quelle partie du programme doit être responsable de la création de cette instance de `ReservationSalleDao` ?

Une réponse simple serait de dire que c'est la classe qui devrait l'instancier, dans son constructeur comme ceci :


```java
public class ReservationSalleService {

  private ReservationSalleDao reservationSalleDao;

  public ReservationSalleService() {
    reservationSalleDao = new ReservationSalleDao();
  }
//...
```

Cette solution simple est un problème, car elle induit un couplage fort.

- On ne permet pas à ReservationSalleDao d'être une interface ou une abstraction (car on doit le donner le type concret).
- Il est possible que la création d’un objet de type `ReservationSalleDao` nécessite de passer des paramètres.

#### Solution 1 : *Service locator*

On peut créer une classe `ReservationSalleDaoLocator` qui agirait comme une classe outil pour accéder à une instance d’un objet `ReservationSalleDao` en appelant une méthode `get()`.

*le service locator est un Sigleton*

```java
public class ReservationSalleService {

  public void reserver(ReservationSalle reservationSalle) {
    // faire un traitement nécessaire
    // (par exemple la validation de la réservation)

    // sauvegarder la réservation
    ReservationSalleDao reservationSalleDao = ReservationSalleDaoLocator.get();
```

Mais cette solution rend le code moins lisible, et difficile à tester.
Elle peut aussi générer des effets de bord.


#### Solution optimale : Injection de dépendance

Le lien qui permet de supprimer tout lien de dépendance entre les deux classes : l'injection de dépendance par le constructeur.

```java
public class ReservationSalleService {

  private ReservationSalleDao reservationSalleDao;

  public ReservationSalleService(ReservationSalleDao reservationSalleDao) {
    this.reservationSalleDao = reservationSalleDao;
  }

  public void reserver(ReservationSalle reservationSalle) {
    // faire un traitement nécessaire
    // (par exemple la validation de la réservation)

    // sauvegarder la réservation
    reservationSalleDao.sauver(reservationSalle);
  }

}
```

On dit que la classe `ReservationSalleService` reçoit par injection une instance de `ReservationSalleDao`. Le lien de dépendance est clairement indiqué par la signature du constructeur et l’attribut `reservationSalleDao`.

Cette implémentation est préférable aux précédentes en repoussant le problème de la création d’un objet de type `ReservationSalleDao` en dehors de la classe `ReservationSalleService`.
Mais elle ne résout pas complètement le problème puisque nous imaginons qu’il va falloir qu’une autre partie du programme crée une instance de `ReservationSalleDao` et une instance de `ReservationSalleService` en passant l’objet DAO en paramètre. **C’est précisément la fonction du Spring Framework de réaliser ce type d’opérations.**

### L’inversion de contrôle

C'est le pincipe selon lequel le flot d'exécution d'un logiciel n'est plus sous le contrôle direct de l'application elle-même (le code du developpeur) mais du framework qui prend le contrôle. Et spring fait cela à partir du conteneur IoC.

### Conteneur d'IoC

Pour pouvoir être mise en pratique, l’inversion de contrôle implique l’existence d’un composant supplémentaire. Dans l’exemple que nous avons pris précédemment, un code tiers doit être responsable de créer une instance des classes `ReservationSalleDao` et `ReservationSalleService`. Il faut également que ce composant soit capable de réaliser l’injection de l’objet de type `ReservationSalleDao`.

La construction des objets de notre application va être déléguée à ce composant que l’on appelle un conteneur IoC (IoC container), qui va gérer leur cycle de vie.
**Le Spring Framework est avant tout un conteneur IoC**.

Comme son principe reste général, il va être possible d’utiliser le Spring Framework pour des types d’application très divers. Il faut simplement pouvoir laisser au Spring Framework la possibilité d’initialiser l’application au lancement en créant le conteneur IoC. Dans la terminologie du Spring Framework, le conteneur IoC est constitué d’un ou de plusieurs contextes d’application.

## Contexte d'application

[Contexte d'application](https://github.com/iliasse-e/application-context)

## Déclaration par annotations

[Annotation Spring](https://github.com/iliasse-e/spring-annotation)

## Configurer une application Spring

[Configurer app Spring](https://github.com/iliasse-e/spring-framework-configuration)

## Spring AOP : programmation orientée aspect

##
## Spring web MVC

##
## Spring et accès aux BDD

##
## Repositories

[Spring boot](https://github.com/iliasse-e/e-banking-backend)

[Spring cloud](https://github.com/iliasse-e/microcommerce)

[Spring security](https://github.com/iliasse-e/spring-security)
