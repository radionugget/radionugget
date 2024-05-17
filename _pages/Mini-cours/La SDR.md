---
title: "C'est quoi la SDR ?"
date: "17-05-2024"
thumbnail: "/assets/img/thumbnail/sdr.jpg"
---
# Récepteur analogique 
À la base, la réception des ondes radio utilise des composants comme des résistances, des condensateurs, des bobines... On retrouve un ensemble de pièces pour le traitement du signal comme les filtres, bref c'est un système qui existe depuis très longtemps 📻.
L'inconvénient est que chaque composant est une entité physique, ça coûte cher, ça peut prendre de la place, et modifier les caractéristiques d'un d'entre eux demandent des compétences poussées en éléctronique.  
# Récepteur numérique 
L'idée va être de numériser le signal le plus tôt possible pour l'envoyer à un **CPU** où l'on pourra commencer notre traitement du signal. L'avantage est que l'on pourra utiliser des algorithmes beaucoup plus complexes, notamment à l'aide des **nombres complexes** qui sont très difficiles à mettre en place avec des résistances ou autres. 
Et oui, ces fameux nombres qu'on pensait inutiles au lycée ont une réelle utilité pour les signaux. 
- La partie **réelle** du nombre sert à représenter l'**amplitude** (sa hauteur en quelque sorte) du signal.
- La partie **imaginaire**, pour réprésenter sa **phase** (sa position dans le temps). 

Ainsi, on va pouvoir simplifier des opérations mathématiques. 
Pas convaincu ? Prenons par exemple la multiplication de deux signaux (inutile de comprendre ce que ça signifie). 
Sans nombres complexes, il faudrait utiliser des calculs **trigonométriques** assez tordu. 
Alors qu'avec les nombres complexes, il suffirait de multiplier les amplitudes et ajouter leur phase, ce qui se fait simplement avec des opérations algébriques sur les nombres complexes (si si :D). 
De plus, le numérique se met simplement à jour, ce qui est pratique, notamment pour les logiciels ou autres algorithmes. 
Un autre gros avantage du numérique est de pouvoir utiliser un **analyseur de spectre** ce qui est très pratique pour faire du **debug**. C'est comme utiliser **WireShark** 🦈. 
# Récepteur SDR 
Numériser le signal et le traiter par logiciel a un nom, c'est la **SDR** (**S**oftware **D**efined **R**adio). Elle est rendue possible par des récépteurs comme par exemple celui-ci : 
![RTL-SDR-V4](../../assets/img/mini-cours/rtlsdrv4.png)
Ces récépteurs bon marché (**30€** en moyenne), se branchent en **USB** à un ordinateur équipé d'un logiciel **SDR** (il en existe plusieurs). On retrouve un port **MCX** (**M**icro **C**oaxial e**X**tended), c'est un connecteur **coaxial** plus petit que l'on relie à notre antenne. 