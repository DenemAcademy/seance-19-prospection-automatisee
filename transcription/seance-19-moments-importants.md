# Séance 19 — Prospection automatisée Gmail

## Moments importants

- **00:00:00** — Objectif : construire une prospection automatisée Gmail à partir de la base de leads nettoyée.
- **00:01:36** — Point de départ : utiliser le fichier `leads-prospection-ready.xlsx`, pas une ancienne version.
- **00:02:05** — Lancer Codex dans le bon dossier puis auditer le XLSX avant toute automatisation.
- **00:04:18** — Préparer les trois briques : Gmail, OpenAI API et VPS Hostinger.
- **00:06:49** — L’audit produit un rapport, mais le vrai objectif est de construire le système.
- **00:07:24** — Créer et remplir une configuration privée locale : `env`, `deploy.env`, `.gitignore`.
- **00:08:54** — Créer un mot de passe d’application Gmail : si l’option est absente, activer la validation en deux étapes.
- **00:10:11** — Activer la double authentification, revenir sur App Passwords, nommer l’application.
- **00:12:30** — Récupérer les infos Hostinger : accès root, clé SSH, IP VPS, puis tout coller en vrac.
- **00:14:07** — Ajouter la clé OpenAI API et demander à Codex de remplir les variables sans afficher les secrets.
- **00:20:30** — Nettoyer la configuration : supprimer le fichier texte temporaire, garder un accès VPS réutilisable.
- **00:24:22** — Déployer la base et la configuration sur le VPS, sans exposer de secret.
- **00:25:27** — Construire le moteur Python : lecture XLSX, éligibilité, OpenAI, Gmail SMTP/IMAP, SQLite, relances.
- **00:35:06** — Tester avant production : un système qui dit “terminé” peut encore être mauvais.
- **00:36:23** — Premier email réel : techniquement envoyé, mais style trop générique.
- **00:43:56** — Correction demandée : emails plus courts, plus structurés, plus amicaux, sans phrase lourde.
- **00:50:43** — Deuxième version : on se rapproche du ton attendu mais on continue à brander le système.
- **00:58:17** — Idée importante : ne pas se satisfaire d’une automatisation fonctionnelle si les messages ne convertissent pas.
- **01:04:27** — Le système est planifié, testé, et prêt à tourner avec OpenAI, Gmail et VPS.
- **01:07:20** — Transition vers la suite : cas d’entreprise et proposition automatisée.

## Transcription nettoyée

### 00:00:00
Ok bon c'est parti on va attaquer du coup la séance 19 et là on va parler du coup d'un système automatisé qui va nous permettre de faire de la prospection et d'envoyer d'emails par jour. Donc à nos leads trouvés. On les a trouvés grâce à Google Maps, ensuite à Apify, ensuite on les a enrichis aussi grâce à Data.gouv et après on les a nettoyés. Donc là on va utiliser une adresse email qui va nous permettre de contacter directement ces personnes là sur leur adresse email de manière personnalisée.

### 00:00:33
Ca va pas être très complexe, encore une fois on va se baser sur un système en plusieurs étapes donc avec le .1, .2, .3. Donc ensuite on demandera à Codex de nous coder la solution pour qu'on puisse contacter tout ça en automatique. Donc encore une fois c'est vraiment l'objectif que j'ai envie de comprenner. C'est que vous avez une problématique, une idée, vous allez pouvoir créer un système automatisé si vous posez votre réflexion directement sur le LLM. Donc là Codex c'est pas bien complexe, ça va prendre une heure et ça vous pouvez le facturer à un client je sais pas peut-être 5-6 000 euros.

### 00:01:14
Ça dépend, ça va permettre bien plus si vous améliorez le système, vous connectez plusieurs adresses email. Après c'est toujours la même logique. Si vous avez réussi à créer ça, vous demandez à Codex d'ajouter une adresse email supplémentaire et faire partir 20 000 par jour à la place de 10, ça va les faire direct. Donc toute l'automatisation elle va partir de notre fichier. Donc le fichier c'est lequel ? C'est celui qu'on a nettoyé. Donc leads Prospection Ready. Normalement vous devez avoir le même si vous avez donné le petit prompt tout à l'heure hors de la séance 18.

### 00:01:52
Et on va du coup récupérer ses adresses email et ensuite contacter directement. Donc là le point 1 ça va être l'analyser du coup ce fichier leads Prospection Ready. Vous allez juste le copier. Donc d'abord il va falloir lancer une session. Pour lancer une session maintenant je pense que vous le savez. On va récupérer la commande Codex codex --dangerously-bypass-approvals-and-sandbox. Donc comme on l'a vu dans les précédentes séances, c'est vraiment le mode qui va nous permettre de le faire travailler en automatique

