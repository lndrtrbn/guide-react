# Monter en compétence sur React

_v1.0_

L'objectif du document est d'expliquer aussi succinctement que possible les fondamentaux de [React](https://fr.reactjs.org/) pour être prêt à développer rapidement. Des points de bonnes pratiques seront également abordés.

## Sommaire

- React, c'est quoi ?
- Les prérequis pour bien débuter
  - Langage de programmation : Typescript
  - La syntaxe Javascript ES6
  - Le code asynchrone en JS TODO
- Les blocs principaux de React
  - Les composants : IHM de notre application
  - Les hooks : enrichir nos composants
  - Le router : naviguer entre les pages
  - La gestion des formulaires TODO
- Sujets avancés
  - Les composants de présentation VS les composants métier
  - Comment communiquer entre les composants ?
- Pour aller plus loin

## React, c'est quoi ?

React est une **librairie Javascript**. Un framework dont l'objectif est de pouvoir développer des applications accessibles depuis un navigateur (comme Angular, Vue, ...). Il apporte un ensemble de fonctionnalités permettant de mieux structurer son code et de s'épargner de tâches récurrentes et complexes.

A l'inverse d'Angular qui vient avec beaucoup d'éléments et qui impose sa propre vision de comment doit être développée une application, React apporte le minimum nécessaire à la création de composants dynamiques. On ajoutera des librairies supplémentaires pour ajouter d'autres fonctionnalités (un Router par exemple).

> Malgré une approche différente, on retrouve des points communs dans la façon de penser et dans les bonnes pratiques entre React et Angular (et les autres frameworks du même style).

## Les prérequis pour bien débuter

Si les sujets suivants n'ont pas de secret pour vous alors vous pouvez passer à la section suivante.

- Langage de programmation : Typescript (optionnel)
- La syntaxe Javascript ES6
- Le code asynchrone en JS

### Langage de programmation : Typescript (optionnel)

Avec React on fait des applications web exécutées dans un navigateur. Sans surprise c'est donc Javascript qui est utilisé. On peut également, avec les dernières versions de React, développer en Typescript. Ce qui est conseillé de faire, même si ce n'est pas obligatoire.

Typescript, c'est avant tout du Javascript. Du Javascript avec une notion de POO. Ce qui est intéressant avec Typescript (et qui doit être utilisé systématiquement si on veut garder un code lisible) : **le typage**.

> Autrement dit : pas de _any_ dans le code merci

```typescript
// === TYPAGE === \\

// --- En javascript
var nbTweets = 5; // typage dynamique
nbTweets = "5"; // OK

// --- En typescript
let nbTweets: number = 5; // typage statique explicite
nbtweets = "5"; // Erreur à la compilation
let nbComments = 10; // typage statique implicite
nbComments = "10"; // Erreur à la compilation
```

Typescript permet de faire des interfaces, des classes, de l'héritage, on essaiera donc de toujours modéliser le format des objets que l'on manipule.

```typescript
// Interface pour représenter le format du body
// d'une requête /login à une API.
interface LoginDto {
  login: string;
  pwd: string;
}

// Classe pour modéliser un utilisateur d'une app.
class User {
  firstname: string;
  lastname: string;
  role: Role; // "Role" peut être une interface, une classe, une énumération...
}
```

> Les exemples d'utilisation des interfaces et classes sont très basiques, Typescript apporte bien plus, vous pouvez aller voir la [documentation officielle](https://www.typescriptlang.org/docs/home.html). Ce qui est important de comprendre est le fait de pouvoir modéliser les données pour être raccord avec l'API, ce qui n'était pas possible en Javascript (car pas de typage).

### La syntaxe javascript ES6

#### **Les fonctions anonymes**

C'est quelque chose de beaucoup utilisé en React. En ES6 on peut déclarer une fonction de deux façons :

```js
// Façon classique
function sum(a, b) {
  return a + b;
}

// Façon ES6
const sum = (a, b) => {
  return a + b;
};

// En plus, si la fonction fait une seule instruction, on peut
// la mettre sur une seule ligne et ommetre le mot clé "return".
const sum = (a, b) => a + b;
```

Une fonction anonyme est une fonction avec la syntaxe ES6 mais qui n'est pas nommée, c'est à dire qu'elle n'est pas déclarée dans une variable.

```js
// Execute la fonction {callback} après un délais de {timer} millisecondes
function waitFor(timer, callback) {
  setTimeout(callback, timer);
}

// La fonction qui log 'ccsv' n'a pas de nom, c'est une fonction anonyme.
waitFor(1000, () => console.log("ccsv !!"));

// C'est équivalent à :
function logCcsv() {
  console.log("ccsv !!");
}
waitFor(1000, logCcsv);
```

#### **La destructuration**

La destructuration permet d'accéder facilement à des éléments d'un objet ou d'un tableau.

```typescript
// Supposon un objet task qui ressemble à ça :
export interface Task {
  id: number;
  content: string;
  isPrio: boolean;
}

// On peut destructurer notre objet de la façon suivante :
const { id, content, isPrio } = task;
// Au lieu de faire :
const id = task.id;
const content = task.content;
const isPrio = tast.isPrio;
```

De même pour les tableaux, si on considère un tableau représentant la position géographique d'un élément avec latitude en premier puis la longitude dans le tableau.

```js
const [lat, long] = coordinate;
// Au lieu de faire :
const lat = coordinate[0];
const long = coordinate[1];
```

C'est important de comprendre ces syntaxes qui ne sont pas liées à React pour ne pas tout mélanger en attaquant les concepts de React. Ce sont juste des sucres syntaxiques, il faut juste s'habituer à les lire.

## Les blocs principaux de React

### Les composants : IHM de notre application

Les composants sont les éléments qui nous serviront à faire notre interface graphique. Un composant a pour objectif d'**être rendu dans le DOM**. On peut faire un composant pour notre page entière, pour le haut de page, pour notre menu, pour une liste, un élément de la liste, pour un formulaire... ll n'y a pas de contrainte sur ce que représente un composant. La seule chose est que c'est un élément graphique.

> Il arrive qu'on puisse dans certains cas écrire des composants React qui n'ajoutent rien dans le DOM, qui retournent `null`. Ce sont des cas assez spécifiques et assez rares. Ceci pourrait être utilisé si on veut faire un wrapper d'une librairie JavaScript pour React, comme il en existe pour OpenLayers par exemple.

On peut imaginer faire un seul composant qui contient tout le code de notre application. A moins qu'elle soit minimaliste ce n'est pas, **du tout**, une bonne idée. Il faut donc se poser la question de comment notre application peut être découpée en plusieurs composants. Supposons que notre application soit, pour être original, une liste de tâches à réaliser. On peut imaginer le découpage suivant :

- un composant qui sera notre page entière contenant :
  - un composant avec un formulaire pour ajouter une tâche,
  - un composant avec la liste des tâches à effectuer, lui même contenant :
    - X composants représentant chacun une tâche.

Un composant React est composé de plusieurs fichiers :

- un fichier JS ou TS contenant une fonction représentant le composant,
- un fichier pour le style CSS (ou SCSS),
- un fichier pour les tests unitaires.

Reprenons notre todo list, le composant pour afficher **une tâche** pourrait ressembler à ce qui suit.

> Si certains bouts de code semblent confus, pas de panique, les explications sont dans les paragraphes suivant.

```tsx
// Task.tsx

// La tâche à effectuer, ici juste un string pour
// la simplicité de l'exemple. La notion de props
// sera abordée juste après.
const Props = {
  task: string,
};

export default function Task({ task }: Props) {
  return <p className="ma-super-tache">{task}</p>;
}
```

```scss
// Task.scss

.ma-super-tache {
  color: yellow;
  background: red;
  font-size: 100px;
}
```

#### **Les composants sous forme de classe**

Ancienne façon de faire avant l'apparition des hooks. Il est toujours possible de faire de cette manière mais elle ne sera pas abordée dans ce document.

> Si jamais vous voulez des infos : CF la documentation officielle de React.

#### **Les composants sous forme de fonction**

Qu'est ce qu'un composant React ? C'est une fonction qui prend en paramètre un objet qu'on appelle **props** et qui retourne du code **JSX**.

Le JSX c'est le code qui ressemble au HTML qu'on écrit directement dans notre code JS ou TS. C'est ce qui permet de déterminer à quoi ressemble notre composant, c'est le paragraphe `<p>` dans notre exemple de Todo list.

Les props sont les propriétés de l'objet qu'on fournit en paramètre de notre fonction composant. Ces props sont passées à la fonction via des attributs HTML quand on utilise notre composant. Un exemple :

```jsx
function ChildComponent({ children, color }) {
  const style = { background: color };
  return (
    <div id="enfant" style={style}>
      {children}
    </div>
  );
}

function ParentComponent() {
  return (
    <div id="parent">
      <ChildComponent color="#ffffff">
        <p>I'll be inserted as 'children' props</p>
      </ChildComponent>
    </div>
  );
}
```

```html
<!-- Résultat final dans le DOM -->
<div id="parent">
  <div id="enfant" style="background: rgba(255, 255, 255);">
    <p>I'll be inserted as 'children' props</p>
  </div>
</div>
```

> La propriété children du composant enfant représente ce qui est passé à l'intérieur du composant quand on l'appelle, ici le paragraphe `<p>`.

> Un composant peut ne pas avoir de paramètre comme c'est le cas pour le composant parent dans l'exemple.

#### **Le data-binding**

Le data-binding c'est ce qui nous permet de faire le pont entre les données du corps de notre fonction et notre template JSX. Il existe plusieurs façon de le faire et cela va notamment dépendre du sens dans lequel on fait le binding (script vers template ou l'inverse).

