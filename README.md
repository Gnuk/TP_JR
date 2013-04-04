# Simulation d'une Mine

![Interface graphique](http://i.imgur.com/n1qPWH1.png)

## À propos
Ce programme est créé dans le cadre de TP de parallélisme pour la formation STIC ISC dans le cadre du Master.

L'équipe est composée de :

* Anthony Rey
* Yohann Berthon

## L'application
Ce programme simule le comportement d'une pompe et d'un ventilateur pour réguler le niveau d'eau d'une mine en fonction de son niveau de gaz.

### Niveaux

* __Niveau H2O__ : Le niveau d'eau dans la mine, s'il dépasse un seuil, la pompe doit s'activer
* __Niveau CO__ : Le niveau de monoxyde de carbone, s'il dépasse un seuil, le ventilateur s’enclenche et tant qu'il est au dessus du seuil la pompe se désactive
* __Niveau CH4__ : Le niveau de méthane qui a le même comportement que le monoxyde de carbone

### Processus
Pour ce déroulement, plusieurs processus interviennent :

* __Tâches de détection__ pour vérifier les niveaux
* __Capteurs__ pour capter les niveaux
* __Commande__ pour lancer les tâches de détection
* __Environnement__ pour simuler la mine

## JR

JR est un langage qui propose des structures particulières pour réaliser du parallélisme :

* __send__ : Envoi asynchrone sur un canal
* __call__ : Envoi synchrone sur un canal
* __inni__ : Réception du canal

### Installation

#### Prérequis
Avant d'installer JR lui-même, il vous faudra

* Java
* Perl

#### Windows

#### Linux (Unix)

### Exemple
Cette classe utilise l'appel synchrone __call__ depuis _processus1_, on ne peut donc pas savoir quel __inni__ de _processus2_ ou _processus3_ va le recevoir.

On peut remarquer que les processus sont lancés de façon parallèle depuis le constructeur puisqu'il utilise un __send__.

#### Code

	public class CanalTest{
		/*
		* Cannaux
		*/
		private static op void canal(int);

		/*
		* Déclaration des
		*/
		private op void processus1();
		private op void processus2();
		private op void processus3();
		
		/**
		* Premier processus
		* Ce processus est utilisé comme un canal
		*/
		private void processus1()
		{
			call canal(42);
		}
		
		/**
		* Second processus
		*/
		private void processus2()
		{
			inni void canal(int v){
				System.out.println("Le processus 2 reçoit : " + v);
			}
		}
		
		/**
		* Troisième processus
		*/
		private void processus3()
		{
			inni void canal(int v){
				System.out.println("Le processus 3 reçoit : " + v);
			}
		}
		
		/**
		* Ce constructeur appelle les processus comme des cannaux
		*/
		private CanalTest()
		{
			send processus1();
			send processus2();
			send processus3();
		}
		public static void main(String [] args){
			new CanalTest();
		}
	}

#### Résultats possibles

Soit :

	$ jr CanalTest
	Le processus 2 reçoit : 42

Soit :

	$ jr CanalTest
	Le processus 3 reçoit : 42

### Limites

* Les processus peuvent être statique mais dans ce cas, ils sont automatiquement lancés et ne peuvent se rappeler eux-même, on doit alors utiliser des canaux pour pouvoir les appeler dynamiquement
* Il arrive que lorsqu'on force la mine à avoir un seuil élevé pour stopper la pompe lorsque le niveau de gaz est trop haut que JR ne traite plus ses _inni_, ceux-ci deviennent bloquant et l'environnement continue donc de tourner indépendamment des processus de détection.