### 00:02:24
sans qu'on ait besoin à chaque fois de lui dire ok tu as le droit de faire ça. Donc là le but c'est de gagner du temps. On sait qu'il est bon ce modèle et qu'il a la capacité de tout faire sans erreur. Donc on va en profiter. Donc maintenant une fois que c'est fait on récupère le prompt. Alors que je ne me trompe pas c'est lui. Prompt technique.1 On colle et on le laisse travailler. En gros là ce qu'on va faire c'est simple. C'est qu'on va lui dire qu'il va analyser la base Excel leads Prospection Ready.

### 00:02:59
Ensuite la mission c'est qu'il doit vérifier que ce fichier est utilisé par un système d'eenvoi automatique Gmail. Tu ne dois envoyer aucun mail donc ça on lui donne bien l'information. La contrainte ne modifie pas l'XLSX. Ne supprime aucune ligne. Donc là on lui donne des données en fait c'est important à chaque fois qu'on a une nouvelle sélection et qu'on a une nouvelle session et qu'on a un nouveau projet. On lui donne une base. Il comprend et ça va nous permettre ensuite d'avoir un rendu bien plus qualitatif et éviter qu'on fasse la boucle qu'on a vu depuis le départ.

### 00:03:29
Donc point A on a une idée. Point B on lui demande de faire l'idée. Point C on regarde le résultat. Point D on lui demande des améliorations. Du coup on repart sur l'étape A. Le but ce n'est pas de faire ça. C'est vraiment d'aller faire deux allers-retours maximum et ensuite de continuer et de ne pas faire une boucle. On finit ça quoi. Donc là c'est pour ça que je lui donne le fichier parce que c'est une nouvelle session et on a besoin de lui donner du contexte. On a envie qu'il ait des informations et qu'ensuite il soit direct dans le bain.

### 00:04:18
Laissez travailler. Ensuite nous on aura besoin de travailler avec Gmail, OpenAI donc l'API et le VPS directement pour que ça tourne en automatique 24 sur 24. Donc voilà. Donc ça encore une fois c'est la structure que j'utilise tout le temps pour mes clients. Je me prends pas la tête, je lui me connexe, un projet, je lui achète un VPS, je lui achète un abonnement et ensuite je fous toute l'automatisation à l'intérieur. Je prends même plus N8N ou à part si il veut un Visu, j'utilise N8N et ensuite ça tourne.

### 00:04:50
Donc ça va être très rAPIde. Là encore une fois vous pouvez le revendre assez cher ce système là. Vous allez voir en moins de deux heures c'est fini c'est bouclé et vous allez pouvoir reproduire la même chose. Et après peu importe les projets que le client va vous demander vous êtes dans la capacité de le faire. En fait il vous faut encore une fois ce petit déblocage qu'on va voir lors de la séance 24 quand vous allez réaliser l’exercice qui est assez complexe. Vous allez la voir un petit peu mais dès que vous allez travailler sur vos premiers cas avec des vrais clients ça va se faire tout seul.

### 00:05:19
Y'a pas de limite. Y'a pas de limite et ça qui est top. Je vais voir où il en est.

### 00:05:38
Il laisse travailler.

### 00:06:08
Il est en train de générer le rapport MD. Ensuite, tu vas relire les sorties et conclure. Il est en train de générer les sorties et conclure. Il est en train de générer les sorties et conclure. Il nous a fait un rapport. On peut aller très bien l'analyser. Ce n'est pas ce qui va nous intéresser. Le but va être vraiment de créer le système. Pour créer le système, on va lui donner ce prompt en question. Le prompt numéro 2, on l'attaque directement. Après, on va lui renseigner dans le fichier qui va nous créer les infos.

### 00:07:08
Les infos. Il est en train de générer les sorties. Il est en train de générer les sorties. ok donc top là il va nous créer le fichier on a besoin donc ça on va tout coller en vrac après ensuite on lui dira de nous mettre ça en env pour récupérer du coup les informations qu'on a besoin on va voir Gmail je vous ai mis les petits liens ici on va besoin on va avoir besoin de créer un mot de passe d'application donc pour ça va falloir cliquer dessus ensuite renseigner l'adresse email que vous souhaitez où vous souhaitez justement que les emails partent moi

