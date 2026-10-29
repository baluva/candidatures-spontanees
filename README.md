# candidatures-spontanees

Envoie des candidatures spontanées par email : cherche des entreprises data/IA dans les
départements choisis (API officielle recherche-entreprises), trouve un email générique sur leur
site (recrutement@, rh@, contact@...), puis envoie depuis Outlook le CV et une lettre de
motivation PDF générée pour chaque boîte, avec une pause de 1 à 3 min entre deux envois.

Le bot LinkedIn « Candidature simplifiée » est dans un repo à part :
[linkedin-apply](https://github.com/baluva/linkedin-apply).

## Installation
```
pip install playwright
cp config.example.toml config.toml   # puis remplir [profile], le chemin du CV et [email]
```
(Outlook classique doit être installé et connecté pour le backend `outlook` ; Chrome ou Opera sert
à imprimer les lettres en PDF. `config.toml`, `entreprises.csv` et les lettres restent en local,
ils ne sont pas versionnés.)

## Utilisation
Double-clic sur **Envoyer candidatures.bat** (ou le raccourci du Bureau) : recherche, liste des
boîtes trouvées, confirmation o/n, puis envoi.
```
python spontane.py run                     # ce que fait le raccourci
python spontane.py scrape --max 40         # remplir entreprises.csv seulement
python spontane.py send --dry-run --max 5  # créer des brouillons Outlook sans envoyer
python spontane.py suivi                   # noter qui a répondu (lecture seule de la boîte)
python spontane.py relance                 # une seule relance, après relance_apres_jours sans réponse
python lettre.py --company "Linedata" --site linedata.com --ville Aix-en-Provence
```
Réglages dans `config.toml` : `[spontane]` (départements, secteurs NAF, tailles) et `[email]`
(compte, objet, corps du mail, délais). Le mot de passe SMTP éventuel n'est jamais écrit dans le
fichier : il est lu dans une variable d'environnement.

## La lettre
Lettre classique sur une page : logo Ynov et coordonnées à gauche, logo de l'entreprise (icône de
son site, gardée dans `assets/logos/`) et destinataire à droite, objet, corps justifié. Les
paragraphes sont choisis selon le thème détecté et le texte rétrécit tout seul pour tenir sur une page.

## Apprentissage (sans IA payante)
`apprentissage.py` lit les réponses notées dans `entreprises.csv` et calcule un taux de vraies
réponses par code NAF, taille et département : l'envoi et la recherche passent d'abord par les
segments qui répondent. Les pages où l'email est le plus souvent trouvé (/contact,
/mentions-legales…) sont visitées en premier. Rien n'est supprimé, un segment peu rentable passe
simplement après. Bilan : `python apprentissage.py`. Désactiver : `apprendre = false` dans `[spontane]`.

## Fichiers
- `config.toml` : profil, recherche d'entreprises, email
- `entreprises.csv` : boîtes trouvées, email, statut d'envoi, réponse (supprime une ligne pour l'exclure)
- `apprentissage.json` : pages email apprises
- `lettres/` : lettres PDF (supprimées après envoi sauf `[lettres] garder = true`)
- `assets/ynov_logo.jpg` : logo de l'en-tête