Du script vers le JSX, on utilise les accolades, à l'intérieur on peut y mettre une variable, un appel de fonction, une expression.

```jsx
// Affichera le contenu de la tâche
return <p>{task}</p>;
```

On peut aussi l'utiliser au niveau d'un attribut d'une balise HTML.

```jsx
// Le champ sera rempli avec la valeur de la tâche
return <input type="text" value={task} />;
```

Dans le sens JSX vers script c'est différent. On fait forcément appel à une fonction en réaction à quelque chose qui s'est produit (souvent une interaction utilisateur).

```jsx
// Au clic sur le bouton on fait appel à la méthode submitForm()
// qui est définie dans notre script
return <button onClick={submitForm}>Envoyer</button>;
```

#### **La manipulation du JSX**

JSX apporte la possibilité de modifier dynamiquement le template avec des conditions, des boucles. Ceci grâce à des expressions Javascript insérées dans le JSX.

On peut afficher ou masquer certaines parties du template selon des conditions. Par exemple on peut imaginer afficher un message à la place de notre liste de tâche si aucune tâche n'a été créée.

```tsx
// Le texte s'affichera uniquement si le tableau de tâches est vide
return <>{tasks.length === 0 && <p>Plus rien à faire ! C'est repos</p>}</>;
```

De manière un peu similaire on peut ajouter des classes dynamiquement selon des conditions. On utilise les conditions ternaires pour avoir une expression JS dans notre string de classes CSS.