### 00:08:09
je vais utiliser celle-ci et ensuite mettez votre mot de passe Gmail donc on me demande des confirmation qu'est ce soir par sms tac que c'est quoi le petit code que j'ai reçu 10h23 8 3 13 35 ok donc c'est alors 8 3 13 ok c'est 35 36 ok je vais faire là il vous montrer que ça va être simple donc là vous allez pouvoir avoir ce message là mot de passe des applications de votre place mots de passe des applications le paramètre que vous recherchez n'est pas disponible pour votre compte c'est normal vous avez pas autorisé la double authentification

### 00:09:04
dès que vous allez autoriser la double authentification vous allez voir que vous allez pouvoir créer justement une app donc vous allez aller juste ici accéder à votre compte google cliquez dessus là déjà d'une part des sélectionnés la bonne adresse email que ça c'est un peu relou c'est un petit problème au niveau de google donc à chaque fois vous de changer le bon compte maintenant ce que vous faites c'est que vous allez aller dans sécurité et connexion

### 00:09:30
tac tac tac tac recommandations je sais plus où c'était mot de passe non c'est pas ici formation données confidentialité non c'est pas ici comptez partage

### 00:09:45
applications associées informations

### 00:09:49
alors le but c'est de trouver comment activer la double authentification euh sur assez sécurité connexion

### 00:10:01
ok je clique ici accident pas le refaire sinon je demanderai la codec directement validation en deux étapes voilà juste ici vous cliquez là vous cliquez sur activer la validation en deux étapes ça hop donc là vous avez protégé par la validation en deux étapes vous cliquez sur OK et là c'est donc là c'est activé maintenant ce que vous allez faire c'est que vous allez tout simplement recliquer sur créer un mot de passe d'application

### 00:10:31
alors quand j'ai cliqué ça m'a fait ça faut peut-être refermer la page application associée donc là on va refermer la page on va relancer

### 00:10:45
ok super donc c'est ça on se reconnaît que sur le compte on a accepté la double authentification donc là il va me redemander encore une fois ah non je pense qu'il s'est allé me redemander du coup un petit code donc là nom de l'appli vous mettez prospection euh ce que vous voulez dire je vais mettre moi

### 00:11:09
et là comme vous pouvez voir vous avez un mot de passe nous on va l'enseigner donc là codex nous a tout simplement euh fichier créer champ trouvé test donc là on va aller dans en finder 06 leads ENV prospection ready audit tac donc là si je vais dans ENV on fait euh text edit on peut voir qu'on a toutes les informations nous on va pas se prendre la tête on va créer ici euh présentation alors on va demander tout simplement crée moi un fichier txt please donc là on va lui demander qu'il nous crée un fichier txt

### 00:11:58
voilà donc là hop il nous a donné le fichier txt donc là je lui donne le nom de l'app donc là je mets non app ensuite je copie tout ça mot de passe application je me prends pas la tête encore une fois tac tac tac tac tac tac tac on va mettre adresse email app donc c'est celle ci hop et ensuite ce qu'on va faire ça peut être compliqué on va aller sur hostinger VPS et on va tout simplement encore une fois on a déjà utilisé notre VPS pour faire le blog automatique donc on va utiliser le même VPS pour éviter d'en racheter un

### 00:12:59
on va aller dans gérer juste ici on va récupérer l'access route prenez vous même pas la tête vous savez quoi vous recopiez tout ça

### 00:13:11
euh ensuite vous allez aller vu que c'est une clé euh générale on bloque les SSH juste ici tac tac tac affiché vous donnez votre clé directement sans faux copier tout on va demander d'organiser ça vu qu'il ne va pas l’afficher adresse IP ou copier aussi

### 00:13:37
donc là hop le fichier texte donc maintenant on lui dit tout simplement de lire le fichier texte texté texté et remplis les variables eenv stp voilà le but de secteur le transfert j'ai oublié de renseigner ma clé API OpenAI donc ce qu'on a à faire c'est qu'on va relancer le fichier juste ici OpenAI ici en peine à y 15 avec que vous allez sur l'OpenAI plateforme donc API case create vous mettez prospection automatique avec vous copier la clé API voilà vous prenez pas la tête on lui dira on dira après pour le modèle qu'on veut

### 00:15:14
que là il affiche pas des valeurs c'est top but c'est qu'on mette pendure directement dans codex donc là c'est fait je veux dire

### 00:15:44
que la joue vers les fichiers un v ouvrir texte dit

### 00:16:04
c'est bien tout c'est top qu'est ce que tu veux faire donc là ça va pas marcher je pense là le but c'est de comprendre moi je sais ce qui va manquer mais de comprendre pourquoi il va pas réussir à se connecter normalement il devrait pas pour la capacité je crois parce que des infos faites vous devez le challenge et constamment que ce que vous faites

