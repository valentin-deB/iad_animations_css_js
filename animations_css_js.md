# Animations CSS / JS

## Vendor prefixes et feature detection

Même si le support des navigateurs est excellent, [animations](https://caniuse.com/#search=animations) et [transformations](https://caniuse.com/#search=transforms) ne sont pas toujours implémentées nativement dans certains navigateurs.

Pour différencier les propriétés CSS encore en développement de celles qui font partie des recommandations du W3C, les divers navigateurs ont utilisé des extensions propriétaires qui se placent devant les propriétés CSS. Aujourd'hui, les choses ont changées et [les navigateurs utilisent d'avantage des flags](http://demosthenes.info/blog/217/CSS-Vendor-Prefixes-and-Flags).

Des outils comme [Autoprefixer](https://github.com/postcss/autoprefixer) peuvent vous aider à être certain de ne rien oublier. Des ressources comme [caniuse](http://caniuse.com/) et [html5please](http://html5please.com/) vous donneront quantités d'informations précieuses. Vous pouvez également utiliser des [feature queries](https://developer.mozilla.org/en-US/docs/Web/CSS/%40supports) avec `@supports`.

## Transformations CSS

Les transformations CSS et l'opacité sont les deux propriétés les plus a utilisées pour des transitions et des animations. Animer seulement ces propriétés [permet de rester à 60 frames par seconde](https://medium.com/outsystems-experts/how-to-achieve-60-fps-animations-with-css3-db7b98610108) dans la mesure où les navigateurs utilisent le GPU.

Utiliser la propriété [`will-change`](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change) en CSS ou en javaScript permet au navigateur de préparer les transitions et ainsi d'améliorer les performances.

### Transformations 2D

Avec CSS3, nous disposons maintenant de propriétés nous permettant de faire subir des transformations aux éléments HTML. Ces propriétés sont supportées par [la plupart des navigateurs récents](http://caniuse.com/#feat=transforms2d).

L’ensemble de ces transformations ont lieu après que la page soit rendue et n’influencent donc pas le rendu de la page.

#### Translate

Effectue une translation, soit sur l’axe horizontal, soit sur l’axe vertical, soit sur les deux. Les valeurs spécifiées peuvent être positives ou négatives.

```css
.myElement {
  transform: translateX(100px);
}

.myElement {
  transform: translateY(20%);
}

.myElement {
  transform: translate(100px, 20%);
}
```

#### Scale

Effectue une mise à l’échelle. Cette mise à l’échelle peut concerné la hauteur ou la largeur d’un élément ou les deux. L’argument ne prend pas ici d’unité de mesure, il s’agit d’un ratio par rapport à la taille par défaut de l’élément.

```css
.myElement {
  transform: scaleX(1.5);
}

.myElement {
  transform: scaleY(1.5);
}

.myElement {
  transform: scale(1.5);
}
```

#### Rotate

Effectue une rotation. Les valeurs spécifiées peuvent être positives ou négatives. Ces valeurs peuvent être spécifiées en degrés ou en nombre de tours

```css
.myElement {
  transform: rotate(2turn);
}

.myElement {
  transform: rotate(45deg);
}
```

#### Skew

Effectue une distorsion de type “skew” spécifiée en degrés. Celle-ci peut être appliquée selon les axes horizontaux ou verticaux ou encore selon les deux à la fois. Les valeurs spécifiées peuvent être positives ou négatives.

```css
.myElement {
  transform: skewY(30deg);
}
```

#### Transform-origin

Par défaut, ces transformations prennent généralement le coin supérieur droit de la bounding-box de l’élément comme point de référence. La propriété `transform-origin` permet de modifier ce point de référence.

```css
.myElement {
  transform-origin: 0 50%;
}
```

**Note**: Firefox ne supporte pas toujours bien la propriété `transform-origin` lorsqu'elle est exprimée en pourcentages et lorsque les transformations sont appliqués à des fichiers SVG. Pour contourner ce problème, vous pouvez la spécifier en pixels par exemple.

#### Chaining

Nous pouvons également combiner différentes transformation en les chaînant et en les séparant par un espace. Les navigateurs appliquent ces diverses transformations successivement en commençant par la gauche.

```css
.myElement {
  transform: rotate(45deg) scale(2);
}
```

_Exercice: expérimenter avec les transformation 2D en :hover_

### Transformations 3D

L'une des notions les plus importantes à comprendre est celle de perspective. [Chris Coyier vous en donne un bon aperçu sur CSS Tricks](http://css-tricks.com/almanac/properties/p/perspective/).

En bref, la perspective est la profondeur de l'axe Z, la distance entre un objet situé sur celui-ci et l'utilisateur. Plus ce chiffre est petit, plus la perspective est importante. Plus cette valeur est grande, plus l'effet sera subtil.

La perspective peut être spécifiées de deux façons:

#### Via la propriété `transform` directement sur l'élément concerné

Dans ce cas, chaque élément concerné possède son propre "vanishing point".

```css
.myElement {
  transform: perspective(300px) rotateY(20deg);
}
```

Si vous utilisez `transform: perspective(xxx)` sur un élément, veillez bien à l'utiliser **avant** d'avoir spécifié votre propriété `transform` dans votre CSS.

Ne fonctionne pas:

```css
.myElement {
  transform: rotateY(20deg) perspective(300px);
}
```

Fonctionne:

```css
.myElement {
  transform: perspective(300px) rotateY(20deg);
}
```

#### Via la propriété `perspective` sur l'élément parent

Dans ce cas, cela affecte l'ensemble des enfants du parent, qui partagent alors tous le même "vanishing point".

```css
.myParentElement {
  perspective: 300px;
}
```

Attention, la perspective n'affecte que les enfants directs. Si vous devez utiliser la même perspective pour des éléments qui sont des descendants plus lointains, vous pouvez utiliser la propriété `transform-style` qui peut prendre les valeurs `flat` ou `preserve-3d`. La seconde valeur permet d'étendre le contexte 3D à tous les descendants du parent auquel la `perpective` aura été appliquée.

[La plupart des transformations 2D ont leur équivalent en 3D](http://css-tricks.com/almanac/properties/t/transform/). Vous retrouverez également la propriété `transform-origin` vue plus haut.

```css
.myElement {
  transform: rotateX(50deg);
}

.myElement {
  transform: translateZ(50px);
}

.myElement {
  transform: scaleZ(200px);
}
```

Il existe également des notations courtes qui requièrent des valeurs pour les trois dimensions:

```css
.myElement {
  transform: translate3d([x], [y], [z]);
  transform: scale3d([x], [y], [z]);
  transform: rotate3d([x], [y], [z], [angle]);
}
```

Avec `rotate3d`, vous spécifiez simplement quels axes de rotation sont activés (valeurs 0 ou 1) et vous définissez ensuite l'angle à appliquer.

Avec les transformations 3D, vous pouvez placer certains éléments de telle façon que leur "avant" ne fasse plus face à l'écran. Par exemple avec une rotation:

```css
.myElement {
  transform: rotateY(180deg);
}
```

Dans ce cas la propriété `backface-visibility` permet de gérer la visibilité des faces d'un élément lorsqu'elles ne font pas face à l'écran. Les valeurs possibles sont `visible` et `hidden`.

```css
.myElement {
  transform: rotateY(180deg);
  backface-visibility: visible;
  /*backface-visibility:hidden;*/
}
```

## Transitions en CSS

Les [transitions](https://developer.mozilla.org/en-US/docs/Web/CSS/transition) permettent au navigateur de gérer la transition entre deux états d’un élément spécifiés par CSS. Voici [une liste des propriétés CSS pouvant être animées](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties).

Les transitions CSS sont souvent utilisées pour "lisser" les transitions entre deux états d'éléments d'interface: boutons (default et hover ou touch), apparitions successives des éléments d'un menu, etc.

Les propriétés permettant de gérer les transitions sont `transition-property`, `transition-duration`, `transition-delay`, `transition-timing-function`.

```css
.myElement {
  transition-property: background-color;
  transition-duration: 0.2s;
  transition-delay: 0.1s;
  transition-timing-function: ease-out;
}
```

Il est également possible d’utiliser une notation courte:

```css
.myElement {
  transition: all 2s 0.2s ease-out;
}
```

Vous pouvez éventuellement combiner diverses transitions, en notation étendue comme en notation courte.

```css
.myElement {
  transition-property: background-color, transform;
  transition-duration: 0.25s, 0.5s;
  transition-delay: 0.1s, 0.2s;
  transition-timing-function: ease-out, ease-in;
}
```

ou

```css
.myElement {
  transition: background-color 0.25s 0.1s ease-in, transform 0.5s 0.2s ease-out;
}
```

**Note:** la propriété `transition-timing-function` peut également être exprimée avec des courbes de Bezier pour plus de précision. Lea Verou a réalisé un [outil en ligne](http://cubic-bezier.com/) vous permettant de les calculer et de les visualiser facilement. A voir aussi, [Caeser](http://matthewlein.com/ceaser/) par Matthew Lein ou tout simplement les outils de dévelopement dans Chrome ou Firefox.

_Exercice: expérimenter avec les transitions en :hover_

Les transitions sont également souvent déclenchées à l'aide de JavaScript. Pour cela, il vous faudra sélectionner vos éléments ([querySelector](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector), [querySelectorAll](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll)) et ajouter ou supprimer des classes dans votre HTML ([API classList](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList)) lors du déclenchement d'un événement ([eventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventListener)), par exemple un click.

Voyons ensemble comment créer un menu de navigation déclenché par un click sur une icône "hamburger". Ce menu va venir "pousser" le contenu de la page.

_Exercice: créer un menu responsive avec déclenchement au click (querySelector, querySelectorAll, eventListener, classList)_

Les transitions CSS sont faciles à mettre en oeuvre et à manipuler via JavaScript. Elles permettent de réaliser une certaine palette d'effets mais elles ont également leurs limitations:

- les transitions se font toujours d'un état A vers un état B, sans étapes intermédiaires. Pour contrôler plus finement les étapes, il faut se tourner vers les animations CSS.
- les transitions ne peuvent pas effectuer de loop ou d'itération. Si vous souhaitez une animation en boucle ou ayant plusieurs itérations, il faut vous tourner vers les animations.
- les transitions ne sont pas réutilisables, les animations peuvent être appliquées à plusieurs éléments une fois définies.

## Animations en CSS3

Voyons maintenant comment créer de véritables animations en CSS3. Le processus comporte deux étapes distinctes.

1. Définir votre animation
2. L'assigner à un ou plusieurs éléments HTML

### Définir vos animations avec `@keyframes`

Vous pouvez nommer votre animation et décrire les étapes qui la composent en utilisant l'élément `@keyframes`. Celle-ci défini les propriétés qui doivent être modifiées et à quel moment elles doivent l'être dans le déroulement de votre animation.

Les étapes de votre animations peuvent soit être décrites à l'aide des mots-clés `from` et `to`, soit à l'aide de valeurs en pourcentage. Ces dernières sont utiles si vous avez plus de deux étapes et/ou si vous avez besoin d'une progression non-linéaire.

```css
@keyframes move {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(400px);
  }
}
```

```css
@keyframes move {
  0% {
    transform: translateX(0);
  }
  20% {
    transform: translateX(100px);
  }
  100% {
    transform: translateX(400px);
  }
}
```

Note: si vous ne spécifiez pas de de `keyframes` à 0% ou 100%, les styles originaux appliqués à votre élément seront utilisés.

### Assigner l'animation à un ou plusieurs éléments HTML

Vous allez maintenant assigner cette animation à un élément HTML et en définir les caractéristiques pour cet élément. Cela se fait à l'aide des propriétés suivantes

- `animation-name`: spécifie le nom de l'animation à appliquer (celui que vous avez spécifié dans votre élément `@keyframes`)
- `animation-duration`: défini la durée de l'animation. Cette propriété est exprimée en secondes `s` ou en milisecondes `ms`.
- `animation-timing-function`: défini le easing de votre animation: `ease-in`, `ease-out`, `linear` ou encore `cubic-bezier(0.1, 0.7, 1.0, 0.1)` sont des valeurs possibles. Par défaut, la valeur est `ease`.
- `animation-iteration-count`: défini le nombre de fois que l'animation doit être effectuée. Par défaut, la valeur est 1. Cette valeur peut également être définie comme `infinite`.

`@keyframes`, `animation-name` et `animation-duration` sont suffisants mais il vaut mieux toujours définir explicitement les deux autres.

_Exercice: réaliser plusieurs nuages qui traversent l'écran (une seule animation @keyframes) et expérimenter avec les diverses propriétés vues ci-dessus. Attention, des vendor-prefixes sont encore nécessaires pour les navigateurs webkit tels que chrome ou Safari. [Chris Coyier vous en détaille l'utilisation dans un article sur CSS-Tricks](http://css-tricks.com/snippets/css/keyframe-animation-syntax/)_

### Différentes autres propriétés

#### `animation-delay`

`animation-delay` défini le délai avant lequel l'animation se déclenche. Spécifié en secondes `s` ou milisecondes `ms`.

#### `animation-fill-mode`

`animation-fill-mode` défini la manière dont se comporte l'élément animé une fois l'animation terminée. Les valeurs possibles sont:

- `none`: l'élément retrouve ses caractéristiques originales une fois l'animation terminée
- `forwards`: l'élément conserve les caractéristiques de la dernière frame de l'animation
- `backwards`: l'élément possède les caractéristiques de la première frame de l'animation pendant la durée de `animation-delay`. Cette valeur est donc particulièrement utile en travaillant avec des délais.
- `both`: combine les effets de `backwards` et `forwards`.

_Exercice: expérimenter avec les diverses valeurs de `animation-fill-mode`._

#### `animation-direction`

`animation-direction` défini la direction dans laquelle se déroule l'animation.

- `normal`: l'animation se déroule de la première frame définie jusqu'à la dernière. C'est la valeur par défaut.
- `reverse`: l'animation se déroule de la dernière frame définie jusqu'à la première.
- `alternate`: ne peut être utilisé que lorsque votre propriété `animation-count` est supérieure à 1. La première fois que l'animation est exécutée, elle se déroule en mode `normal`, la seconde fois en mode `reverse`, etc.
- `alternate-reverse`: identique à `alternate` hormis que l'animation se déroule d'abord en mode `reverse`.

_Exercice: expérimenter avec les diverses valeurs de `animation-direction`._

## Notation courte et chaining

Une notation courte existe évidemment pour appliquer vos animations à un élément HTML.

```css
animation: myAnimation 0.5s ease-in 1s 3;
```

Dans l'ordre: <`animation-name`> <`animation-duration`> <`animation-timing-function`> <`animation-delay`> <`animation-iteration-count`>.

Il est également possible de chaîner plusieurs animations sur un même élément.

```css
animation: myAnimation 1s ease-in-out 2s 4, myOtherAnimation 4s ease-out 2s;
```

### Exercices

- Voitures roulant à travers l'écran à différentes vitesses et dans différents sens
- Animation image par image avec un sprite et `steps`. La marche à suivre est ici de réaliser un sprite. Créer une animations `@keyframes` allant du haut du sprite au bas du sprite à l'aide `background-position`. Enfin, spécifier un nombre d'étapes correspondant aux nombre d'images fixes dans le sprite.

```css
@keyframes fly {
  /* keyframe implicite: background-position:0 0; */

  100% {
    background-position: 0 -400px;
  }
}

.bird {
  position: absolute;
  top: 20px;
  left: 20px;
  width: 200px;
  height: 100px;
  background: url(../img/bird_sprite.png) 0 0 no-repeat;

  animation: fly 0.5s steps(4) infinite;
}
```

### Démarrer et arrêter une animation avec `animation-play-state`

Vous pouvez facilement définir vos animations, les assigner à vos éléments HTML et en contrôler l'état avec `animation-play-state` qui peut avoir deux valeurs: `running` (default) et `paused`.

Ces propriétés peuvent être modifiées facilement en CSS avec des pseudo-classes comme `:hover` ou en utilisant JavaScript ([querySelector](https://developer.mozilla.org/fr/docs/Web/API/document.querySelector), [querySelectorAll](https://developer.mozilla.org/en-US/docs/Web/API/Document.querySelectorAll), [addEventListener](https://developer.mozilla.org/fr/docs/DOM/element.addEventListener) et [classList](https://developer.mozilla.org/fr/docs/DOM/element.classList)).

_Exemple: `animation-play-state` et `:hover`_

```css
@keyframes spin {
  0% {
    transform: rotate(0);
  }
  100% {
    transform: rotate(1turn);
  }
}

.windmill {
  animation: spin 5s linear infinite;
  animation-play-state: paused;
}

.windmill:hover {
  animation-play-state: running;
}
```

_Exemple: `animation-play-state` et classes manipulées via JavaScript._

```css
@keyframes spin {
  0% {
    transform: rotate(0);
  }
  100% {
    transform: rotate(1turn);
  }
}

.sticker {
  animation: spin 5s linear infinite;
  animation-play-state: paused;
}

.sticker.is-animated {
  animation-play-state: running;
}
```

### Déclenchement au scroll

Avec l'aide Javascript, les transitions et animations CSS peuvent facilement être [déclenchées au scroll](http://dogstudio.be).

[`IntersectionObserver`](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver) est une API native qui permet facilement de détecter si un ou plusieurs éléments sont en intersection avec d'autres éléments ou avec le viewport du navigateur pour déclencher des animations via quelques changements de classes CSS. Voici [une petite démonstration](https://jeromecoupe.github.io/onscroll_css_animations/) rapide.

_Exercice: décortiquer le script et voir comment CSS et JS interagissent_

## Filtres

Les filtres peuvent servir à appliquer des effets simples sur des élements du DOM, typiquement des images. Les filtres fonctionnent à l'aide de la propriété `filter` pour laquelle on spécifie comme valeur un type de filtre et une valeur.

Vous pouvez vous référer à MDN pour une [liste complète des filtres disponibles](https://developer.mozilla.org/fr/docs/Web/CSS/filter).

Il est également possible de réaliser des filtres plus complexes à l'aide de SVG et de les appliquer à votre contenu HTML. Voici un [bon tutoriel sur le sujet par Sara Soueidan](https://www.sarasoueidan.com/blog/svg-filters/).

**exemples**

```css
.u-grayscale {
  filter: grayscale(100%);
}

.u-blur {
  filter: blur(3px);
}
```

## Blend modes

Les propriétés `background-blend-mode` et `mix-blend-mode` permettent de modifier vos images de façon importante, comme vous le feriez avec des calques dans une application graphique. `background-blend-mode` se charge faire un blend mode entre deux backgrounds, tandis que `mix-blend-mode` est utilisé pour gérer un blend mode entre un élément et un autre.

```css
/* mix blend mode */
.o-blended-red {
  display: inline-block;
  background-color: red;
}

.o-blended-red > img {
  filter: grayscale(1);
  mix-blend-mode: multiply;
  vertical-align: middle;
}
```

Vous pouvez vous référer à MDN pour une liste complète des [`background-blend-mode`](https://developer.mozilla.org/fr/docs/Web/CSS/background-blend-mode) et des [`mix-blend-mode`](https://developer.mozilla.org/fr/docs/Web/CSS/mix-blend-mode) disponibles.

## Clips et masques

Clipping et masking peuvent également aider à apporter un peu de variété à vos images. Ces deux principes se ressemblent en ce qu'ils servent tous les deux à cacher certaines parties d'un élément. Le support au niveau des navigateurs n'est pas identique mais [voici une page de test par Yoksel](https://codepen.io/yoksel/full/fsdbu/) pour vérifier par vous mêmes.

- **masques**: sont des images. Avec `mask-mode: luminance;` les parties noires du masque sont cachés, les parties blanches sont visibles. Avec `mask-mode: alpha;` les parties opaques du masque sont visibles et les parties transparentes cachées.
- **clips**: sont des formes. Ce qui est à l'intérieur de la forme est visible

Voici un bon [résumé des choses sur CSS-Tricks](https://css-tricks.com/clipping-masking-css/). Comme cela date un peu, je vous invite à également regarder l doc de MDN à ce sujet: [`mask`](https://developer.mozilla.org/en-US/docs/Web/CSS/mask) et [`clip-path`](https://developer.mozilla.org/en-US/docs/Web/CSS/clip-path).

```css
/* appliqué à une <img> */
.masked {
  -webkit-mask-image: url(../img/masks-scribbles.svg);
  mask-image: url(../img/masks-scribbles.svg);
  -webkit-mask-repeat: no-repeat;
  mask-repeat: no-repeat;
}
```

```css
/* appliqué à une <img> (ne fontionne qu'avec un svg inclus dans le document pour Safari, Chrome et Opera) */

/* SVG externe / lié au document */
.clipped-svg {
  -webkit-clip-path: url(../img/clip.svg#myClip);
  clip-path: url(../img/clip.svg#myClip);
}

/* SVG interne au document */
.clipped-svg {
  -webkit-clip-path: url(#myClip);
  clip-path: url(#myClip);
}
```

```svg
<svg width="137" height="130" viewBox="0 0 137 130" xmlns="http://www.w3.org/2000/svg">
<title>Star</title>
<defs>
  <clipPath id="myClip">
    <path fill="#D8D8D8" d="M68.5 107.25l-42.027 22.095L34.5 82.547.5 49.405l46.987-6.827L68.5 0l21.013 42.578 46.988 6.827-34 33.142 8.026 46.798z" fill-rule="evenodd"/>
  </clipPath>
</defs>
</svg>
```

```css
/* appliqué à une <img> */
.clipped-polygon {
  -webkit-clip-path: polygon(
    20% 0%,
    0% 20%,
    30% 50%,
    0% 80%,
    20% 100%,
    50% 70%,
    80% 100%,
    100% 80%,
    70% 50%,
    100% 20%,
    80% 0%,
    50% 30%
  );
  clip-path: polygon(
    20% 0%,
    0% 20%,
    30% 50%,
    0% 80%,
    20% 100%,
    50% 70%,
    80% 100%,
    100% 80%,
    70% 50%,
    100% 20%,
    80% 0%,
    50% 30%
  );
}
```

[Clippy de Bennett Feely](https://bennettfeely.com/clippy/) est un petit outil qui vous permettra de créer facilement des clip paths CSS.

```css
/* appliqué à une <img> */
.c-gradient-text {
  background-image: linear-gradient(to right, #e03d52, #ffb25b);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}
```

Les SVG et les polygones étant animables, ainsi que les images utilisées comme background, il est possible de réaliser des masques animés en CSS ou en JS.

_Exercice: expérimenter avec clipping et masques dans Figma et en code_

## Animations JavaScript

Les animations en Javascript offrent bien plus de contrôle que les animations CSS si vous avez besoin d'interactivité, d'effets poussé ou de séquences d'animations chainées les unes aux autres.

Des librairies telles que [GSAP de Greensock](https://greensock.com/gsap) offrent une grande facilité d'utilisation et permettent de créer des animations complexes avec SVG, HTML ou Canvas. Ces librairies sont extérieures aux navigateurs mais offrent une grande palette de possibilités aux dévelopeurs. En voici un petit [exemple avec un formulaire de login](https://github.com/jeromecoupe/web_animations_demo).

Au niveau des navigateurs justement, [Web animation API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API) est un standard qui se développe bien et dont les fonctions de bases bénéficient d'un bon support dans les navigateurs récents. Cette spécification est déjà intéressante à utiliser aujourd'hui et deviendra encore plus importante lorsque le support des navigateurs augmentera pour les fonctions avancées (séquences et timeline). Il est également possible d'utiliser `requestAnimationFrame`, qui est plus complexe à gérer au niveau performance mais vous permet de créer à peu près n'importe quelle animation.

L'animation est devenue une part importante des interfaces et du web en général, en partie parce que nous y sommes habitués sur les plateformes mobiles natives comme iOS ou Androïd.

Comme à son habitude, le web intègre cela et avance en direction d'une expérience utilisateur plus riches dans lesquelles l'animation devient de plus en plus importante.

Comme dit plus haut, la librairie [Greensock / GSAP](https://greensock.com/) est le standard du moment et offre les avantages suivants:

- facile d'utilisation
- attention accordée à la performance (versions light et max)
- fonctionnalités impressionnantes
- bonnes ressources et tutoriaux
- possibilité d'[animer également les SVG](https://greensock.com/svg-tips)
- gestion des inconsistances dans les navigateurs.

Voici le HTML et le CSS utilisés pour quelques exemples très simples

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Animations GSAP</title>
    <link rel="stylesheet" href="css/main.css" />
  </head>
  <body>
    <div class="box  js-box"></div>

    <!-- lib -->
    <script src="https://cdn.jsdelivr.net/npm/gsap@3.0.1/dist/gsap.min.js"></script>

    <!-- script -->
    <script src="js/anims.js"></script>
  </body>
</html>
```

```css
.box {
  width: 50px;
  height: 50px;
  background-color: red;
}
```

Au niveau du code JavaScript, commençons par des Tweens simples. GSAP vous permet de réaliser des Tweens de deux façons différentes:

- `to`: utilise les caractéristiques de l'élément comme première frame du tween
- `from`: utilise les caractéristiques de l'élément comme dernière frame du tween

Dans tous les exemples donnés ici, nous utiliserons des transformations CSS3 pour les déplacements, dans la mesure où elles n'ont pas d'impact sur les autres éléments de la page, utilisent la carte graphique plutôt que le processeur et causent un minimum de repaints de la part du navigateur.

### Tweens avec GSAP

```js
const myBox = document.querySelector(".js-box");

// To tween
// utilise les caractéristiques de départ
// spécifie les caractéristiques d'arrivée
gsap.to(myBox, {
  rotation: 180,
  x: 100,
  duration: 1,
  backgroundColor: "#0000FF"
});
```

```js
const myBox = document.querySelector(".js-box");

// From tween
// utilise les caractéristiques d'arrivée
// spécifie les caractéristiques de départ
gsap.from(myBox, {
  rotation: 180,
  x: 100,
  duration: 1,
  backgroundColor: "#0000FF"
});
```

```js
const myBox = document.querySelector(".js-box");

// toFrom tween
// spécifie les caractéristiques d'arrivée et de départ
gsap.fromTo(
  myBox,
  {
    rotation: 21,
    backgroundColor: "#00FF00"
  },
  {
    delay: 1,
    rotation: 180,
    x: 250,
    duration: 1,
    backgroundColor: "#0000FF"
  }
);
```

```js
const myBox = document.querySelector(".js-box");

// Using set
// spécifie les caractéristiques d'arrivée et de départ
gsap.set(myBox, {
  rotation: 21,
  backgroundColor: "#00FF00"
});

gsap.to(myBox, {
  delay: 1,
  rotation: 180,
  x: 250,
  duration: 1,
  backgroundColor: "#0000FF"
});
```

Avec GSAP, nous pouvons donc modifier différentes propriétés CSS à la fois et animer quasiment toutes les propriétés CSS, même si il est conseillé de se limiter le plus possible a `transfrom` et `opacity` pour des raisons de performance. La notation est souvent la même que celle des propriétés CSS mais en `camelCase`.

Vous avez également à votre disposition une [large bibliothèque de fonctions d'Easing](https://greensock.com/docs/Easing) et vous pouvez définir vos propres effets avec [CustomBounce](https://greensock.com/docs/Easing/CustomBounce), [CustomEase](https://greensock.com/docs/Easing/CustomEase) et [CustomWiggle](https://greensock.com/docs/Easing/CustomWiggle).

```js
const myBox = document.querySelector(".js-box");

// To tween
gsap.to(myBox, {
  x: 100,
  ease: "elastic. out(1, 0.2)",
  duration: 0.5
});
```

La gestion des délais est également facilitée.

```js
// add delay
const myBox = document.querySelector(".js-box");

gsap.to(myBox, {
  x: 100,
  ease: "elastic. out(1, 0.2)",
  duration: 0.5,
  delay: 1
});
```

Si vous avez plsusieurs éléments à animer, la fonction `stagger` est très pratique.

```js
// stagger
const myBoxes = document.querySelectorAll(".js-box");

// using stagger
gsap.to(myBoxes, {
  stagger: 0.1,
  rotation: 180,
  x: 200
});
```

### Timeline avec GSAP

L'un des avantages centraux de GSAP est que cette librairie vous permet facilement de gérer des animations complexes grâce à un concept de timeline. Modifier les timings d'une animation complexe avec des éléments en successions et survenant en même temps devient un exercice relativement simple.

```js
const myBox = document.querySelector(".js-box");

// simple timeline with parameters
let tl = gsap.timeline({ repeat: -1, yoyo: true });
tl.pause();

tl.to(myBox, {
  x: 200,
  duration: 0.5
});

tl.to(myBox, {
  backgroundColor: "blue",
  rotation: 360,
  duration: 0.1,
  repeat: 5
});

tl.to(myBox, {
  y: 200,
  duration: 1
});

tl.to(myBox, {
  backgroundColor: "green",
  x: 0,
  duration: 0.2
});

tl.to(
  myBox,
  {
    backgroundColor: "red",
    y: 0,
    duration: 0.25
  },
  "-=0.2"
);

tl.play();
```

Les timelines peuvent aussi être utilisées de façon impriquées pour avoir un meilleur contrôle de vos animation et en nommer les différentes parties.

```js
// Self invoking function
// Avoid variables collisions by scoping them
(function () {
  // nested timelines
  // better for composing complex animations and overlap
  const myBox = document.querySelector(".js-box");

  function one() {
    let tl = gsap.timeline();
    tl.to(myBox, { x: 200, duration: 1, delay: 1 });
    return tl;
  }

  function two() {
    let tl = gsap.timeline();
    tl.to(myBox, {
      backgroundColor: "blue",
      rotation: 360,
      duration: 0.5,
      repeat: 3
    });
    return tl;
  }

  function three() {
    let tl = gsap.timeline();
    tl.to(myBox, {
      y: 200,
      duration: 1
    });
    return tl;
  }

  function four() {
    let tl = gsap.timeline();
    tl.to(myBox, {
      backgroundColor: "green",
      x: 0,
      duration: 0.25
    });
    return tl;
  }

  function five() {
    let tl = gsap.timeline();
    tl.to(myBox, {
      backgroundColor: "red",
      y: 0,
      duration: 1
    });
    return tl;
  }

  let master = gsap.timeline();
  master.pause();
  master
    .add(one())
    .add(two(), "+=2")
    .add(three(), "-=1")
    .add(four())
    .add(five());
  master.play();
})();
```

Voici un [exemple plus abouti](https://github.com/jeromecoupe/web_animations_demo) utilisant des timelines imbriquées pour gérer efficacement les diverses parties d'une animation complexe.

_Exercice: décortiquer ensemble le script et voir comment les choses fonctionnent_

## Ressources

- [An introduction to CSS 3D Transforms](http://24ways.org/2010/intro-to-css-3d-transforms/) par [David De Sandro](http://desandro.com/)
- Le même article, assorti de [démonstrations sur github](http://desandro.github.io/3dtransforms/docs/introduction.html) par David De Sandro toujours
- [CSS Animations](http://www.fivesimplesteps.com/products/css-animations): un superbe bouquin pour commencer par Val Head sur Five Simple Steps.
- Pour ceux qui préfèrent les podcasts et les liens qui les accompagnent, [Motion and Meaning](http://motionandmeaning.io/) par Val Head and Cennydd Bowles.
- [Primer on Bézier Curves](http://pomax.github.io/bezierinfo/)
- [Animations and UX Resources](http://rachelnabors.com/animation-ux/) par Rachel Nabors
- [Keyframe Animations Syntax](http://css-tricks.com/snippets/css/keyframe-animation-syntax/) par Chris Coyier sur CSS Tricks.
- [CSS3 Transitions, Transforms, Animation, Filters and more!](http://css3.bradshawenterprises.com/) par Rich Bradshaw. Un superbe résumé de tout ce qu'il faut savoir, exemples inclus.
- [Disney's 12 principles of animations](http://en.wikipedia.org/wiki/12_basic_principles_of_animation)
- Google propose quelques conseils concernant les animations et les transitions dans son introduction au "Material Design": [animation](https://www.google.com/design/spec/animation/authentic-motion.html), [responsive interaction](https://www.google.com/design/spec/animation/responsive-interaction.html), [meaningful transitions](https://www.google.com/design/spec/animation/meaningful-transitions.html), [delightful details](https://www.google.com/design/spec/animation/delightful-details.html).
- [All the right moves](http://vimeo.com/86821694): vidéo expliquant les interactions entre animations CSS et JavaScript par Val Head
- [Alice in Videoland](http://rachelnabors.com/alice-in-videoland/book/): une petite histoire animée et quelques tutoriaux à la fin - par Rachel Nabors.
- [Flashless animations](http://24ways.org/2012/flashless-animation/) - par Rachel Nabors sur 24ways
- [How to Create Windows-8-like animations with CSS3 and jQuery](http://sarasoueidan.com/blog/windows8-animations/) - Par Sara Soueidan
- [CSS Sprite Sheet Animations with steps()](http://blog.teamtreehouse.com/css-sprite-sheet-animations-steps) - par Guil Hernandez sur Treehouse
- [Web animation API](http://rachelnabors.com/waapi): ressources par Rachel Nabors
- [Greensock / GSAP](https://greensock.com): le site de Greensock / GSAP
- [Getting Started with GSAP](https://greensock.com/get-started-js): commencer avec GSAP (video)
- [Jump Start: GSAP JS](https://greensock.com/jump-start-js): commencer avec GSAP (video)
- [How to Animate on the Web With GreenSock ](https://css-tricks.com/how-to-animate-on-the-web-with-greensock/): par Sarah Drasner