```jsx
function Example({ task }) {
  // La tâche aura la classe "high-priority" si la condition est vraie
  const classCSS = `task ${task.isPrio ? "high-priority" : ""}`;
  return <p className={classCSS}>{task.content}</p>;
}
```

On peut également faire des boucles, utiles par exemple quand on a une liste de tâches à afficher. Pour cela on utilise les même méthodes que quand on manipule un tableau mais on retourne des éléments JSX.

```tsx
return (
  // On aura un paragraphe pour chaque tâche de notre tableau tasks
  <>
    {tasks.map((task) => (
      <p>{task.content}</p>
    ))}
  </>
);
```

#### **Notre todo list**

Si on reprend notre exemple de todo list, le code pourrait ressembler à ça :

```typescript
// Notre modèle de tâche, nous utilisons une
// interface plutôt qu'une classe car de toute façon
// nous n'allons pas créer d'instances.
export interface Task {
  id: number;
  content: string;
  isPrio: boolean;
}
```

```tsx
export default function TodoList() {
  // Notre liste de tâches. On reparlera de useState un peu plus tard.
  const [tasks, setTasks] = useState([
    { id: 1, content: "Faire les courses", isPrio: true },
    { id: 2, content: "Donner des nouvelles à maman", isPrio: false },
  ]);

  // Méthode appelée quand on reçoit un évènement de l'enfant.
  function deleteTask(taskId: number) {
    // On enlève la tâche correspondant à l'id donné.
    const newTasks = tasks.filter((t) => t.id !== taskId);
    setTasks(newTasks);
  }

  return (
    <div class="my-todoes">
      {tasks.map((task) => {
        return <Task task={task} onDelete={() => deleteTask(task.id)} />;
      })}
      {tasks.length === 0 && <p>Plus rien à faire ! C'est repos</p>}
    </div>
  );
}
```

```tsx
export default function Task({ task, onDelete }) {
  const classCSS = `task ${task.isPrio ? "high-priority" : ""}`;

  return (
    <div className={classCSS}>
      <p>{task?.content}</p>
      <button onClick={onDelete}>X</button>
    </div>
  );
}
```