### 00:17:29
on leur donne le fichier voilà ce qui manquait

### 00:17:59
on va demander de supprimer le texte et de mettre toutes les variables directement comme ce qu'on a fait je vois enfin vous voyez sur et env

### 00:20:29
Ok top. Ok. Maintenant, organise les fichiers env. Et ensuite, supprime le fichier txt stp. Je veux que tu puisses constamment te connecter au VPS sans me demander stopper. Voilà. Comme ça, on a une structure qui est bonne et ensuite, si on voudra modifier certains points, on pourrait lui dire, le but c'est d'avoir toujours accès à notre automatisation. Par exemple, vous avez un client qui veut changer quelque chose, vous lui proposez, je ne sais pas, 1800 euros pour ajouter des leads, voire plus.

### 00:21:19
Vous avez juste à faire ouvrir un terminal au dossier et ensuite vous ajoutez les leads et en 40 minutes, c'est terminé. Mais pas 30 minutes.

### 00:24:22
classé bon il a préparé du coup de VPS qui l'a fait chez aucun secret donc c'est top maintenant va pouvoir passer aux prompts techniques donc que des systèmes encore une fois je crois pas que j'ai mis une modèle sur OpenAI donc là je peux très bien lui donner le prompt on va l'analyser les temps qu'il fasse ça utilise le modèle gpt moi c'est mon modèle préféré parce qu'il est pas cher et il est assez performant on va lire tout ça 4 elle vous montrez comment personnaliser vraiment votre système selon vos envies donc va copier faire comme d'habitude google doc

### 00:25:11
hop hop hop hop hop on va grandir un petit peu le texte donc là je demande de créer un moteur de prospection qui lisent du coup le fichier XLSX donc tac tac tac celui là choisi uniquement les prospects éligibles demande à OpenAI de rédiger un nouveau mail eenvoie réellement via Gmail SMTP lis les réponses via stop automatiquement les relance les réponses booms ou output limite les sorties à 10 emails maximum par jour écrit tous les tracking XLSX exploitable règle absolue sur les messages ne recopie jamais le

### 00:25:58
message disponible dans le XLSX ne prend jamais parce qu'en gros on veut vraiment du personnaliser que lAPI réfléchit selon la ligne qu'on a rédigé par exemple alors je vais ouvrir c'est un gros fichier ça va prendre du temps je pense que relou à tel que si tu es sur sheet taille c'est plus j'ai l'ouvert sur quelle fait de quelques cent si tu crois voilà superbe olg for mail si on récupère la ligne tac tac tac à outreur du message bonjour j'ai vu mal beau rannis question rAPIde combien voilà on a un message très simple une c'est qu'on est quand même

### 00:26:43
du personnalisé qu'on n'est pas toujours le même que la structure elle change parce que c'est toujours pareil mais c'est toujours de contexte à lAPI pour qu'il puisse vraiment personnaliser un maximum alors il a assez d'informations pour créer vraiment j'ai d'intéressant même au niveau de la relance au message de cas ça récupère vraiment les informations qu'on avait créé

### 00:27:13
voilà un peu là l'objectif qu'on a une infrage de créer le Python on va justement le challenge et ça qui va être intéressant sur cette séance là c'est de vous faire comprendre que voilà on perd un sujet donc là vous avez toute la base mais l'objectif c'est pas que vous reproduisez exactement la même chose que vous voyez lors de toutes les séances parce que sinon on sera pas assez personnalisé par rapport à lui c'est vraiment d'avoir une bonne réflexion de faire les modifications ensuite de comprendre de manière générale qui peut vraiment tout faire

### 00:27:53
je mets il je suis prémé tout ça on laisse travailler ça prend un petit peu de temps je pense on va créer le code Python et les tests en secret synchroniser les projets sous le VPS ça prend un petit peu de time on va le laisser faire un peu de temps

### 00:33:06
On a une petite montée là, c'est sûr. Pas été encore mis à jour.

### 00:34:06
Ok, donc c'est vrai.

### 00:34:17
Ok. Donc là, on lui fait.

### 00:34:28
Donc là, on a une autre adresse email. Normalement, ça devrait partir juste ici.

### 00:35:06
c'est important de voir justement la réaction du système voir si tout fonctionne avant de le mettre en production et que ça tombe en automatique

