# Diplochain-Site-Public-Phase2
DiploChain — Site de Démonstration Phase 2
●	Rôle du site Phase 2
Le site Phase 2 est une interface de démonstration dédiée à la demi finale du Hackathon Miabe 2026. Il ne cherche plus à “vendre” l’idée (Phase 1), mais à prouver concrètement le fonctionnement du registre de diplômes sur blockchain.
Il remplit trois objectifs techniques principaux :
•	Exposer clairement le flux de vérification (émission, inscription, vérification) ;
•	Démontrer la logique de hash SHA 256 + signature ECDSA à travers une interface compréhensible ;
•	Montrer qu’un recruteur peut vérifier un diplôme en quelques secondes, sans appel téléphonique ni accès au système interne d’un ministère.
Phase 1 = vitrine narrative (“pourquoi c’est important ?”).
Phase 2 = démonstration fonctionnelle (“ça marche comment, concrètement ?”).

●	 Architecture du site
Le site Phase 2 est une application web statique déployée sur GitHub Pages. Aucun serveur applicatif n’est requis pour la version de démonstration :
•	HTML, CSS et JavaScript pur (vanilla), sans Framework.
•	Hébergement via GitHub Pages à partir du dépôt DiplochainSitePublic_Phase2 (branche main).
•	Zéro base de données, zéro backend pour la démo : la logique de vérification est simulée côté client dans le navigateur.
Cette architecture respecte les contraintes du hackathon (déploiement rapide, infrastructure minimale) tout en restant cohérente avec la vision finale, où le portail appellera un backend de vérification connectée à une blockchain réelle.

●	 Design et expérience utilisateur
L’interface adopte une esthétique institutionnelle africaine :
•	Palette : fond blanc cassé, vert émeraude, or, rappels des identités visuelles universitaires et administratives ;
•	Typographies : Fraunces (titres) et Space Mono (éléments techniques, chiffres, labels) pour différencier texte narratif et informations cryptographiques ;
•	Mise en page : sections verticales claires (“Le Problème”, “La Solution”, “Fonctionnement”, “Portail de vérification”).
Chaque section est pensée pour correspondre à un moment précis de la démonstration orale :
•	“Le Problème” : chiffres et incidents officiels (KORAG, révocations) pour cadrer la crise.
•	“La Solution” : les trois acteurs (établissement, diplômé, recruteur) et la vision “Infalsifiable · Instantané · Universel”.
•	“Fonctionnement” : traduction pédagogique de SHA 256 et ECDSA en trois étapes numérotées (01/02/03).
•	“Portail” : interaction live avec le jury via le widget de vérification.

●	Widget de vérification : logique technique
Le cœur du site Phase 2 est le portail de vérification DiploChain, visible dans la section “Portail de Vérification”. Il expose une API utilisateur minimaliste, mais reflète la logique technique de vérification décrite dans la documentation générale.

Le widget se compose de :
•	Un champ de saisie pour l’identifiant unique de diplôme ;
•	Deux boutons de scénario :
•	“✓ Tester un vrai diplôme” (cas de succès) ;
•	“✗ Tester un faux diplôme” (cas de fraude) ;
•	Une zone de résultat affichant l’état courant (scan / succès / rejet) avec code couleur (or / vert / rouge).
Côté JavaScript, un objet DEMOS encode deux scénarios types :
•	Valid : identifiant DC-2024-BF-UO-028471, correspondant au diplôme fictif d’Aminata Ouédraogo, avec données complètes (nom, diplôme, établissement, date, mention, hash SHA 256 tronqué, signature ECDSA valide, établissement accrédité).
•	Invalid : identifiant DC-2024-XX-FAKE-99999, qui simule un diplôme inexistant ou falsifié (aucune correspondance dans la blockchain, signature invalide, hash absent).
Une fonction runVerify orchestre la séquence suivante :
1.	Lecture de l’entrée utilisateur (verifyInput) ou du scénario passé par loadDemo('valid'/'invalid').
2.	Affichage d’un état intermédiaire “Interrogation de la blockchain en cours…” avec animation de ligne de scan (simule la requête au réseau).
3.	Après un délai simulé (1,6 à 2,2 secondes), affichage du résultat final :
•	Succès (vert) avec toutes les métadonnées du diplôme ;
•	Échec (rouge) avec message “Diplôme invalide… Hash : aucune correspondance trouvée”.
Pour la saisie libre, une fonction pickDemo (input) applique une règle simple :
•	Si l’identifiant contient “FAKE” ou “FAUX” ou est trop court, on bascule sur le scénario invalid ;
•	Sinon, on bascule sur le scénario valid.
Cette logique n’est qu’une simulation UI mais reflète le comportement de la version finale : un identifiant douteux ou non trouvé doit être rejeté immédiatement.

