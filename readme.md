## Objectifs du système à modéliser

Application : Plateforme de gestion de covoiturage en temps réel

Créer une plateforme de covoiturage où les utilisateurs peuvent publier des trajets, rechercher des trajets disponibles, réserver une place, et suivre le trajet en temps réel.

Fonctionnalités principales :

Gestion des utilisateurs : Enregistrement, connexion et gestion du profil.

Gestion des rôles : conducteur ou passager.

Publication de trajets : Les conducteurs peuvent publier des trajets avec des détails comme la ville de départ, la destination, les horaires, le prix, et les places disponibles.

Recherche et réservation de trajets : Recherche avancée de trajets (par localisation, date, prix, etc.)
Réservation de places disponibles.

Paiements en ligne : Intégration d'une solution de paiement (ex : Stripe, PayPal).

Notifications en temps réel : Notifications pour les confirmations de réservation, les annulations, et les rappels de trajets.


## Interfaces

```
artist->master: POST venue
vendor->master: GET Gigs
master->vendor: Collection<Gigs>

Customer->vendor: cli:gig selection

vendor->master: jms:booking
alt booking successfull
    master->vendor: transitional tickets
    vendor->Customer: ticket purshase ok
    Customer->vendor: cli:customer informations
    
    vendor->master: jms:ticketing
    master->vendor: tickets

else booking unsuccessfull
    master->vendor: no quota for gigs
end

opt venue cancellation
    artist->master: DELETE venue
    master->vendor: jms:topic:cancellation
    vendor->Customer: smtp:cancellation email
end
```
![](seqDiagram.png)

## Schéma relationnel

![](EER.png)

## Exigences fonctionnelles

* le vendor NE DOIT proposer que les concerts pour lesquels il a un quota disponible, transmis par le master.
* le vendor DOIT pouvoir effectuer les opérations de booking et ticketing
* le master DOIT permettre à l'artiste d'annuler son concert.
* le master DOIT informer le vendor en cas d'annulation de concert
* le vendor DOIT informer les clients de l'annulation du concert par mail
* le master DOIT proposer un service de validation de la clé du ticket, pour les contrôles aux entées.

## Exigences non fonctionnelles

* le booking et le ticketing, bien qu'étant des opérations synchrones, DOIVENT être fiables et donc utiliser le messaging
* Lors de l'annulation de tickets, le master DOIT informer tous les vendors de l'annulation, de façon fiable.