> Les fichiers de style ne sont pas représentés car ils ne sont pas vraiment spécifiques à React.

### Les hooks : enrichir nos composants

Les hooks permettent d'enrichir le comportement de nos composants et de partager des comportements entre les composants sans avoir à faire des composants à rallonge.

Ils permettent d'accéder aux fonctionnalités de React en dehors d'un composant. On peut ainsi définir tout type de fonctionnalités pouvant ensuite être injectée dans nos composants.

Techniquement un hook est aussi une fonction, comme les composants, mais sans JSX. Ils ne peuvent être appelés que dans des fonctions composant React.

Dans un premier temps nous allons faire la revue des hooks mis à disposition par React.

> Pour chacun, se référer à la documentation officielle de React pour avoir plus d'informations.

#### **`useState`**

Le hook `useState` permet de conserver la donnée d'un rendu à l'autre.

Un rendu signifie le fait de recalculer ce que retourne le composant, la partie JSX. Un composant React est une fonction donc pour faire un rendu, React appelle la fonction.

Par conséquent toutes les variables que l'on a déclaré à l'intérieur reprennent les valeurs initiales et les opérations faites à l'appel de fonction sont exécutées à nouveau.

```jsx
// Si React fait un rendu de ce composant alors {count} sera remis
// à 0 peu importe qu'on ai cliqué sur le bouton ou non.
// De plus, pour voir la mise à jour à l'IHM il faut que le composant soit
// rendu à nouveau, donc dans cet exemple il ne se passe rien.
function Counter() {
  let count = 0;
  const increment = () => (count = count + 1);
  return <button onClick={increment}>Valeur: {count}</button>;
}
```

Pour conserver cette valeur on va utiliser le hook d'état `useState`. Ce hook est une fonction qui retourne un tableau dont le premier élément est la valeur de l'état et le second est une fonction pour modifier la valeur de cet état.

> A noter également qu'une modification de l'état de `useState` entraîne automatiquement un nouveau rendu du composant, ce qui permet d'avoir la mise à jour à l'IHM.

```jsx
function Counter() {
  // Même si React fait un rendu de ce composant, on conservera
  // la valeur de {count} car elle est encapsulée dans le hook d'état.
  const [count, setCount] = useState(0); // On lui donne la valeur par défaut en argument.
  const increment = () => setCount(count + 1);
  return <button onClick={increment}>Valeur: {count}</button>;
}
```

Dans cet exemple on affiche bien la dernière valeur de `count`. A chaque fois qu'on clique sur le bouton la valeur de l'état est incrémentée de 1, le composant est rendu à nouveau (car on a modifié la valeur d'un état) et l'IHM se met à jour.

#### **`useRef`**

Il existe un deuxième hook d'état : `useRef`. La principale différence avec `useState` est que celui-ci ne déclenche pas de nouveau rendu du composant quand sa valeur est modifiée.

L'API de `useRef` change par rapport à `useState` également, on n'a plus un tableau mais un objet contenant une propriété `current` qu'on accède et modifie directement.

Si on reprend notre exemple de compteur avec avec un `useRef` :

```jsx
function Counter() {
  // Même si React fait un rendu de ce composant, on conservera
  // la valeur de {countRef} car elle est encapsulée dans le hook d'état.
  // CEPENDANT il n'y aura pas de nouveau rendu au changement de la valeur.
  const countRef = useRef(0); // On lui donne la valeur par défaut en argument.
  const increment = () => (countRef.current += 1);
  return <button onClick={increment}>Valeur: {countRef.current}</button>;
}
```

Dans ce cas la valeur de `countRef` est bien mise à jour et conservée d'un rendu à l'autre. Cependant modifier sa valeur ne déclenche pas de nouveau rendu, donc nous ne voyons aucun changement à l'IHM.

Faisons un composant avec deux compteurs. Un via `useState` et un autre via `useRef`.

```jsx
function Counter() {
  const [count1, setCount1] = useState(0);
  const count2 = useRef(0);

  const increment1 = () => setCount1(count1 + 1); // Nouveau rendu.
  const increment2 = () => (count2.current += 1); // Pas de rendu.

  return (
    <>
      <button onClick={increment1}>Count1: {count1}</button>
      <button onClick={increment2}>Count2: {count2.current}</button>
    </>
  );
}
```

Dans cet exemple quand on clique sur le bouton du `count1` il y a un nouveau rendu car on utilise `useState`, l'IHM est donc mise à jour et on voit la nouvelle valeur de `count1`. A l'inverse si ensuite on clique sur le bouton du `count2` il n'y a pas de rendu de déclenché car on utilise `useRef` alors on ne voit pas la modification de `count2` à l'IHM qui reste à 0. Si maintenant on clique à nouveau sur le bouton du `count1` l'IHM se met à jour et on voit que les valeurs des deux compteurs sont mises à jour : `count1` vaut maintenant 2 car on a cliqué 2 fois dessus et `count2` vaut 1.

> Prenez le temps de comprendre et assimiler cette notion de rendu qui est importante quand on développe en React.

> 💡 Tips pour savoir s'il faut utiliser une variable JS normale, useState ou useRef.
>
> ```
>  ----------------------------------------------------
>  | Je veux conserver la valeur à travers les rendus |
>  ----------------------------------------------------
>                  | oui                   non |
>                  v                           v
>  ---------------------------------    ---------------
>  | L'IHM doit se mettre à jour   |    | Variable JS |
>  | à chaque changement de valeur |    ---------------
>  ---------------------------------
>       | oui              non |
>       v                      v
>  ------------           ----------
>  | useState |           | useRef |
>  ------------           ----------
> ```

#### **`useEffect`**

Le hook `useEffect` n'est pas un hook d'état, c'est un hook d'effet. Il permet d'exécuter du code quand certains éléments (qu'on appelera dépendances du hook) ont changé.

