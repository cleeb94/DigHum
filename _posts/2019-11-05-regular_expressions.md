---
layout: post
title: Learning Regular Expressions
subtitle: Praktikum with RE
image: /img/reg.jpeg
---

The Solution for the Praktikum are as folllowd:

## INTRO (there is no number two):
1. t**hat** **cat** c**hat** p**hat**
3. **02** **03** **12** **13**
4. **501-1234** **234 1252** 713-**342-7452** **653-6464**
5. **runs** **run**
6. **12345** **122345** **111111** **113456** **109493** 5**10349**
7. ar?t -> 1. 2.
   a[fr]?t -> 1. 2. 4.
   ar*t -> 1. 2. 3.
   ar+t -> 2. 3.
   a.*t -> 1. 2. 3. 4.

## PART I
1. \b(eats?(ery?|en|ing)?|ate)\b
2. \b(M[auo\']+m+[ae]r?) ?( [eAEa][lI][ -])?([GK]h?a[dhz]+[a]f+[iy])\b
3. \b[IEHieh][siṣ][bfs]p?[aā]h[aā]n\b
NB: There are more options for names in the solution file, which is why the solution here looks very different.
In the solution there is also something missing: in some of the [] there are letters missing, I believe.

## PART II
1. ^\t(\w+), (\w+)$ 
    \t\2 \1
2. Vienna|Graz|Linz|Salzburg|Innsbruck|Klagenfurt|Villach|Wels|Sankt Pölten|Dornbirn|Wiener Neustadt|Steyr|Feldkirch|Bregenz|Leonding|Klosterneuburg|Baden bei Wien|Wolfsberg|Leoben|Krems|Traun|Amstetten|Lustenau|Kapfenberg|Mödling|Hallein|Kufstein|Traiskirchen|Schwechat|Braunau am Inn|Stockerau|Saalfelden|Ansfelden|Tulln|Hohenems|Spittal an der Drau|Telfs|Ternitz|Perchtoldsdorf|Feldkirchen|Bludenz|Bad Ischl|Eisenstadt|Schwaz|Hall in Tirol|Gmunden|Wörgl|Wals-Siezenheim|Marchtrenk|Bruck an der Mur|Sankt Veit an der Glan|Korneuburg|Neunkirchen|Hard|Vöcklabruck|Lienz|Rankweil|Hollabrunn|Enns|Brunn am Gebirge|Ried im Innkreis|Bad Vöslau|Waidhofen|Knittelfeld|Trofaiach|Mistelbach|Zwettl|Völkermark|Götzis|Sankt Johann im Pongau|Gänserndorf|Gerasdorf bei Wien|Ebreichsdorf|Bischofshofen|Groß-Enzersdorf|Seekirchen am Wallersee|Sankt Andrä
3. a) my solution (longer than the one provided in the solution file): \b(\w+ )?(\w+ )?(\w+)(-\w+)? \(Lower Austria\)
    b) \b(\w+ )?(\w+ )?(\w+ )?(\w+)(-\w+)? \(Salzburg\)


