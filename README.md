# nombre-al-atoire
comment définir un nombre aléatoire  

# Projet : Modélisation et Génération de Nombres Pseudo-Aléatoires

## Introduction théorique
En informatique, le hasard pur est une impossibilité physique pour un processeur standard. Les ordinateurs utilisent des algorithmes déterministes pour produire des suites de nombres qui possèdent les propriétés statistiques de l'aléatoire. On appelle ces systèmes des **Générateurs de Nombres Pseudo-Aléatoires (PRNG)**.

L'élément central de ces algorithmes est la **Graine (Seed)**. Si la graine est identique, la suite de nombres générée sera rigoureusement la même à chaque exécution.

---

## Solutions algorithmiques et Formules
Il existe plusieurs méthodes pour simuler l'aléatoire. Voici les trois plus documentées :

### 1. Le Générateur Congruentiel Linéaire (LCG)
C'est la méthode la plus ancienne, utilisée pour sa rapidité et sa faible consommation en mémoire. Elle repose sur l'arithmétique modulaire.

**Formule :**
$$X_{n+1} = (a \cdot X_n + c) \mod m$$

* **m (le module) :** définit la période maximale (ex: $2^{32}$).
* **a (le multiplicateur) :** doit être choisi avec soin pour garantir une bonne répartition.
* **c (l'incrément) :** doit être premier avec **m**.
* **Xn :** la valeur actuelle (le résultat du calcul précédent).

### 2. Le Générateur à Décalage de Registre (Xorshift)
Cette méthode est très appréciée dans les systèmes embarqués car elle n'utilise que des opérations logiques (XOR et décalages de bits), ce qui la rend extrêmement rapide.

**Principe :**
On prend un nombre binaire, on le décale vers la gauche ou la droite, et on applique un "OU exclusif" (XOR) avec sa valeur d'origine.

**Exemple de logique :**
$$x \leftarrow x \oplus (x \ll a)$$
$$x \leftarrow x \oplus (x \gg b)$$
$$x \leftarrow x \oplus (x \ll c)$$

### 3. Le Mersenne Twister (MT19937)
C'est l'algorithme standard actuel (utilisé par la bibliothèque `random` de Python). Il est beaucoup plus complexe que le LCG mais offre une période de répétition immense ($2^{19937} - 1$).

**Méthode :**
Il repose sur une récurrence matricielle sur un corps fini de $GF(2)$. Il est conçu pour éviter les corrélations entre les nombres générés sur de très longues séries.

---

## Implémentation visée
L'objectif de ce dépôt est de coder une version personnalisée du **Générateur Congruentiel Linéaire** en Python, sans utiliser d'importation externe, afin de démontrer le fonctionnement interne de la gestion du chaos numérique.

## Utilisation
1.  **Définition d'une graine :** Utilisation d'une valeur initiale (souvent basée sur l'horloge système).
2.  **Itération de la formule :** Calcul successif pour obtenir une suite de nombres.
3.  **Normalisation :** Transformation du résultat pour ramener le nombre dans un intervalle compris entre 0 et 1.

---
*Développement réalisé à des fins pédagogiques sur l'algorithmique déterministe.*

```cpp
_M = 2**31
_A = 1103515245
_C = 12345
_graine = 0

def initialiser_graine(valeur):
    global _graine
    _graine = valeur % _M

def initialiser_graine_variable():
    objet_temporaire = object()
    graine_auto = id(objet_temporaire)
    initialiser_graine(graine_auto)
    return graine_auto

def next_rand():
    global _graine
    _graine = (_A * _graine + _C) % _M
    return _graine

def rand_float():
    return next_rand() / _M

def rand_range(borne_min, borne_max):
    if borne_min > borne_max:
        raise ValueError("Erreur : min > max")
    amplitude = borne_max - borne_min + 1
    return borne_min + int(rand_float() * amplitude)

if __name__ == "__main__":
    graine = initialiser_graine_variable()
    print(f"Graine : {graine}")
    
    print("5 nombres entiers :", [next_rand() for _ in range(5)])
    print("5 flottants (0-1) :", [round(rand_float(), 4) for _ in range(5)])
    print("5 lancers de des  :", [rand_range(1, 6) for _ in range(20)])
    
