Domain-Driven Design (DDD) : Concepts et Implémentation en TypeScript
1. Introduction au DDD
Le Domain-Driven Design (DDD), ou "conception pilotée par le domaine", est une approche de développement logiciel qui place le domaine métier au cœur de la conception. Son principe fondamental est de construire un modèle logiciel étroitement aligné sur les règles et processus du domaine étudié​
MARTINFOWLER.COM
. En pratique, cela signifie que l’on centre le développement sur un modèle de domaine riche qui reflète fidèlement la logique métier. Cette approche a été formalisée par Eric Evans dans son livre de 2003 Domain-Driven Design: Tackling Complexity in the Heart of Software, et une communauté de praticiens a depuis continué à la faire évoluer​
MARTINFOWLER.COM
.Un concept clé du DDD est l’établissement d’un langage omniprésent (Ubiquitous Language) partagé entre les développeurs et les experts métier. Ce langage commun, constitué des termes et notions du domaine, est utilisé dans les discussions et dans le code afin de réduire les ambiguïtés et les pertes d’information​
MARTINFOWLER.COM
. Contrairement aux méthodes où les modèles métier restent théoriques ou isolés, le DDD encourage à implémenter et à faire évoluer le modèle de domaine directement dans le logiciel, en collaboration continue avec les experts métier, tout au long du cycle de vie du projet​
MARTINFOWLER.COM
.On distingue dans DDD la conception stratégique (vision d’ensemble du système, découpage du domaine en sous-domaines, contextes délimités, langage commun, etc.) et la conception tactique (implémentation concrète à l’aide de patterns comme entités, objets valeur, agrégats, services, etc.). DDD est particulièrement indiqué pour les systèmes complexes où la logique métier est abondante et intriquée : il aide à organiser et maîtriser cette complexité en la reflétant dans une conception cohérente​
MARTINFOWLER.COM
. En revanche, pour des applications très simples (de type CRUD basique), le poids d’une telle architecture peut ne pas se justifier.Avantages du DDD : En adoptant DDD, on vise un code plus expressif et aligné sur les besoins métier, ce qui facilite la communication et la maintenabilité. Un modèle de domaine bien pensé sert de documentation vivante du système et s’adapte aux évolutions du métier. Le langage partagé réduit les incompréhensions entre l’informatique et le métier. De plus, DDD tend à produire une architecture modulaire (via les Bounded Contexts) facilitant la scalabilité de l’application. Bien appliqué, DDD améliore la maintenabilité, la flexibilité du code et la collaboration entre experts métier et développeurs en alignant la logique logicielle sur la logique métier​
CONCETTOLABS.COM
. Enfin, en isolant la logique métier du reste (infrastructure technique, interface...), on obtient des composants facilement testables et réutilisables.
2. Les Concepts Clés du DDD
Voici un tour d’horizon des principaux concepts du Domain-Driven Design, qui constituent la base de la conception d’un modèle de domaine riche.
Bounded Context (Contexte Délimité)
Un Bounded Context est une frontière explicite à l’intérieur de laquelle un modèle de domaine s’applique avec une certaine cohérence. C’est un élément central de la conception stratégique en DDD​
MARTINFOWLER.COM
. Lorsque le domaine global est vaste ou complexe, on le divise en plusieurs contextes délimités, chacun correspondant à un sous-domaine où un même terme n’a qu’une seule signification et où le modèle est unifié. DDD reconnaît en effet que vouloir unifier entièrement le modèle d’un grand système n’est ni faisable ni efficace, et qu’il est préférable de le découper en contextes cohérents plus petits​
MARTINFOWLER.COM
.Chaque contexte délimité possède son propre modèle et son propre langage omniprésent. Les interactions entre contextes sont gérées par des mécanismes d’intégration (mapping, traduction…) explicitement définis. Cela permet, par exemple, qu’un même concept (comme “Client” ou “Produit”) puisse avoir des attributs ou comportements différents selon le contexte, sans collision de sens. Exemple : dans un contexte “Vente”, une Commande peut représenter un achat client, tandis que dans un contexte “Logistique”, une Commande peut désigner un ordre d’approvisionnement – les deux concepts portent le même nom mais vivent dans des contextes séparés. Le Bounded Context garantit l’autonomie sémantique de chaque partie du système (un contexte = un vocabulaire propre, un modèle cohérent).
Aggregate (Agrégat)
Un Agrégat est un groupe cohérent d’objets du domaine traités comme une unité unique du point de vue des modifications de données​
MARTINFOWLER.COM
. Autrement dit, c’est un ensemble d’entités et/ou d’objets valeur étroitement liés, considéré comme un tout pour assurer la consistance des règles métier. Il y a toujours une racine d’agrégat (Aggregate Root), qui est une entité principale servant de point d’entrée de l’agrégat. Toute interaction externe avec l’agrégat doit passer par cette racine, garantissant ainsi l’intégrité interne du groupe​
MARTINFOWLER.COM
.Exemple : une commande client peut être modélisée comme un agrégat comprenant l’entité Commande et une collection de Lignes de commande. La Commande est la racine d’agrégat ; les lignes (objets contenant par exemple un produit et une quantité) lui sont rattachées. On manipule et persiste l’ensemble Commande + lignes comme une unité indivisible​
MARTINFOWLER.COM
. Par conséquent, les invariants métier qui concernent la commande (par ex. “une commande ne peut pas avoir deux fois la même référence de produit” ou “le total doit être la somme des lignes”) seront vérifiés par la racine en englobant ses lignes.Un agrégat définit également un contexte de cohérence transactionnelle : on évite les transactions qui modifient plusieurs agrégats à la fois. En base de données, cela se traduit par le fait de sauvegarder ou charger un agrégat en entier, en une seule opération, plutôt que de manipuler partiellement ses composants isolément​
MARTINFOWLER.COM
. Cela contribue à maintenir l’intégrité des données. Pour reprendre l’exemple, on ne sauvegardera pas une ligne de commande sans sa commande parente, on sauvegardera toujours la commande et toutes ses lignes ensemble.
Entity (Entité)
Une Entité est un objet du domaine doté d’une identité qui lui est propre et qui perdure dans le temps, indépendamment de ses attributs. Deux entités avec des attributs identiques sont néanmoins distinctes si leur identité est différente. L’unicité d’une entité ne repose donc pas sur ses données, mais sur un identifiant (unique dans son contexte) qui permet de la distinguer​
DEV.TO
. Par exemple, deux clients peuvent avoir le même nom et habiter à la même adresse, ils seront malgré tout représentés par deux entités différentes de type Client, chacune ayant son propre identifiant unique pour les différencier​
DEV.TO
. De même, il peut exister des milliers de voitures Toyota Corolla identiques en tout point, seule un identifiant (par ex. un numéro de série VIN) permet de les distinguer en tant qu’entités distinctes​
DEV.TO
.L’identité d’une entité est généralement immuable et se définit soit par un identifiant explicitement manipulé (code interne, UUID, etc.), soit par un composé de propriétés unique. Les entités représentent souvent les choses principales du domaine (p. ex. Utilisateur, Commande, Produit, etc.) et portent la majeure partie de la logique métier. On s’efforce de regrouper dans les entités le comportement associé à ces objets – par exemple, les méthodes qui modifient l’état d’une commande ou qui appliquent des règles de gestion propres à un utilisateur.
Note : À la conception, déterminer si un concept doit être modélisé comme une entité ou comme un objet valeur dépend du besoin de distinction. On se pose la question : “A-t-on besoin de distinguer cet objet parmi d’autres du même type ?” Si oui, c’est une entité. Si non (si seules ses caractéristiques comptent), c’est un objet valeur​
DEV.TO
.
Une entité peut référencer ou contenir des Value Objects (objets valeur) pour certains de ses attributs. Par exemple, une entité Commande peut avoir une propriété de type Montant (objet valeur encapsulant une devise et une valeur), ou une entité Personne peut avoir un objet valeur Adresse. Les objets valeur étant interchangeables par définition, ils peuvent être partagés entre entités ou recréés à l’identique sans problème. Pour garantir cela, les objets valeur sont conçus comme immuables : leur état ne change pas une fois créés​
DEV.TO
. Si une propriété doit changer, on crée un nouvel objet valeur. Cette immuabilité évite des effets de bord quand un même objet valeur est utilisé à plusieurs endroits et assure qu’une entité ne soit pas impactée par la modification involontaire d’un objet valeur qu’elle utilise.
Value Object (Objet Valeur)
Un Objet Valeur est un objet qui modélise une notion du domaine uniquement par ses attributs, sans identité propre. Deux objets valeur contenant les mêmes données sont considérés comme égaux dans le contexte du domaine. Ce sont souvent de petites classes qui regroupent des propriétés intrinsèquement liées, avec éventuellement de la logique pour garantir leur validité ou effectuer des calculs, mais on ne cherche pas à les identifier de manière unique. Ils décrivent des caractéristiques ou des mesures.Exemples d’objets valeur : une monnaie (avec un code devise et éventuellement un taux de conversion), un intervalle de dates, une adresse postale, un nom de personne, une coordonnée géographique. Ces éléments n’ont pas d’identité métier unique – deux adresses identiques sont interchangeables, de même que deux montants de 50 EUR – seule leur valeur importe.​
DEV.TO
.Les objets valeur doivent être immutables et conceptuellement entiers. Immutables, car si l’on modifie la valeur, on préfère créer un nouvel objet (ainsi, si un même objet valeur est référencé à deux endroits et qu’on le change, cela pourrait sinon changer deux entités à la fois – ce qui est non désiré). Conceptuellement entiers, c’est-à-dire que leurs propriétés forment un tout significatif ; un objet valeur représente une notion complète dans le domaine​
DEV.TO
. Par exemple, un objet valeur NomComplet pourrait combiner un prénom et un nom, et garantir que ces champs ne sont pas vides.Résumé : les Entities se distinguent par leur identité unique, alors que les Value Objects sont définis uniquement par la valeur de leurs attributs et n’ont pas d’identité persistante​
DEV.TO
. On utilise les objets valeur pour modéliser des concepts où l’égalité sémantique est l’égalité des données, et on les garde immuables pour qu’ils puissent être librement partagés ou recréés sans ambiguïté.
Repository (Référentiel)
Un Repository est un objet (souvent une interface) qui assure la médiation entre le domaine et la couche de stockage des données. Il simule pour le domaine une collection d’objets en mémoire, alors qu’en réalité les données peuvent provenir d’une base de données, d’un service externe, etc. L’objectif principal d’un repository est de cacher la complexité de la persistence et de fournir des méthodes simples pour obtenir ou modifier les agrégats du domaine​
HIBIT.DEV
. Ainsi, du point de vue du code métier, interagir avec un repository ressemble à manipuler une liste ou un ensemble d’objets en mémoire​
HIBIT.DEV
.Dans la couche domaine, on définit généralement un repository par une interface (par ex. OrderRepository avec des méthodes comme save(order) ou findById(id)). L’implémentation concrète de cette interface sera fournie dans la couche infrastructure (par ex. SQLOrderRepository qui va exécuter de vraies opérations SQL). Cette séparation permet de changer facilement la technologie de stockage sans impacter le domaine : par exemple, remplacer une base SQL par une base NoSQL ou un service web, en écrivant une nouvelle implémentation du repository, tout en conservant la même interface​
BLOG.LOGROCKET.COM
. Le domaine n’”sait” pas comment les objets sont stockés, seulement comment les obtenir via le repository.Une bonne pratique est que chaque agrégat racine ait son repository dédié. Le repository gère les opérations CRUD au niveau de l’agrégat complet. Par exemple, un CustomerRepository fournira des méthodes pour ajouter, mettre à jour, supprimer ou rechercher des clients (entité Customer), un OrderRepository fera de même pour les commandes (agrégat Order). En interne, l’implémentation du repository peut s’appuyer sur un ORM, des requêtes SQL, des appels HTTP, etc., mais ces détails ne fuient pas dans le domaine.En résumé, les repositories découplent la logique métier de la source des données. Le développeur qui écrit la logique d’application n’a pas besoin de connaître les détails d’accès à la BD ; il interagit avec un repository comme avec une simple collection d’objets​
HIBIT.DEV
. Le repository se charge en coulisse de traduire ces opérations en requêtes ou en appels nécessaires (ex : requête SQL, appel REST, lecture d’un fichier…).
Domain Event (Événement de Domaine)
Un événement de domaine représente un fait significatif du point de vue métier qui s’est produit dans le système. C’est une notification émise après qu’une action métier a eu lieu, et qui peut intéresser d’autres parties du système​
HIBIT.DEV
. Les Domain Events permettent de déclencher des réactions découplées suite aux changements d’état du domaine, et de faire communiquer le domaine avec l’extérieur de manière propre.Concrètement, un événement de domaine est généralement un objet portant le nom de ce qui est arrivé (souvent formulé au passé). Par exemple : OrderPlaced (commande passée), ProductBackOrdered (produit en rupture commandé), PaymentReceived (paiement reçu), etc. Il contient éventuellement des informations sur ce qui s’est passé (identifiants, timestamp, détails pertinents). Dans DDD, on publie l’événement dès que l’action correspondante est confirmée dans le domaine. Par exemple, lorsque la commande passe de l’état "Panier" à "Confirmée", on pourrait créer et émettre un événement OrderPlaced contenant l’ID de la commande et le montant total.D’autres parties du système s’abonnent aux événements de domaine pour réagir : dans notre exemple, un listener pourrait, à la réception d’un OrderPlaced, lancer le processus de facturation ou de préparation d’expédition. L’intérêt est de découpler l’émetteur de l’événement (le domaine qui constate le fait) et les traitements réalisés en réaction (qui peuvent être dans d’autres couches ou contextes). Un événement de domaine peut aussi servir à notifier un autre Bounded Context (via une file de messages, par exemple).Il est important de nommer ces événements de manière explicite au passé (car ils décrivent quelque chose d’achevé)​
HIBIT.DEV
. Par convention, on utilise un verbe au passé ou un nom d’action passé. Par ex. UtilisateurInscrit (et non InscrireUtilisateur qui serait une commande).En résumé, les Domain Events participent à rendre le domaine réactif : au lieu de coupler fortement les modules (ex: appeler directement le module de stock depuis le module commande), la commande émet un événement “CommandeValidée”, et le module de stock, en écoutant cet événement, peut réagir en débitant le stock correspondant. Cela facilite l’extensibilité (on peut ajouter de nouvelles réactions à un événement sans modifier le code du domaine émetteur) et l’intégration avec des systèmes externes.
Application Service (Service Applicatif)
Un Service Applicatif représente un cas d’utilisation de l’application au niveau logique. C’est un orchestration de haut niveau qui coordonne les opérations du domaine pour réaliser une action métier complète du point de vue de l’utilisateur ou d’un processus. On le situe dans la couche application (au-dessus du domaine).Concrètement, un service applicatif est souvent implémenté par une classe ou un ensemble de fonctions qui n’appartiennent pas au domaine, mais qui appellent le domaine. Il peut par exemple gérer une transaction, faire appel à plusieurs entités/répositories, et déclencher des événements ou des actions externes. Il ne contient pas en lui-même de règles métier complexes (celles-ci résident dans le domaine), mais il orchestre les appels aux objets du domaine dans le bon ordre pour accomplir une tâche.On peut voir les services applicatifs comme l’API de notre domaine : ce sont les points d’entrée pour l’interface utilisateur ou les couches externes, pour réaliser des opérations complètes. Exemple : un service applicatif OrderService pourrait avoir une méthode placeOrder(commandData) qui va : valider les données d’entrée, créer une nouvelle commande (entité Order), via le domaine ajouter les items, calculer le total, marquer la commande comme confirmée, sauvegarder la commande via le repository, puis émettre un événement de domaine OrderPlaced. Cette méthode unique réalise ainsi le cas d’utilisation “Passer une commande” du point de vue applicatif.Dans DDD, on préfère d’abord mettre la logique dans les Entités/ValObjs du domaine (là où elle “vit” naturellement), et n’utiliser un service (de domaine ou d’application) que quand cette logique ne peut appartenir à un objet en particulier​
STACKOVERFLOW.COM
. Un service applicatif ne fait donc souvent que déléguer aux entités/domain services et coordonner.Domain Service vs Application Service : À ne pas confondre avec les Services de Domaine, qui eux font partie du domaine. Un domain service encapsule une logique métier pure qui n’est pas naturellement portée par une entité ou un objet valeur. Par exemple, une calculatrice de frais de port pourrait être un service de domaine (si le calcul implique plusieurs entités ou des constantes métier globales). Ce service de domaine serait appelé par le service applicatif ou par une entité afin de connaître le résultat. Le service applicatif, lui, est plutôt orienté cas d’usage, peut appeler plusieurs opérations de domaine et gère éventuellement des aspects techniques (transactions, sécurité, appels à d’autres contextes). En résumé, “L’application service est spécifique à un cas d’usage de l’application, tandis qu’un domain service représente une capacité métier réutilisable dans le modèle.”​
NIMMNEUN.COM
​
NIMMNEUN.COM
.
Anti-Corruption Layer (Couche Anti-Corruption)
La Couche Anti-Corruption (ACL) est un pattern de DDD (stratégique) visant à protéger le modèle du domaine des influences externes indésirables. Elle s’applique lorsqu’on doit intégrer notre système avec un autre système ou sous-domaine aux modèles et sémantiques différents (par exemple, un ancien système legacy, ou un service tiers)​
LEARN.MICROSOFT.COM
. L’idée est d’intercaler une couche de traduction/adaptation entre notre domaine et l’autre système, de façon que chacun puisse évoluer ou fonctionner sans corrompre l’intégrité de l’autre.Concrètement, l’ACL consiste à créer des adaptateurs ou façades qui convertissent les données et appels d’un modèle à l’autre​
LEARN.MICROSOFT.COM
. Par exemple, si notre domaine manipule des objets Commande avec certaines propriétés, et qu’on doit appeler un service externe dont l’API attend un format différent (champres nommés autrement, autres unités, etc.), on ne va pas disperser ces conversions partout dans le code du domaine. À la place, on implémente un composant de la couche anti-corruption, qui, d’un côté, expose une interface dans le langage du domaine (ex: une méthode envoyerCommande(command: Order)), et de l’autre, traduit cette commande en appel REST ou en DTO compréhensible par le système externe.Cette couche fait donc office de bouclier : elle empêche les concepts étrangers de fuiter dans notre domaine. Notre code métier reste propre, exprimé dans son langage, et l’ACL se charge de toutes les conversions nécessaires pour communiquer avec l’extérieur​
LEARN.MICROSOFT.COM
. Ainsi, on évite que la “mauvaise qualité” ou la complexité d’un système tiers ne vienne compliquer notre propre modèle. Ce pattern est fréquent lors de migrations (par ex. un nouveau système interagit encore avec l’ancien pendant une période) ou d’intégrations de logiciels tiers. L’ACL peut être implémentée comme une bibliothèque d’adaptateurs, ou même comme un microservice à part faisant office de traducteur.En somme, la couche anti-corruption ajoute une complexité (du code de mapping à écrire) mais protège la cohérence et la longévité du domaine en isolant les interactions externes. On gagne en souplesse : si l’API externe change, on n’aura qu’à adapter l’ACL, sans toucher au cœur du domaine. Et réciproquement, on peut faire évoluer notre modèle interne librement tant que l’ACL continue à fournir les données attendues à l’externe.
3. Mise en Œuvre en TypeScript
Voyons maintenant comment mettre en pratique ces concepts en TypeScript. Cette section aborde la structuration d’un projet DDD, illustre un domaine métier par du code (classes et interfaces TypeScript), et explique la gestion concrète des repositories, des événements de domaine, ainsi que l’intégration avec l’infrastructure (base de données, API externes…).
Structuration d’un projet TypeScript avec DDD
L’implémentation de DDD en TypeScript s’appuie sur une architecture en couches bien séparées. On distingue généralement :
Couche Domaine – le cœur métier : contient les entités, objets valeur, agrégats, événements de domaine, interfaces de repositories et éventuellement services de domaine. Aucune dépendance externe ne doit figurer ici (pas de code d’accès BD, pas d’HTTP, etc.). Cette couche représente la logique pure du domaine.
Couche Application – les services applicatifs : contient les opérations de haut niveau correspondant aux cas d’utilisation. On y orchestre les appels au domaine et aux infrastructures (ex : démarrer une transaction, manipuler les entités via leurs méthodes, appeler les repositories, publier des événements). On peut y définir des DTOs (objets de transfert) ou des modèles de requête/réponse pour l’interface. C’est cette couche qui est appelée par l’interface utilisateur ou les API externes.
Couche Infrastructure – la technique : contient les implémentations concrètes des interfaces du domaine (repositories, services externes…), le code d’accès aux bases de données, la communication avec d’autres systèmes, les détails des frameworks, etc. Elle dépend des couches supérieures (par ex, implémente les interfaces définies dans le domaine), mais en aucun cas le contraire.
Couche Présentation (Interface Utilisateur) – optionnelle dans le cadre de la discussion DDD, c’est la couche la plus extérieure : interface utilisateur (ex. composants front, CLI, API REST) qui reçoit les actions de l’utilisateur et déclenche les services applicatifs en retour. Dans une application Node.js, cela correspond aux contrôleurs web/REST, aux adaptateurs d’UI.
En TypeScript, on peut organiser le code par couches horizontales (un dossier pour le domaine, un pour l’infrastructure, etc.), mais une approche souvent plus efficace est de le structurer par modules de domaine (contextes délimités) afin de regrouper tout ce qui concerne un même sous-domaine fonctionnel. Par exemple, on peut avoir un module commande séparé du module catalogue : chacun contient son domaine, son application, son infrastructure, sans mélange avec l’autre. Cela améliore la cohésion et la lisibilité, surtout sur les gros projets. Au sein de chaque module, on retrouve les trois couches principales mentionnées ci-dessus (domaine, application, infrastructure)​
GITHUB.COM
.Exemple d’arborescence d’un projet DDD en TypeScript, structuré par modules de domaine :
perl
Copier
Modifier
src/
├── commandes/
│   ├── domaine/        // entités, objets valeur, agrégats, events, interfaces de repo
│   ├── application/    // services applicatifs, use cases, gestion des événements
│   └── infrastructure/ // implémentations concrètes (ex: OrderRepositorySQL, adaptateurs externes)
├── catalogue/
│   ├── domaine/
│   ├── application/
│   └── infrastructure/
└── (autres contextes…)
Dans cette organisation, chaque module (bounded context) est relativement autonome. Par exemple, le dossier commandes/domaine pourrait définir une interface OrderRepository, et le fichier commandes/infrastructure/OrderRepositorySQL.ts en fournir l’implémentation avec une base de données relationnelle. La couche application commandes/application contiendrait le service applicatif PasserCommande qui orchestre le processus en faisant appel aux entités du domaine et au repository.Une telle séparation présente plusieurs avantages pratiques :
Isolation du domaine : on peut coder et tester le domaine sans avoir l’infrastructure (en remplaçant les implémentations de repository par des versions en mémoire pour les tests, par exemple).
Évolutivité : si on décide de changer de base de données ou de système de messaging, il suffit d’écrire de nouvelles implémentations dans la couche infrastructure, sans toucher à la logique métier.
Clarté architecturale : les dépendances sont contrôlées, la présentation appelle l’application, qui appelle le domaine, qui définit des contrats implémentés par l’infrastructure – et pas l’inverse. Cela correspond au principe de l’architecture hexagonale (Ports and Adapters) où le domaine et application définissent des ports (interfaces) que l’infrastructure vient brancher avec des adapters concrets.
Enfin, gardez à l’esprit que cette structure doit être adaptée à la taille du projet. Sur un petit projet, on peut grouper certaines couches par simplicité, tandis que sur un grand projet, on gagnera à bien séparer par contextes et couches. DDD n’impose pas une hiérarchie fixe de dossiers, l’important est de respecter l’intention : isoler la logique métier pure des détails techniques. (Par analogie avec Scrum, on adapte les pratiques à son contexte ; de même, certaines notions DDD ne prennent tout leur sens que dans des applications très complexes et vastes​
DEV.TO
.)
Exemple d’implémentation d’un domaine métier (avec classes et interfaces)
Pour illustrer la mise en œuvre concrète, prenons un exemple de domaine simplifié : le domaine des commandes client d’un site e-commerce. Nous définirons une entité Order (Commande) en tant qu’agrégat racine, contenant des objets valeur OrderItem (ligne de commande avec un produit et une quantité). Nous verrons aussi le repository associé et un événement de domaine déclenché à la finalisation de la commande. Enfin, on montrera un service applicatif pour orchestrer le cas d’utilisation “passer une commande”.Dans le dossier commandes/domaine, on peut avoir les classes et interfaces suivantes :
typescript
Copier
Modifier
// Objet Valeur représentant une ligne d'article dans une commande
class OrderItem {
  constructor(
    public productId: string,
    public quantity: number,
    public price: number
  ) {
    if (quantity <= 0) {
      throw new Error("La quantité doit être positive");
    }
  }