### 00:35:36
donc là il a remarqué qu'il y avait une première match au niveau du garde-fou pour bloquer seulement le mot isolé donc là il va relancer l'eenvoi c'est très important de faire ça parce que vous dire ouais voilà il m'a dit que c'était terminé au final en fait on va trouver l’automatisation va pas fonctionner donc là c'est bon on a envoyé le mail à info le mail.fr bonjour je me permets de vous écrire au sujet de la privatisation et des groupes j'ai de la privatisation et des groupes ça va rien dire c'est pas terrible comme message là encore

### 00:36:39
une fois l'API n'est pas bien connecté ça veut dire que derrière le message de manière personnalisée a pas été compris donc là on va lui donner le message un autre

### 00:40:36
Ok, donc là, il a fini. Tac. Donc là.

### 00:41:47
Et clavier, on va envoyer un test. On va aller voir justement le résultat.

### 00:41:52
On attend un petit peu.

### 00:41:58
en plus c'est d'avoir un email parfait ensuite de confirmer que voilà c'est ce système lAPIn ce style email qu'on souhaite pour les autres leads pour le système de relance que l'on a bien resté maintenant que ça ne fait pas encore

### 00:42:59
donc là je leur donne un prompt parce que là c'est pas assez personnalisé j'aime pas trop la but c'est de lui faire comprendre encore une fois c'est des fausses d'ajustement c'est pas grave si vous le faites ou passer 10 15 minutes plus important c'est vraiment avoir un système qui tourne qui va être fonctionné qui va nous ramener du guide ce qui va être pertinent qui va impacter la personne la merde ça il ya simple pourquoi les demandes de groupe ça me plaît pas tu vois qu'il aura fait ça on va faire on va passer au test sur le troisième lit

### 00:44:34
ça peut le challenger de lui faire comprendre ce que vous souhaitez comment vous voulez faire perdre recevoir votre produit comment voulez vous vendre suite il va être dans la capacité de le créer c'est vous le boss c'est vous qui dirigez il ya et qui arrive à votre objectif principal je sais comment bien vendre en eenvoyant des mails ne laissez pas avec ce genre de message c'est pas top là il arrive toute personne tu vas nous répondre elle eenvoie un mail là il enrichit le système de prompting top

### 00:45:51
fait le but cette crème vraie pipeline qui cartonne on va pas lui laisser d'air il va vraiment prendre un rendez vous on va lui rajouter aussi notre calendrier à la limite ça dépend de votre stratégie soit vous mettez un premier contact simple ou sans bien qu'à l'envier soit vous en mettez un ça dépend vraiment de ce que vous souhaitez faire que vous pouvez faire

### 00:46:54
on laisse pas le temps il finisse à attaque on lui eenvoie on veut faire un test on veut tester voir si ça fonctionne mais il déjà envoyé 2

### 00:50:43
Là il y a encore des petits trucs à améliorer, mais on se rapproche un petit peu de quelque chose que j'aime.

### 00:50:54
Donc là je lui eenvoie un message d'email, enfin j'eenvoie le screen d'email.

### 00:51:24
Voilà, le but c'est vraiment de le brander, après on aura quelque chose de solide pour contacter tous nos prochains leads. Après ça va tourner en boucle tous les jours, donc ça sera top. Ça sera top.

### 00:58:17
ou je ne sais pas, si vous n'avez pas une bonne forme d'excel, vous êtes bloqué. Là, non. Là, vous avez la capacité de tout faire. Je sais que je me répète beaucoup, mais c'est super important. Dès que vous allez avoir ce déblocage-là, ça va être trop simple pour vous et vous allez tout exploser sur le marché.

### 00:58:32
Ah oui, j'aurais pu me satisfaire d'une automatisation top-top comme ça avec des messages qui ne vont pas convertir. Ce n'est pas ce que je veux. Je vais améliorer. Le système était bien fonctionnel, ça marchait bien, mais il manquait le plus important.

### 00:59:50
Réfléchis super bien ce modèle. Honnêtement, c'est impressionnant.

### 01:00:32
Ok, même système.

### 01:04:27
ok donc là maintenant il va planifier tout ça et ensuite ça va tourner et ça sera fonctionné ça va pas pris beaucoup de temps ça va prendre le temps que le système qualitatif ça fonctionne bien c'est top on fait les tests mais il s'en voit bien et partie sécurité l'acte à une API donc OpenAI c'est cool encore une fois ça vous peut les facturer cher

### 01:07:20
On va passer maintenant sur des choses un peu plus pratiques par rapport à des cas d'entreprise. Donc on va aller générer un petit SEO pour une boîte qui nous fait une proposition sur l'automatisation qu'elle souhaite. Donc je vous remercie et on se retrouve tout de suite pour la prochaine séance. A toute !