Comme on l'a vu, quand React fait un rendu il appelle à nouveau la fonction composant. par conséquent il exécutera toutes les opérations à l'intérieur. Conservons notre exemple de compteur, cette fois avec deux valeurs utilisant `useState`.

```jsx
function Counter() {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);

  const increment1 = () => setCount1(count1 + 1);
  const increment2 = () => setCount2(count2 + 1);

  console.log("count1 a changé !", count1);

  return (
    <>
      <button onClick={increment1}>Count1: {count1}</button>
      <button onClick={increment2}>Count2: {count2}</button>
    </>
  );
}
```

On logue bien la nouvelle valeur de `count1` quand elle change. Mais on logue également sa valeur quand on modifie la valeur de `count2` car modifier `count2` entraine aussi un rendu du composant et donc nouvel appel de fonction et donc log.

Pour loguer uniquement au changement sur `count1` on peut utiliser un `useEffect` en mettant en dépendance `count1` mais pas `count2`. Dans ce cas là, à chaque rendu, React va déterminer si une des dépendances du `useEffect` a changé. Si oui alors le code à l'intérieur du hook sera exécuter, sinon il ne le sera pas.

```jsx
function Counter() {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);

  const increment1 = () => setCount1(count1 + 1);
  const increment2 = () => setCount2(count2 + 1);

  useEffect(() => {
    console.log("count1 a changé !", count1);
  }, [count1]); // Le tableau en second arguments représente les dépendances.

  return (
    <>
      <button onClick={increment1}>Count1: {count1}</button>
      <button onClick={increment2}>Count2: {count2}</button>
    </>
  );
}
```

> On peut bien sûr avoir plusieurs dépendances dans le tableau de dépendances. On peut même en avoir 0 ! Dans le cas où on donne un tableau vide, le code à l'intérieur du `useEffect` ne sera exécuté qu'une seule fois, au premier rendu du composant. Utile si on a besoin d'initialiser des choses.

#### **`useMemo`**

Le hook `useMemo` permet de mémoriser une valeur qui sera recalculer uniquement si une des dépendances passées au hook a changé. Ce hook peut être intéressant dans le cas où le calcul d'une valeur est couteux et on ne veut pas le faire à chaque rendu.

```js
// Imaginons que la somme de a et b est très longue...
const sum = useMemo(() => a + b, [a, b]);
```

La somme de `a` et `b` ne sera recalculé que si `a` ou `b` ont changé. Si dans le composant un rendu est déclenché par autre chose que ces deux là alors la somme ne sera pas recalculée.

#### **`useCallback`**

Le hook `useCallback` fait la même chose que `useMemo` mais pour mémoriser une fonction au lieu d'une valeur.

`useCallback(fn, deps)` est équivalent à `useMemo(() => fn, deps)`;

### Le router : naviguer entre les pages

> Le router est ce qui permet d'associer une URL à un composant React.

