---
title: "Feature based interactive email trigger"
---


```css
.email-feature > input:checked ~ * .email-interactive-stuff { display:block !important; }
.email-feature > input:checked ~ * .email-fallback { display:none; }
```

```html
<div class="email-feature">
    <!--[if !mso]><!-->
    <input type="radio" class="email-radio" name="email-placebo-radio" checked style="display:none; display:contents;" style="display:none;" />
    <!--<![endif]-->
    <div class="email-interactive-stuff" style="display:none;">

    </div>
    <div class="email-fallback">

    </div>
</div>
```



# 2. Activation du carousel et support dégradé

Pour que le carousel fonctionne, nous avons donc besoin du support de trois éléments :

* Des balises `<label>`
* Des balises `<input>`
* De la pseudo-classe `:checked`

Afin de n'afficher les éléments interactifs que lorsque c'est nécessaire, on va préfixer les règles CSS liées au carousel par le sélecteur suivant : 

```
.carousel > input:checked ~ * .foo { }
```

Ce sélecteur cible un élément ayant une `class="foo"` après un `<input>` coché directement à l'intérieur d'un élément avec une `class="carousel"`. La balise `<input>` utilisée ici joue en fait un rôle de _placebo_ (l'effet, pas le groupe). Elle n'est là que pour tester le support de la balise `<input>` et de la pseudo-classe `:checked`. Ainsi, le code ne repose pas, de base, du ciblage spécifique à un client mail en particulier. Certains cas spécifiques sont cependant nécessaires.

## Exclusion de Outlook et Lotus Notes

Les éléments interactifs ne fonctionnent pas sur les Outlook sur Windows (utilisant le moteur de rendu de Word) ou sur Lotus Notes (utilisant le moteur de rendu d'Internet Explorer). Afin d'exclure les éléments interactifs de ces clients, on utilise des commentaires conditionnels au format suivant.

```
<!--[if !(mso|IE)]><!-->
Ce contenu sera masqué aux yeux des Outlook (sur Windows) et Lotus Notes
<!--<![endif]-->
```

## Particularités des webmails français (SFR et La Poste)

Les clients mails français de SFR et La Poste remplacent les balises `<input>` par des `<noinput>`. (C'était le cas aussi de Orange jusqu'à mars 2021.) Cette balise `<noinput>`, non standard, va se comporter comme une balise `<div>` et englober le contenu qui la suit. Cela pose problème avec notre comportement dégradé souhaité par défaut où l'on va masquer les balises `<input>`. Ainsi, le code suivant :

```
<input type="radio" style="display:none;" />
<div class="carousel-button">
    …
</div>
<div class="carousel-item">
    …
</div>
```

… est interprété par ces clients mails comme :

```
<noinput style="display:none;">
    <div class="carousel-button">
        …
    </div>
    <div class="carousel-item">
        …
    </div>
</noinput>
```

Tout le contenu suivant un `<input>` se retrouve alors masqué par défaut. Si certaines versions de ces clients supportent une règle de styles du type `noinput { display:block !important; }`, d'autres ne supportent aucune balise `<style>` et nécessitent donc d'appliquer un style spécifique inline.

On utilise alors en supplément la propriété [`display:contents`](https://developer.mozilla.org/fr/docs/Web/CSS/display#display_contents). 

## Particularités de T-Online.de

T-Online.de ne supporte pas le `display:contents` ajouté précédemment. On va alors appliquer une autre technique spécifique en doublant l'attribut `style` sur les `<input>`. On obtient alors le code suivant.

```
<input type="radio" style="display:none; display:contents;" style="display:none;" />
```