  getTotal(): number {
    return this.quantity * this.price;
  }
}

// Entité Commande (Aggregate Root de l'agrégat Order)
class Order {
  private items: OrderItem[] = [];
  public status: "en_attente" | "finalisée" = "en_attente";

  constructor(public id: string, public customerId: string) {}

  addItem(productId: string, price: number, quantity: number): void {
    // Ajoute une ligne de produit à la commande
    this.items.push(new OrderItem(productId, quantity, price));
  }

  get totalAmount(): number {
    // Calcule le total de la commande en sommant les totaux de chaque ligne
    return this.items.reduce((sum, item) => sum + item.getTotal(), 0);
  }

  completeOrder(): void {
    // Règle métier : on ne peut finaliser que si au moins un item
    if (this.items.length === 0) {
      throw new Error("Impossible de finaliser une commande sans articles");
    }
    this.status = "finalisée";
    // Ici on pourrait émettre un événement de domaine OrderPlaced, par ex.
  }
}

// Interface du Repository pour l'agrégat Order
interface OrderRepository {
  save(order: Order): Promise<void>;
  findById(id: string): Promise<Order | null>;
}

// Événement de domaine émis lorsqu'une commande est finalisée
class OrderPlacedEvent {
  constructor(
    public orderId: string,
    public total: number,
    public occurredOn: Date = new Date()
  ) {}
}
Explications :
OrderItem est un objet valeur qui représente une ligne de commande. Il contient un productId, un prix unitaire et une quantité. Dans son constructeur, on applique une invariant métier simple : la quantité doit être positive (on lance une erreur sinon). Il expose une méthode getTotal() pour calculer le total de la ligne (prix × quantité). Comme c’est un objet valeur, on ne lui définit pas d’identifiant unique ; deux OrderItem avec mêmes productId, price, quantity seraient considérés équivalents dans le domaine.
Order est l’entité racine de l’agrégat Commande. Elle possède un identifiant id (qu’on peut générer comme un UUID par exemple, lors de la création) et une référence vers le client (customerId) passant la commande. Elle maintient en privé la liste des OrderItem qui lui sont associés. On a des méthodes métiers : addItem pour ajouter une nouvelle ligne à la commande (en créant un OrderItem). La propriété calculée totalAmount retourne le total actuel. La méthode completeOrder() marque la commande comme finalisée, avec une règle de gestion : on interdit la finalisation d’une commande vide (on lance une erreur dans ce cas). En finalisant, on pourrait créer un événement de domaine (ici commenté, mais on y reviendra lors de la publication d’événement). Notons que Order ne sait rien de la base de données ou autre – elle manipule ses objets, c’est tout.
OrderRepository est l’interface du dépôt de commandes. Il fournit les opérations nécessaires au cycle de vie des agrégats Order – ici deux méthodes en exemple : save pour sauvegarder (créer/mettre à jour) une commande, et findById pour la retrouver par son ID. Dans un vrai projet on aurait possiblement d’autres méthodes (chercher par critère, supprimer, etc.). En DDD, le repository est défini au niveau du domaine comme interface, ce qui permet de le mocker ou de l’implémenter de différentes façons. Le fait de les déclarer en interface permet d’échanger facilement une implémentation par une autre (par ex. stub vs réel)​
BLOG.LOGROCKET.COM
​
BLOG.LOGROCKET.COM
.
OrderPlacedEvent est notre événement de domaine. C’est une classe simple qui emporte l’ID de la commande et le total (ainsi qu’un horodatage occurredOn). On l’émettra quand une commande est finalisée. Les événements de domaine faisant partie du langage du domaine, on les place aussi dans la couche domaine. Ici la classe ne contient que des données (on pourrait enrichir selon besoin).
Jusqu’ici, on a défini le modèle de domaine et ses comportements. L’utilisation de ce modèle dans un cas d’usage réel se fera via la couche application. Regardons maintenant comment on implémente le repository concrètement et comment on utilise le tout dans un service applicatif.Dans le dossier commandes/infrastructure et commandes/application, on pourrait avoir par exemple :
typescript
Copier
Modifier
// Implémentation concrète du OrderRepository (stockage en mémoire pour l'exemple)
class InMemoryOrderRepository implements OrderRepository {
  private orders: Map<string, Order> = new Map();