A l'inverse d'Angular, React n'intègre pas de router. Pour ça on va utiliser la librairie [react-router-dom](https://reactrouter.com/web/guides/quick-start).

On définit quelles sont les URLs accessibles à travers l'application.

```jsx
import { BrowserRouter, Switch, Route, Redirect } from "react-router-dom";

export default function App() {
  return (
    <BrowserRouter>
      <Switch>
        <Route exact path="/home">
          <HomePage />
        </Route>
        <Route exact path="/todoes">
          <TodoesPage />
        </Route>
        <Route path="/todoes/:todoId">
          <TodoPage />
        </Route>

        {/* If no match redirect to home */}
        <Redirect to="/home" />
      </Switch>
    </BrowserRouter>
  );
}
```

> Une convention intéressante est de suffixer nos composants utilisés dans le Router par `Page`.

On a définit trois pages différentes dans notre router :

- _/home_ affiche le composant `HomePage`,
- _/todoes_ affiche le composant `TodoesPage`,
- _/todoes/:todoId_ affiche le composant `TodoPage`.

Dans le dernier cas `:todoId` représente l'id de la tâche qu'on souhaite récupérer. On pourra ensuite récupérer cet id dans notre composant.

```jsx
import { useParams } from "react-router-dom";

export default function TodoPage() {
  const { todoId } = useParams();
  return <p>Todo id: {todoId}</p>;
}
```

Pour naviguer entre les pages on peut utiliser des liens via le composant `Link` ou alors via le code directement.

```jsx
import { useParams, useHistory, Link } from "react-router-dom";

export default function TodoPage() {
  const { todoId } = useParams();
  const history = useHistory();

  const redirect = () => history.push("/todoes");

  return (
    <>
      <p>Todo id: {todoId}</p>
      <Link to="/home">Go home</Link>
      <button onClick={redirect}>Go todoes</button>
    </>
  );
}
```

### La gestion des formulaires

TODO (librairie react-hook-form)

## Sujets avancés

Les différents éléments qui composent React ont été vu. Cette partie ne va pas présenter de nouveaux outils mais plutôt la façon dont utiliser ce que nous avons déjà vu. On va parler de bonnes pratiques.

### Les composants de présentation VS les composants métier

React ne propose qu'un seul type de composant. Un composant c'est un composant. Mais on peut découper les composants en deux catégories en fonction de à quoi ils vont servir. Certains, ne vont faire que de l'affichage et ne contiennent pas de logique, on les appelle des **composants de présentation**. D'autres, vont contenir de la logique, communiquer avec des API, etc, on les appelle des **composants métier**.

Faire cette différenciation est utile afin de maintenir un code facile à lire et plus évolutif. L'objectif étant d'avoir un maximum de composants de présentation et le moins de composants métier possible. Comme les composants de présentation n'ont aucune logique particulière ils sont aussi beaucoup plus facilement réutilisable. Limiter le nombre de composants métier permet également de centraliser la logique métier et de ne pas en avoir un peu partout ce qui peut complexifier la lisibilité du code.

De plus les composants de présentation sont assez faciles à tester par rapport aux composants métier.

#### **Composant de présentation**

Au niveau du code, un composant de présentation ne va pas s'occuper d'aller récupérer de la donnée. On lui la donne via des _props_ et il peut émettre des évènements via ces mêmes _props_.

#### **Composant métier**

A l'inverse un composant métier va aller chercher de la donnée, utiliser des services pour faire des requêtes HTTP, etc. Une fois la donnée récupérée, il la manipule si besoin et ce composant métier va pouvoir la fournir à ses composants enfants qui sont des composants de présentation.

#### **Découpage concret**

De manière générale dans mon code : les composants qui sont associés à une route (des composants "pages") sont toujours des composants métier. Ensuite j'essaie de découper la page en différent sous-composants de taille correcte (max 250 lignes) qui eux doivent être au plus possible des composants de présentation. Une exception assez courante : les composants qui englobent un formulaire. J'ai l'habitude de faire un composant dédié à un formulaire, ce composant n'est pas vraiment de présentation car il contient la logique du formulaire, mais pas vraiment métier non plus car il ne gère pas la donnée mais juste le formulaire.

> On a déjà vu dans les exemples précédents ce qui est expliqué ici. Si on reprend la todo list de la fin de la partie sur les composants. Alors on peut constater que le `TodoList` est un composant métier et `Task` est un composant de présentation.