●	 Correspondance avec le système réel
Le portail Phase 2 illustre, de manière simplifiée, le comportement du système complet décrit dans la documentation :
•	Dans la démo :
•	La “blockchain” est simulée par l’objet DEMOS ;
•	La vérification logicielle est remplacée par un choix de scénario valid/invalid côté front.
•	Dans la cible réelle :
•	L’identifiant saisi par le recruteur est envoyé à un service backend ;
•	Ce service interroge la blockchain, récupère les données du diplôme, recalcule le hash SHA 256, vérifie la signature ECDSA et le statut d’accréditation de l’établissement ;
•	Il renvoie au portail un statut binaire (authentique / invalide) accompagné des métadonnées du diplôme.
Ainsi, le site de la Phase 2 n’est pas une simple maquette graphique : il est une maquette fonctionnelle du portail public décrit dans les sections “Portail de vérification public”

●     Extensions et raffinements apportés en Phase 2
Sans changer la philosophie ni l’architecture générale ci dessus, plusieurs raffinements ont été ajoutés pour rendre le portail plus robuste, plus réaliste et plus professionnel.
1.	Gestion explicite du champ vide
Si l’utilisateur clique sur “Vérifier →” sans aucun identifiant saisi, le portail affiche désormais un message d’erreur rouge explicite (“AUCUN IDENTIFIANT SAISI Veuillez entrer un identifiant ou utiliser les scénarios de démo.”) au lieu de ne rien faire. Cela évite toute confusion lors d’une interaction spontanée d’un membre du jury.
2.	Accessibilité de la zone de résultat
La zone de résultat du portail est désormais marquée avec aria-live="polite", ce qui permet aux lecteurs d’écran d’annoncer automatiquement les changements d’état (scan en cours, diplôme authentique, diplôme invalide). Cette amélioration est invisible visuellement mais aligne le portail sur les bonnes pratiques d’accessibilité.
3.	Boutons de démo enrichis pour l’accessibilité
Les boutons “✓ Tester un vrai diplôme” et “✗ Tester un faux diplôme” exposent maintenant un rôle explicite de bouton via les attributs ARIA. L’interface reste identique pour l’utilisateur, mais la structure est plus claire pour les technologies d’assistance.
4.	Navigation mobile via menu hamburger
Sur mobile, la navigation principale est désormais accessible via un bouton hamburger (☰) qui ouvre et ferme le menu. Les liens de navigation se replient automatiquement après un clic, ce qui permet au jury de parcourir facilement le site sur smartphone sans être bloqué par le menu.
5.	Mention “données fictives”
Sous le widget de vérification, une note discrète précise que les noms, identifiants et diplômes présentés sont fictifs et utilisés uniquement à des fins de démonstration. Cette mention clarifie la nature des données et répond à d’éventuelles questions sur leur provenance.
6.	Base simulée étendue à six diplômés
Au lieu de ne simuler que deux scénarios (un vrai diplôme et un faux), la démo embarque désormais une base simulée de six diplômés burkinabè, chacun avec son identifiant DiploChain, son diplôme, son établissement, sa date et sa mention.
•	Tout identifiant appartenant à cette liste renvoie un résultat vert avec les métadonnées correspondantes.
•	Tout identifiant inconnu (y compris ceux contenant “FAKE”, “FAUX” ou des formats arbitraires) renvoie un résultat rouge immédiat.
Cette extension rend la démonstration plus riche : elle montre qu’un même portail peut gérer plusieurs diplômes réels, et pas seulement un cas “hardcodé” pour le show, tout en conservant la même logique de vérification binaire (authentique / invalide).

