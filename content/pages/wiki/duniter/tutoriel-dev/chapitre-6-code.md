Title: VI. Code
Order: 1
Date: 2017-10-31
Slug: chapitre-6-code
Authors: cgeek

## Introduction

Dans ce chapitre, nous allons visualiser et détailler les éléments importants du code de Duniter, ceux les plus utilisés et les plus importants afin que vous puissiez mieux vous repérér dans le rôle de chaque élément.

Le but *n'est pas* de décrire chaque élément du code, mais juste ses grandes lignes. Les éléments non décrits sont jugés périphériques, et peuvent être étudiés ultérieurement par vos soins en lisant directement le code.

> Ce chapitre est théorique. Toutefois, vous pouvez placer des points d'arrêt dans les fichiers de code correspondants afin de constater la réalité de ce qui est décrit et d'étudier vous-même le fonctionnement, l'étude personnelle étant *in fine* une étape nécessaire.

## Le fichier `server.ts`

C'est **LE** fichier central de Duniter, d'où sont accessibles toutes les données et toutes les logiques propres au protocole Duniter.

::uml::

@startuml

server --> conf
server --> dal
server <--> BlockchainService
server <--> IdentityService
server <--> MembershipService
server <--> PeeringService

package "SQL : blockchain" {
    dal ---> iindexDAL
    dal ---> mindexDAL
    dal ---> sindexDAL
    dal ---> cindexDAL
    dal ---> bindexDAL
    dal ---> blockDAL
}

package "SQL : piscines" {
    dal ----> idtyDAL
    dal ----> certDAL
    dal ----> txsDAL
    dal ----> msDAL
}

package "SQL : autres" {
    dal ----> walletDAL
    dal ----> peerDAL
    dal ----> metaDAL
}

package "Fichiers" {
    dal --> confDAL
    dal --> statDAL
}

package "Addon C++" {
    dal --> wotb
}

@enduml

::end-uml::

### Propriété `server.conf`

Cette propriété contient la configuration chargée au démarrage de Duniter, puis éventuellement modifiée au cours de l'exécution par n'importe quel module. Toute partie de l'application a en général accès à cette configuration.

### Propriété `server.dal`

Une instance de la classe contenue dans le fichier `fileDAL.ts`. Permet l'accès à l'intégralité de la base de données de Duniter.

### Propriété `server.BlockchainService`

Accès au service de gestion de la blockchain, afin d'ajouter ou retirer des blocs. C'est ce service qui s'assure du respect du protocole Duniter lors de l'ajout d'un bloc en contrôlant sa forme et son contenu.

### Propriété `server.IdentityService`

Accès au service de gestion des identités et certifications en piscine.

### Propriété `server.MembershipService`

Accès au service de gestion des adhésions en piscine.

### Propriété `server.TransactionService`

Accès au service de gestion des transactions en piscine.

## Le fichier `fileDAL.ts`

Contient la classe centralisant l'accès à toutes les données de l'application.

### Des méthodes pour l'accès aux données

Cette classe contient des méthodes facilitatrices pour accéder à des données précises.

Par exemple la méthode `getMembers()` renvoie la liste des membres contenus dans la base de données. Mais obtenir cette information n'est pas si triviale étant donné qu'il faut explorer la blockchain et faire quelques jointures en base de données. Cette méthode masque toute cette complexité.

### Des objets pour un accès libre

Mais afin de permettre à tout module même encore inconnu d'avoir accès à la base de données, la classe du fichier `fileDAL.ts` contient également des membres permettant d'exécuter directement des requêtes sur la base de données : 

* `bindexDAL` : accès à la table `b_index` (données blockchain)
* `iindexDAL` : accès à la table `i_index` (données blockchain)
* `mindexDAL` : accès à la table `m_index` (données blockchain)
* `sindexDAL` : accès à la table `s_index` (données blockchain)
* `cindexDAL` : accès à la table `c_index` (données blockchain)
* `blockDAL` : accès à la table `block` (blockchain complète)
* `metaDAL` : accès à la table `meta` (méta-données de la base de données)
* `idtyDAL` : accès à la table `idty` (piscine des identités)
* `certDAL` : accès à la table `cert` (piscine des certifications)
* `msDAL` : accès à la table `membership` (piscine des adhésions)
* `txsDAL` : accès à la table `txs` (piscine des transactions)
* `peerDAL` : accès à la table `peer` (fiches de pair)
* `walletDAL` : accès à la table `wallet` (table calculée des comptes monétaires)
* `statDAL` : accès au fichier de statistiques `stats.json`
* `confDAL` : accès au fichier de configuration `conf.json`

## Les services

### Le fichier `BlockchainService.ts`

### Les autres fichier de service

Les fichiers `IdentityService.ts`, `MembershipService.ts`, `TransactionService.ts` et `PeeringService.ts` servent pour leur part à vérifier et accepter en pisicine les nouveaux documents respectivement d'identité et certification, d'adhésion, de transaction et de fiches de pair.

Aucun de ces services n'écrit dans la blockchain directement, mais en piscine : c'est une zone temporaire pour les données en attente d'être inscrites officiellement dans un bloc.

## La blockchain

### Fichier `BlockchainContext.ts`

### Fichier `DuniterBlockchain.ts`

### Fichier `Switcher.ts`