Mais faisons un autre exemple pour bien visualiser le concept. Imaginons une application de suivi d'entraînement pour des athlètes, prenons le cas où sur une page de l'application l'athlète peut accéder à une liste de recettes pour son régime alimentaire. Le code pourrait ressembler à ce qui suit :

```typescript
// recipe.type.ts

export interface Recipe {
  name: string;
  preparationTime: number;
  ingredients: { name: string; qty: number; unit?: string }[];
  steps: string[];
}
```

```typescript
// recipe.http.ts

// Mock d'une requête http.
export async function getRecipes() {
  return [
    {
      name: "Crêpes sucrées",
      preparationTime: 15,
      ingredients: [
        { name: "farine", qty: 200, unit: "g" },
        { name: "lait", qty: 400, unit: "ml" },
        { name: "oeufs", qty: 3 },
        { name: "sucre", qty: 2, unit: "c.a.s" },
        { name: "beurre", qty: 35, unit: "g" },
        { name: "rhum", qty: 1, unit: "c.a.s" },
        { name: "huile d'olive", qty: 1, unit: "c.a.s" },
      ],
      steps: [
        "Battre les oeufs dans un bol",
        "Faire fondre le beurre",
        "Verser la farine dans un saladier et y faire un puit",
        "Ajouter le sucre, les oeufs et le beurre puis mélanger",
        "Verser le lait petit à petit tout en mélangeant",
        "Ajouter le rhum et mélanger",
        "Laisser reposer la pâte une heure ou deux",
        "Dans une poêle bien chaude, étaler un peu d'huile d'olive, y verser et répartir une louche de pâte",
        "Quand les bords sont dorés, retourner la crêpe",
        "Encore 2 minutes puis profiter",
      ],
    },
  ];
}
```

```tsx
// Recipes.tsx

export default function Recipes() {
  const [recipes, setRecipes] = useState<Recipe[]>([]);

  // Récupération des recettes au premier rendu.
  useEffect(() => {
    getRecipes().then((data) => setRecipes(data));
  }, []);

  return (
    <div className="recipes">
      {recipes.map((r) => (
        <Recipe key={r.name} recipe={r} />
      ))}
    </div>
  );
}
```

```tsx
// Recipe.tsx

type Props = {
  recipe: Recipe;
};

export default function Recipe({ recipe }: Props) {
  if (!recipe) {
    return null;
  }

  return (
    <div className="recipe">
      <h4 className="recipe__name">{recipe.name}</h4>
      <span className="recipe__time">{recipe.preparationTime} minutes</span>

      <ul className="recipe__ingredients">
        {recipe.ingredients.map((ingredient) => {
          return (
            <li key={ingredient.name} className="recipe__ingredient">
              {ingredient.name} / {ingredient.qty} {ingredient.unit}
            </li>
          );
        })}
      </ul>

      <div className="recipe__steps">
        {recipe.steps.map((step) => {
          return (
            <p key={step} className="recipe__step">
              {step}
            </p>
          );
        })}
      </div>
    </div>
  );
}
```

L'exemple est simple donc on peut se dire qu'on aurait pu tout mettre dans un seul composant. C'est vrai, l'idée ici était de montrer la différence entre composants métier et présentation.

On pourrait imaginer que l'athlète peut noter la recette avec des étoiles, on aurait alors un système avec de notation au niveau du composant de présentation qui, quand l'athlète clique sur une étoile, remonte un évènement via une _props_ au composant métier qui lui se chargera de faire un appel à l'API pour prendre en compte le vote, etc.

> Avec un peu d'expérience cette distinction se fera naturellement et vous ferez vos découpages dans votre tête facilement avant d'entammer la moindre ligne de code pour déjà visualiser comment le tout va s'articuler.

### Comment communiquer entre les composants ?

On a vu que découper notre application en composants était une bonne idée. Mais si nous avons pleins de composants, il va bien falloir trouver un moyen de les faire communiquer entre eux. Dans un premier temps on peut se dire qu'on sait déjà faire communiquer les composants avec les _props_.

#### **Les props**

Les _props_ sont là pour faire communiquer les composants entre eux, en passant de la donnée dans un sens (parent vers enfant) et en passant des fonctions appelées sur évènement dans l'autre sens (enfant vers parent).