  async save(order: Order): Promise<void> {
    this.orders.set(order.id, order);
  }

  async findById(id: string): Promise<Order | null> {
    return this.orders.get(id) || null;
  }
}

// Simulateur de publication d'événements de domaine
class EventDispatcher {
  static publish(event: object): void {
    console.log("Event published:", event);
    // Dans une vraie application, on notifierait ici les écouteurs/abonnés de cet événement
  }
}

// Service Applicatif pour le cas d'utilisation "Placer une Commande"
class OrderApplicationService {
  constructor(private orderRepo: OrderRepository) {}

  async placerCommande(clientId: string, items: { productId: string; price: number; quantity: number; }[]): Promise<Order> {
    // 1. Créer l'entité commande
    const order = new Order(generateUniqueId(), clientId);  // generateUniqueId() génère un ID unique
    for (const item of items) {
      order.addItem(item.productId, item.price, item.quantity);
    }
    // 2. Finaliser la commande (vérifie les règles métier internes)
    order.completeOrder();
    // 3. Sauvegarder l'agrégat via le repository
    await this.orderRepo.save(order);
    // 4. Publier un événement de domaine indiquant que la commande a été passée
    const event = new OrderPlacedEvent(order.id, order.totalAmount);
    EventDispatcher.publish(event);
    return order;
  }
}
Explications :
InMemoryOrderRepository est une implémentation de l’interface OrderRepository. Ici, pour simplifier, on stocke les commandes dans une map en mémoire (orders mapant l’ID -> l’objet Order). La méthode save insère ou met à jour la commande dans la map, et findById la recherche. Bien sûr, en production on aurait une implémentation connectée à une base de données (par ex. un SqlOrderRepository utilisant un ORM ou des requêtes SQL). Mais l’implémentation en mémoire est utile pour les tests ou pour illustrer l’idée. L’important est que du point de vue du domaine et de l’application, l’implémentation est interchangeable : on peut brancher ici une version mémoire, une version base de données, etc., sans changer le code du service applicatif (grâce à l’interface commune).
EventDispatcher est un composant d’infrastructure/application qui se charge de publier les événements de domaine aux parties concernées. Dans cet exemple minimal, il se contente d’un console.log. En pratique, on pourrait avoir quelque chose de plus élaboré : par exemple, une liste de handlers inscrits par type d’événement, ou bien l’émission d’un message sur un bus (RabbitMQ, Kafka…) ou via un mécanisme d’EventEmitter de Node.js. L’important est que le domaine émet un OrderPlacedEvent et que ce dispatcher le prenne en charge pour avertir qui de droit. Ce EventDispatcher fait partie de la plomberie architecture (couche infrastructure ou application). Souvent, on implémente un patron Observer ou Mediator pour les événements de domaine.
OrderApplicationService est le service applicatif qui réalise le cas d’utilisation “placer une commande” du point de vue de l’extérieur (par exemple, quand un utilisateur passe commande depuis l’UI). Il injecte en dépendance un OrderRepository – ainsi, il ne connaît pas l’implémentation concrète, juste l’interface. Dans la méthode placerCommande :
On crée une nouvelle commande Order avec un ID unique (on imagine une fonction utilitaire generateUniqueId() pour générer un UUID par ex) et l’identifiant du client demandeur.
On parcourt les items demandés (données d’entrée, par ex. produits dans le panier) et on utilise la logique du domaine (order.addItem(...)) pour construire l’agrégat commande. Chaque ajout applique les règles de OrderItem (quantité positive).
On appelle order.completeOrder() pour appliquer la règle de finalisation (vérification qu’il y a au moins un item). Si cette règle n’est pas respectée, une erreur est lancée et la transaction serait avortée (ce qui évite d’enregistrer une commande vide).
Si tout est bon, on persiste la commande via le repository injecté. À ce stade, les données de la commande sont enregistrées (par exemple, en base).
On crée ensuite un objet OrderPlacedEvent avec l’ID et le total, et on le publie via le EventDispatcher. Cela va permettre à d’autres parties du système d’être informées qu’une nouvelle commande vient d’être passée.
Enfin, on retourne éventuellement l’objet Order créé (ici la fonction renvoie la commande, ce qui pourrait servir par exemple à afficher un récapitulatif à l’utilisateur, bien que dans un vrai contexte d’API on renverrait plutôt un DTO).
Ce flux illustre comment la couche application orchestre les éléments : elle utilise le domaine (Order, OrderItem) pour la logique et la validation métier, puis délègue à l’infrastructure (repository) pour la persistance, et déclenche des side-effects (ici l’événement, qui pourrait être écouté par, disons, un module de facturation ou d’email). Le tout, sans que le domaine ait conscience de ces détails.À noter : Dans certains designs DDD, l’émission d’un événement de domaine peut être gérée à l’intérieur de l’agrégat lui-même (par ex., la méthode completeOrder() pourrait retourner un OrderPlacedEvent ou l’ajouter à une liste d’événements internes de Order). L’approche peut varier, l’essentiel est que l’événement soit émis une fois la modification de domaine effectuée, et généralement traité en dehors de l’agrégat (par la couche application ou un mécanisme infrastructurel). Ici, pour simplifier, on émet l’événement juste après le save. On pourrait améliorer ce service en englobant tout cela dans une transaction (selon les besoins de consistance).Cet exemple en TypeScript montre qu’on peut appliquer les principes DDD de manière idiomatique : classes pour les entités et VOs, utilisation des exceptions pour gérer les règles invalides, interfaces pour les dépôts, etc. TypeScript offrant le typage statique et la POO, il se prête bien à ce style de conception. On pourrait par ailleurs enrichir ce code en utilisant des Types ou Interfaces pour formaliser l’événement de domaine (ex: une interface DomainEvent) ou un design pattern de résultat pour gérer proprement les erreurs plutôt que des exceptions (certains utilisent des classes Result génériques). Mais cela sort du cadre de l’exemple.
Gestion des événements de domaine et des repositories
La gestion des repositories et des domain events dans une application TypeScript suit en grande partie ce qui a été illustré ci-dessus, avec quelques considérations supplémentaires.Repositories : Dans une implémentation réelle, un repository va faire le lien avec la base de données ou tout autre stockage. En TypeScript (Node.js), cela peut se faire via un ORM (comme TypeORM, Prisma, Sequelize...) ou des requêtes directes. L’interface du repository définit les opérations métiers (par ex. save(Order)), et l’implémentation va traduire cela en opérations de persistence. Une attention particulière doit être portée à la reconstitution des agrégats : par exemple, pour findById, il faut reconstruire l’objet Order et ses OrderItem associés à partir des tables ou documents de la base. Ceci peut impliquer de faire plusieurs requêtes (une pour la commande, une pour ses lignes) et de les assembler en objets. Ce code de mapping peut être écrit manuellement ou partiellement automatisé par un ORM (via des relations définies).Le point important est de garder ce mapping dans l’infrastructure. Le domaine ne voit que l’objet reconstitué final. Par exemple, un OrderRepositorySQL pourrait exécuter :
typescript
Copier
Modifier
// pseudo-code d'une implémentation SQL
class OrderRepositorySQL implements OrderRepository {
   async findById(id: string): Promise<Order | null> {
      const orderRow = await sql`SELECT * FROM orders WHERE id=${id}`;
      if (!orderRow) return null;
      const itemRows = await sql`SELECT * FROM order_items WHERE order_id=${id}`;
      const order = new Order(orderRow.id, orderRow.customer_id);
      for(const item of itemRows) {
         order.addItem(item.product_id, item.price, item.quantity);
      }
      if(orderRow.status === 'finalisée') order.status = 'finalisée';
      return order;
   }
   // ... save() etc.
}
Dans cet exemple, le repository SQL construit bien un objet Order en utilisant le constructeur et les méthodes du domaine (addItem), assurant que les invariants (quantité positive) sont respectés même lors de la relecture. Une alternative est de faire appel à un Factory du domaine pour créer l’agrégat d’un coup. L’important est de ne pas contourner les règles du domaine lorsqu’on hydrate les entités.En TypeScript, on peut profiter des types pour que le repository retourne exactement le type d’agrégat attendu (Promise<Order>), ce qui rend l’usage du repository sûr et clair côté application.Événements de domaine : Il existe plusieurs façons de propager les domain events. Dans notre exemple, on a utilisé un simple dispatcher statique après l’enregistrement. Une approche plus sophistiquée consiste à faire en sorte que chaque agrégat stocke les événements qu’il a émis pendant la transaction. Par exemple, la classe Order pourrait avoir un tableau interne domainEvents: DomainEvent[]. Lorsqu’on appelle order.completeOrder(), au lieu de (ou en plus de) faire un EventDispatcher.publish direct, on ferait this.domainEvents.push(new OrderPlacedEvent(...)). Ensuite, le repository ou la couche application, après le save, irait récupérer order.domainEvents, les publier une par une, puis éventuellement vider la liste.Ce schéma permet de s’assurer que l’événement n’est publié que si la transaction a réussi (par ex., on ne veut pas envoyer un email de confirmation si la sauvegarde en base a échoué). En Node.js, on n’a pas de transaction ACID multi-request facilement hors base de données, mais on peut tout de même gérer l’ordre : d’abord persist, puis publier les events. On peut également centraliser la publication via un Unit of Work qui, en fin d’un cycle de transaction, prend tous les events collectés et les publie.Techniquement, publier un événement peut vouloir dire : appeler des handlers locaux (par ex., une classe BillingService inscrit un handler sur l’événement OrderPlaced pour créer une facture), ou envoyer un message sur un bus (RabbitMQ, AWS SNS, etc.), ou émettre un événement applicatif (par ex. avec Node EventEmitter). L’implémentation dépend des besoins. L’important en DDD est la découpling : le domaine émet un événement et ne connaît pas les conséquences exactes, ce qui permet d’ajouter des réactions sans modifier le domaine.Dans une application TypeScript Node, on pourra par exemple utiliser la bibliothèque EventEmitter pour enregistrer des callbacks :
typescript
Copier
Modifier
// exemple simplifié de Event Bus
const DomainEvents = new EventEmitter();
DomainEvents.on('OrderPlacedEvent', (event: OrderPlacedEvent) => {
  // Handler lorsqu'une commande est passée
  sendConfirmationEmail(event.orderId, event.total);
  // ou déclencher d'autres logiques...
});
Et dans OrderApplicationService, on ferait DomainEvents.emit('OrderPlacedEvent', event). Ce n’est qu’une des possibilités (on pourrait aussi avoir un système de messages plus robuste pour passer à des microservices).Résumé : les événements de domaine sont un outil puissant pour garder le domaine pur tout en permettant l’interaction avec d’autres parties. Ils sont gérés soit immédiatement dans le service applicatif, soit via une mécanique plus globale d’event bus. L’essentiel est de respecter le caractère après-coup de l’événement (après succès d’une action métier) et de ne pas faire dépendre la logique interne du domaine des consommateurs de ces events.
Interaction avec l’infrastructure (base de données, API, etc.)
Dans une architecture DDD, la domaine est indépendant de l’infrastructure, mais doit tout de même s’y connecter pour les besoins pratiques. Le point de contact principal ce sont les repositories (pour la BD) et éventuellement les services d’infrastructure externes (pour les API tierces, l’envoi d’emails, etc.).Accès base de données : Comme vu, le domaine définit quoi stocker (via les repositories interfaces) et l’infrastructure définit comment. En TypeScript, cela signifie souvent écrire des classes d’implémentation de repository qui utilisent soit un ORM soit des appels directs. Une bonne pratique est d’encapsuler complètement la logique de mapping au sein du repository ou de la couche infrastructure. Par exemple, si l’on utilise TypeORM, on pourrait avoir des “entities” TypeORM (différentes des entités DDD) qui correspondent aux tables, et le repository ferait la conversion entre l’entité TypeORM et l’entité du domaine. On évite ainsi d’annoter les entités du domaine avec des décorateurs de persistence (ce qui les lierait à l’ORM). Cette séparation renforce l’isolement du domaine : on pourrait changer d’ORM ou de schéma sans toucher aux objets métier.Services externes et API tierces : Lorsqu’il faut appeler une API externe depuis notre domaine (par exemple, un service de paiement, un service de géolocalisation, etc.), on ne veut pas que l’appel HTTP se fasse au milieu d’une méthode d’entité. Le DDD recommande de modèle ces interactions via des services d’infrastructure ou une Anti-Corruption Layer comme discuté. En pratique, dans la couche application, on peut injecter un service externe qui fournit les fonctionnalités requises d’une manière compréhensible par le domaine. Par exemple, si notre domaine a besoin de vérifier le score de crédit d’un client en appelant un service bancaire, on pourrait définir une interface CreditCheckService dans le domaine (ou application) avec une méthode getScore(clientId): number. L’implémentation concrète ExternalCreditCheckService irait appeler l’API REST du service de crédit et renvoyer le score. La couche application pourrait alors faire : const score = creditService.getScore(client.id) et ensuite décider (logique de domaine) si on accepte la commande ou pas. Ainsi, le domaine reste isolé des détails du protocole HTTP.Si le modèle de l’autre système ne correspond pas bien, c’est là qu’on introduit une ACL : ce ExternalCreditCheckService jouerait le rôle d’anti-corruption en convertissant les données de notre Client dans la requête appropriée, puis en traduisant la réponse en un objet ou une valeur du domaine (par ex. un objet valeur CreditScore). De même, pour consommer un événement de domaine et le transmettre à un autre système, on peut mettre en place un adaptateur dans l’infrastructure qui écoute nos Domain Events et appelle l’API externe correspondante.Exemple d’intégration : Supposons que lors d’une OrderPlacedEvent, on doive notifier un système de gestion d’inventaire externe pour réserver le stock. On pourrait implémenter un handler (dans la couche infrastructure) qui écoute OrderPlacedEvent. Ce handler, via un client API (par ex. InventoryApiClient), va appeler l’endpoint POST /reserveStock de l’API d’inventaire en lui passant les informations de la commande (articles et quantités). Ce handler ferait la conversion nécessaire (notre OrderItem en un DTO JSON attendu par l’API d’inventaire). Ainsi, le domaine commande ne connaît rien de cette API, il se contente d’émettre l’événement. La couche infrastructure autour de l’API d’inventaire est notre anti-corruption layer dans ce cas : elle traduit l’événement de notre contexte Commande vers le langage du contexte Inventaire.En termes de code TypeScript, cette intégration peut être écrite proprement grâce à la syntaxe moderne (Promises/async-await pour les appels réseau, etc.), tout en gardant les dépendances inversées (le service applicatif pourrait utiliser une interface d’InventoryService injectée, implémentée par InventoryApiClient).Gestion des transactions : Lorsque l’on travaille avec une base de données, il faut veiller à maintenir la consistance des agrégats. Idéalement, on encapsule la modification d’un agrégat dans une transaction BD. Par exemple, dans placerCommande ci-dessus, on pourrait ouvrir une transaction au début et la committer après le save(order). Si l’envoi de l’événement doit être atomique avec la transaction, des solutions comme Outbox pattern peuvent être envisagées (écrire l’événement dans une table en même temps que la commande, puis un processus séparé l’envoie). Ces considérations dépassent le simple code TypeScript et touchent l’architecture globale, mais sont importantes à mentionner quand on parle d’infrastructure.En résumé, l’interaction domaine <-> infrastructure se fait via des interfaces explicites et des implémentations adaptées. En TypeScript, on profite de l’interface du langage pour définir les contrats (repositories, services externes) dans le domaine, et on laisse la charge à l’infrastructure de fournir des classes concrètes qui respectent ces contrats. Le tout est assemblé généralement via de l’injection de dépendances (manuellement, ou à l’aide d’un conteneur IoC si souhaité). Par exemple, on peut avoir dans le code d’initialisation de l’app :
typescript
Copier
Modifier
const orderRepo = new SqlOrderRepository(connection);
const orderService = new OrderApplicationService(orderRepo);
Ainsi, on injecte un repo SQL réel à notre service applicatif. Le domaine, lui, n’a rien à faire de spécifique.L’avantage de cette organisation est une testabilité accrue (on peut tester Order et OrderApplicationService avec un InMemoryOrderRepository sans lancer de base de données) et une évolutivité (on peut répartir chaque contexte sur un microservice plus tard, brancher des queues de messages sans toucher au domaine, etc.). C’est un des objectifs recherchés par DDD : un découplage propre entre le Core Domain et les détails d’infrastructure, ce qui permet de faire évoluer l’infrastructure ou le déploiement (scaling, refonte technologique) sans devoir repenser tout le modèle métier.
4. Meilleures Pratiques et Cas d’Utilisation
Cas concrets d’application du DDD
Le DDD s’avère particulièrement bénéfique dans les projets où la logique métier est riche, complexe et amenée à changer. Quelques cas typiques d’utilisation réussie de DDD :
Systèmes d’information d’entreprise : par exemple, un système ERP ou CRM comportant de multiples sous-domaines (ventes, facturation, stock, livraison, ressources humaines…). DDD aide à structurer chaque sous-domaine en Bounded Context clair, avec un langage métier bien défini, évitant la confusion dans un gros monolithe. De grandes applications bancaires, assurances, santé, etc., ont adopté DDD pour mieux gérer la complexité métier (calculs financiers, règles d’éligibilité, réglementation…).
E-commerce et Logistique : Un site e-commerce comporte typiquement plusieurs domaines complexes : gestion du catalogue produit, commandes, paiements, livraisons, stocks… Chacun peut être un contexte DDD. Par exemple, le domaine Commande s’occupe du panier et de la commande client, le domaine Paiement gère les transactions financières, le domaine Stock gère les réservations de produits, etc. En DDD, on peut faire évoluer ces parties indépendamment (voire en microservices) tout en gardant une cohérence globale via des événements de domaine et des contrats bien définis.
Domaines financiers : Banque, trading, comptabilité – ce sont des domaines par nature très complexes (ex : calcul d’intérêts, règles comptables, produits financiers variés). DDD a beaucoup de succès dans ces secteurs car il permet de modéliser finement les concepts métier (ex : compte, transaction, solde, agrégats pour consolider tout ça) et de traiter les règles (ex : “pas de découvert au-delà de …”) directement dans les entités. De plus, les experts métiers (comme les comptables ou les traders) peuvent participer à la définition du langage omniprésent, assurant que le logiciel reflète correctement les besoins.
Télécommunications : gestion d’abonnements, tarification, routage d’appels – ce sont également des domaines complexes où DDD a été appliqué pour segmenter les sous-domaines (abonnement, facturation, réseau…) et formaliser un modèle autour des concepts clés (client, contrat, facture, etc.).
Projets modulaires / microservices : DDD est souvent utilisé pour découper un système en microservices. Chaque microservice peut correspondre à un bounded context du domaine. Par exemple, une application de réservation de voyages peut avoir un service Réservations, un service Paiements, un service Notifications, etc., chacun avec son modèle. DDD fournit une méthodologie pour identifier ces limites (via l’analyse de domaine) plutôt que de découper arbitrairement. Des entreprises comme Amazon, Netflix ont dans leurs architectures des principes proches de DDD pour structurer leurs services autour du domaine métier (même si elles ne le nomment pas forcément ainsi publiquement).
En bref, DDD s’applique quand on a de la complexité métier à dompter et une nécessité d’échanger avec des experts de ce métier. Il brille dans les projets de moyenne à grande envergure, sur le long terme, où investir dans une bonne conception apporte des bénéfices de maintenabilité et d’évolutivité. A contrario, sur un petit projet CRUD avec très peu de logique, DDD pourrait être perçu comme une sur-ingénierie.
Pièges à éviter (Lessons Learned)
Bien que puissant, le DDD comporte des écueils connus qu’il convient d’éviter pour en tirer le meilleur parti :
Modèle anémique : C’est un anti-pattern classique où les entités du domaine ne contiennent finalement aucune logique, juste des données, et où toute la logique est implémentée dans des services applicatifs ou, pire, dans la couche présentation. Cela revient à faire du modèle objet “creux” et à écrire des procédures par ailleurs – ce qui va à l’encontre de DDD (et du principe OO en général). Un indice de modèle anémique est qu’on voit des objets DTO partout et des services qui font de simples repository.save() sans appel de méthode métier sur les entités. Pour éviter cela, mettre la logique au plus près des données qu’elle concerne : si une règle touche l’entité Commande, implémentez-la dans Order (comme on l’a fait avec completeOrder() qui empêche une commande vide). Ne créez un Domain Service que si vraiment la logique implique plusieurs entités sans appartenir à l’une d’elles. Sinon, vous aboutirez à un design anti-DDD où le modèle n’apporte rien de plus qu’une base de données objet​
STACKOVERFLOW.COM
.
Se focaliser sur les détails techniques aux dépens du métier : Il est tentant pour des développeurs d’aborder DDD en se concentrant sur les patterns (entités, repos, etc.) et d’oublier l’essence : la compréhension profonde du domaine. Un piège courant est de se perdre dans l’implémentation de frameworks, d’ORM, de patterns de conception, et de négliger la collaboration avec l’expert métier ou la définition du langage commun​
STACKOVERFLOW.COM
. Toujours garder à l’esprit que DDD est guidé par le domaine avant tout. Le but n’est pas d’appliquer des patterns pour le plaisir, mais de résoudre les problèmes métier proprement. Conseil : impliquer régulièrement les experts métier, vérifier que votre modèle correspond à leur vision, et ne pas être “esclave” d’un pattern DDD si le besoin n’y est pas.
Bounded Contexts flous ou insuffisants : Ne pas identifier clairement les contextes délimités peut mener à un modèle global confus. Si on mélange les termes de deux sous-domaines différents dans un même modèle, on retombe dans le “Big Ball of Mud”. Eric Evans souligne que les notions de Bounded Context et de langage ubiquitaire sont parmi les plus importantes de DDD​
STACKOVERFLOW.COM
. Il faut donc prendre le temps, lors de l’analyse initiale, de bien découper le domaine en sous-domaines cohérents, chacun avec son lexique propre. Inversement, avoir trop de contextes peut compliquer l’ensemble inutilement – c’est un équilibre à trouver. L’erreur la plus fréquente reste de ne pas assez en créer, par méconnaissance ou par volonté de tout unifier (ce qu’il faut justement éviter). Recherchez les termes ambigus ou surchargés (par ex. “Client” utilisé par 3 départements pour des choses différentes) et envisagez de les séparer dans des contextes.
Surcharger l’architecture pour un petit projet : DDD est très tentant intellectuellement, mais il faut l’appliquer là où il apporte de la valeur. Si votre application est simple (quelques CRUD basiques sans logique), introduire tous les niveaux Entité/VO/Repo/Services/Events va alourdir inutilement le développement et la maintenance. Un écueil est de vouloir absolument faire du DDD partout (“DDD-lite”) et d’être englué dans du code cérémonieux pour pas grand-chose. Soyez pragmatique : DDD est un ensemble d’outils, à utiliser de façon ciblée. Comme le dit l’adage, “pas la peine de tuer une mouche avec un canon”. Certains concepts DDD ne se justifient que dans des applications de grande envergure​
DEV.TO
. Il vaut mieux un code simple et clair sans DDD qu’une pseudo-architecture DDD non maîtrisée. Donc, évaluer la complexité du domaine ; si elle est faible, on peut très bien implémenter rapidement sans tout le formalisme, quitte à refactorer en DDD plus tard si le métier devient plus compliqué.
Ignorer l’Ubiquitous Language : Parler DDD sans réellement mettre en place le langage partagé est voué à l’échec. Ce langage doit être utilisé dans le code et dans les conversations de tous les jours. Un piège est de conserver du jargon technique ou interne dans le code alors que le métier utilise d’autres mots. Ou bien que différentes équipes utilisent des termes différents pour la même chose. Cela crée un fossé. Il faut constamment cultiver ce langage, corriger les écarts. Par exemple, si le métier parle de “dossier client” et que dans le code on a une classe CustomerFile mais que d’autres endroits parlent de ClientRecord, harmonisez ! L’effort peut sembler lourd, mais c’est la clé de la communication sans heurts.
Manque de collaboration avec le domaine : Si les développeurs restent dans leur bulle et ne consultent pas assez les experts métier, le risque est de mal comprendre certaines règles ou d’en manquer. DDD promeut une collaboration étroite. Un anti-pattern serait de deviner ou de supposer le fonctionnement métier sans vérification, menant à un modèle inadéquat. Des techniques comme l’Event Storming, les ateliers de modélisation agile, aident justement à réunir tout le monde pour construire la vision commune.
Ne pas revoir le modèle : le DDD encourage l’itération et la refactorisation du modèle au fil de la compréhension. Un piège est de “figer” le modèle initial et de ne pas oser le faire évoluer même si le métier change ou si l’on découvre de nouvelles subtilités. Il faut accepter de repenser les agrégats, de renommer des concepts, de splitter un contexte en deux, etc., quand c’est pertinent. Sinon, on finit par tordre le code pour des cas non prévus initialement, et on perd les bénéfices.
En somme, les principaux pièges tournent autour d’une mauvaise application des principes : soit trop superficielle (juste ajouter des couches techniques sans vraie réflexion métier), soit trop rigide (appliquer des patterns même s’ils ne conviennent pas ou plus), soit pas assez communicative (rester en silo technique). Le succès d’un projet DDD réside dans un savant mélange de rigueur (sur les concepts de base) et de pragmatisme (adapter à ses besoins). Comme toute approche, il faut l’utiliser à bon escient et avec mesure.
Conseils pour bien organiser son code DDD (Best Practices)
Pour conclure, voici quelques bonnes pratiques éprouvées lors de la mise en place de Domain-Driven Design en TypeScript (ou dans d’autres langages) :
Faites du domaine la priorité : Commencez par bien comprendre et modéliser le métier avant de coder. Investissez du temps dans la création du langage omniprésent avec les experts. Un code DDD réussi, c’est un code où en lisant les classes et méthodes, un expert métier reconnaît ses concepts. Par exemple, si le domaine parle de “valider une commande”, assurez-vous d’avoir une méthode valider() ou completeOrder() dans votre modèle, plutôt qu’une série d’updates dans un service technique. Alignez noms de classes, de méthodes et de modules sur le vocabulaire métier.
Enrichissez vos entités et objets valeur : Utilisez pleinement la POO. Mettez les règles de gestion dans les entités/VO eux-mêmes quand c’est logique. Cela évite le modèle anémique. N’hésitez pas à donner des méthodes aux objets pour qu’ils accomplissent des opérations courantes de manière sûre. Par exemple, une entité CompteBancaire pourrait avoir une méthode retirer(montant) qui encapsule la logique de vérifier le solde suffisant, plutôt que de laisser l’appelant faire ces calculs. De même, un objet valeur Money pourrait avoir une méthode add(Money): Money pour additionner deux montants. Ces comportements rendent le code plus lisible et plus robuste.
Utilisez les Value Objects pour la robustesse : Chaque fois que vous avez un concept qui a des règles internes ou un format spécifique, envisagez d’en faire un objet valeur. Par exemple, un email, un numéro de téléphone, une quantité, un prix... En créant un type dédié (Email, PhoneNumber, Quantity, Price), vous pouvez valider sa valeur à la construction et garantir qu’un objet invalide n’existe pas. Cela réduit les validations répétitives un peu partout et centralise la logique. De plus, les Value Objects clarifient le modèle (on voit qu’une propriété est un Email et pas juste une string). En TypeScript, cela peut se faire via des classes ou des types alias avec des fonctions de validation. L’immutabilité des VO aide aussi à la stabilité du système.
Gardez vos agrégats cohérents et petits : Un agrégat devrait correspondre à un concept cohérent du domaine et contenir tout ce qui est nécessaire pour garantir ses invariants, mais pas plus. Évitez les agrégats tentaculaires qui incluent trop d’entités – cela peut causer des problèmes de concurrence (verrous) et de performances (chargement inutile). Appliquez la règle : si deux entités n’ont pas besoin d’être mises à jour atomiquement en même temps, elles ne doivent probablement pas être dans le même agrégat. Par exemple, dans un domaine commerce, on peut décider que Order et Customer sont deux agrégats séparés (on ne modifie pas un client en même temps qu’une commande dans une même transaction, généralement). Par contre, Order et OrderItem vont ensemble. En cas de doute, commencez avec des agrégats plus petits ; il est plus facile de les composer que de les diviser plus tard.
Contrôlez les accès à l’agrégat racine : Respectez la règle que seule la racine de l’agrégat est référencée de l’extérieur. Les membres internes (entités secondaires) ne devraient pas être manipulés directement hors du contexte. En pratique, en TypeScript, on ne peut pas empêcher complètement l’accès aux propriétés, mais on peut décider de ne pas exposer de repository pour les entités internes, de ne pas passer ces objets en dehors de leur agrégat, etc. Souvent on garde les collections internes en private et on fournit des méthodes sur la racine pour opérer dessus (comme addItem sur Order plutôt que donner accès à la liste des items). Ceci garantit que toutes les règles de l’agrégat passent par la racine, ce qui facilite leur respect.
Séparez clairement vos couches : Ne mélangez pas la logique de présentation ou d’infrastructure avec le domaine. Par exemple, pas de logique SQL ou HTTP dans vos entités de domaine. Inversement, évitez de mettre de logique métier dans vos contrôleurs ou vos classes d’accès aux données. Respectez l’isolation des préoccupations. Cela rend le code plus modulable et testable. Vous devriez pouvoir modifier la couche d’accès aux données (p. ex. passer d’une base locale à une API distante) sans toucher aux règles métier, et changer une règle métier sans impacter les couches techniques – si vos couches sont bien séparées, ce sera possible.
Adoptez le Domain Events pour les interactions complexes : Lorsque vous avez des interactions entre contextes ou des réactions asynchrones à gérer, utilisez les événements de domaine plutôt que des appels directs trop couplés. Cela réduit le couplage entre vos modules. Par exemple, au lieu que le code d’une commande appelle directement le service de livraison, émettez un événement OrderPlaced et ayez un listener dans le contexte livraison qui va initier l’expédition. Cela permet d’ajouter, de modifier ou de supprimer des réactions sans toucher au code de base. Testez bien néanmoins que ces enchaînements d’événements donnent les résultats attendus (les tests d’intégration ont leur place ici).
Documentez les Context Maps : Si votre domaine comporte plusieurs Bounded Contexts, clarifiez leurs relations : lequel est en amont/aval (upstream/downstream), lesquels partagent certains événements ou intègrent les données de l’autre, etc. Utilisez les patterns de contexte (Conformiste, ACL, Pub/Sub, Partagé, etc.) pour définir comment ils interagissent. Une simple page de documentation avec un schéma ou un tableau des contextes et de leurs interfaces peut éviter bien des confusions aux nouveaux venus. Par exemple : “Le contexte Facturation est en aval du contexte Commande : il reçoit l’événement OrderPlaced et crée une facture de vente correspondante.”
Testez le domaine isolément : L’un des bénéfices d’une bonne architecture DDD est de pouvoir tester la logique métier sans lancer toute l’application. Profitez-en ! Ecrivez des tests unitaires pour vos entités et objets valeur (ex : tester qu’une commande vide ne peut pas être finalisée, tester qu’un email invalide est rejeté à la construction de l’objet Email…). Ces tests vont vous aider à valider le bon comportement du modèle. De plus, quand le modèle évolue, les tests servent de filet de sécurité. En TypeScript, on peut utiliser des frameworks comme Jest ou Mocha pour cela. Injetez des fakes pour les repos dans les tests de services applicatifs afin de vérifier les scénarios (ex: vérifier que placerCommande publie bien l’event attendu). Avoir un domaine bien testé facilite ensuite l’évolution du code en confiance.
Restez pragmatique et évolutif : DDD offre de nombreux patterns, mais ne cherchez pas à tout implémenter d’emblée. Peut-être que vous n’aurez pas besoin de Factory ou de Service de Domaine au début, n’en ajoutez pas sans raison. À l’inverse, si avec le temps vous sentez le besoin de modulariser davantage ou d’introduire un nouveau Value Object, faites-le. Comme mentionné, DDD n’est pas une recette figée : “il faut l’implémenter de la manière qui a le plus de sens pour votre contexte”​
DEV.TO
. Par exemple, certains projets n’ont pas de couche application séparée parce que l’API web fait déjà cette orchestration – c’est acceptable si ça reste lisible et que le domaine n’est pas pollué. Adaptez l’architecture en continu en fonction de la taille du projet et de l’équipe, et n’ayez pas peur d’ajuster (ajouter un nouveau Bounded Context si le domaine s’élargit, refactorer une entité en objet valeur si on réalise qu’elle n’a finalement pas besoin d’identité, etc.).
Exploitez les capacités de TypeScript : Un dernier conseil spécifique au langage : TypeScript offre des fonctionnalités de typage puissant (types littéraux, unions/discriminants, generics avancés). Ceux-ci peuvent servir votre domaine. Par ex, vous pouvez définir des types unions pour représenter explicitement certains états (notre status: "en_attente" | "finalisée" de Order en est un petit exemple – cela évite d’avoir un string libre avec risque de typo). Vous pouvez utiliser des interfaces pour définir vos agrégats de manière abstraite et fournir différentes implémentations (utile si vous avez des comportements légèrement divergents par contexte technique, même si à éviter en règle générale dans le domaine). Les Types utilitaires peuvent aider à créer des Value Objects plus facilement (ex: Readonly pour l’immuabilité). Bref, utiliser TS à bon escient peut renforcer la robustesse de votre code DDD.
Enfin, n’oubliez pas que DDD est un moyen et non une fin en soi. L’objectif ultime est de produire un logiciel qui résout le problème métier de manière évolutive et compréhensible. Si les pratiques DDD vous y aident, alors utilisez-les judicieusement. Sinon, il ne faut pas hésiter à rester simple. Eric Evans lui-même insiste sur le fait que les projets qui réussissent en DDD sont ceux qui ont su mettre l’accent sur le langage commun et la compréhension partagée du domaine, plutôt que ceux qui appliquent dogmatiquement tous les patterns​
STACKOVERFLOW.COM
. En suivant les principes de base et en apprenant de l’expérience, vous serez en mesure de tirer le meilleur de Domain-Driven Design dans vos projets TypeScript.Références :
Eric Evans, Domain-Driven Design: Tackling Complexity in the Heart of Software, Addison-Wesley, 2003.
Martin Fowler, bliki sur le DDD et ses patterns (entités, Value Objects, agrégats, etc.) 
MARTINFOWLER.COM
​
MARTINFOWLER.COM
​
MARTINFOWLER.COM
.
Documentation Microsoft – An Introduction to Domain-Driven Design (notamment application aux microservices)​
LEARN.MICROSOFT.COM
​
LEARN.MICROSOFT.COM
.
Articles de blog et communautés (par ex. Dev.to, Medium) sur l’implémentation de DDD en TypeScript et les bonnes pratiques associées​
DEV.TO
​
DEV.TO
​
BLOG.LOGROCKET.COM
.
Retours d’expérience (DZone, Stack Overflow) sur les erreurs fréquentes et comment les éviter​