Pour des communications directes de parent à enfant c'est un système qui fonctionne très bien, simple à mettre en place. Cependant (représentons les liens entre composants sous forme d'arbre) si on souhaite faire communiquer des composants qui ne sont pas directement parent et enfant, s'il y a des composants intermédiaires, ou pire, s'ils ne sont même pas sur la même branche de l'arbre. Alors il faudra faire des séries de remontées et redescentes de données à travers les _props_ de tous les composants intermédiaires pour arriver à nos fin. Autant dire que niveau maintenabilité et lisibilité du code on a vu mieux.

Alors comment partager facilement de la donnée peu importe où se trouve les composants ? Avec les contextes React ou un système de gestion d'état global comme Redux ou Jotai.

> De manière générale j'opte pour l'option des _props_ quand le lien de parenté est direct ou s'il y a un composant intermédiaire. Pas plus. Sinon l'option d'un contexte React semble la plus adaptée.

#### **Les contextes React**

Les contextes React permettent de partager de la donnée entre n'importe quels composants qui descendent du contexte.

Les avantages des contextes React par rapport à des librairies comme Redux :

- déjà intégré avec React, pas de supplément à installer,
- les contextes ne sont pas globaux, on peut les mettre où on veut dans l'arbre de composants pour que la donnée qu'ils stockent soit au plus prêt des composants qui l'utilise.

Prenons le cas des données du profil utilisateur de la personne connectée. Ce sont typiquement des données utilisées à plusieurs endroits de l'application, dans des composants qui ne sont pas directement parent/enfant.

> Code inspiré des articles de Kent C Dodds sur le sujet des contextes React.

```jsx
// Création du contexte React qui contiendra la donnée.
const UserContext = createContext();

/**
 * Création du provider associé au context.
 * Le rôle du provider est de permettre d'accéder au contenu
 * du contexte dans les composants.
 *
 * @params {ReactNode} children Les composants qui peuvent utiliser le context.
 */
function UserProvider({ children }) {
  // Etat contenant la donnée utilisateur.
  const [user, setUser] = useState();
  // Construction de la donnée que l'on va donner au contexte.
  // On utilise `useMemo` pour limiter le nombre de rendus à
  // uniquement quand user a changé.
  const value = useMemo(() => ({ user, setUser }), [user]);

  // On fournit au context la valeur contenant nos données utilisateur.
  return <UserContext.Provider value={value}>{children}</UserContext.Provider>;
}

/**
 * Hook custom qui sert de wrapper pour le contexte.
 * On peut à cet endroit y ajouter des fonctions pour faire des actions
 * particulières sur le contexte.
 *
 * Utilisation dans les composants :
 * const { user, setUser } = useUser();
 */
function useUser() {
  const context = useContext(UserContext);
  if (!context) throw new Error("useUser must be used within a UserContext");
  return context;
}

export { UserProvider, useUser };
```

Une fois notre contexte créé il ne reste plus qu'à ajouter son `provider` dans notre arbre de composants pour pouvoir utiliser le hook qu'on a créé.

```jsx
export default function App() {
  return (
    <UserProvider>
      <UserName />
    </UserProvider>
  );
}
```

```jsx
export default function UserName() {
  const { user } = useUser();
  if (!user) return null;
  return <strong>{user.name}</strong>;
}
```

Le hook `useUser` pourra être utlisé par n'importe quel composant qui descend du composant `UserProvider`. De cette manière on s'abstrait de devoir passer les données à travers les _props_ de chaque composant intermédiaire.

Cette solution est intéressante car le surplus de code reste réduit et facile à lire. Attention cependant à ne pas tout mettre dans les contextes React ! Les contextes sont utiles quand la donnée doit transitée à plusieurs endroits différents. Si la donnée reste utilisée de manière locale à quelques composants qui sont parents enfants alors la solution avec les _props_ est selon moi plus adaptée car il y a moins de complexité dans le code et le flux de données est plus facile à suivre et à se représenter.

> Cette réflexion est aussi valable pour des librairies comme Redux ou Jotai. Certes elles enlèvent la problématique du passage par de multiples composants mais ajoute pas mal de code, de complexité et de connaissances nécessaire à la compréhension du fonctionnement de l'application.
>
> Dans une grand majorité des cas, les deux solutions _props_ et contextes React suffisent sans avoir besoin d'installer une librairie supplémentaire.

## Pour aller plus loin

Après avoir lu ce guide, vous devriez être capable de développer par vous même des applications React.

Pour aller plus loin vous pouvez aller voir le blog de [Kent C Dodds](https://kentcdodds.com/blog) qui partage ses connaissances sur de nombreux aspects de React et notamment sur la partie de tests qui n'est pas abordée ici.
