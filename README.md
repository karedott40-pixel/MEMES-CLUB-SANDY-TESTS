# Sandy — profil de vérification hors ligne

Réparation expérimentale du projet MIT [iq/sandy](https://github.com/iq/sandy/tree/b3dacdff5ec851c4bcc98af89ceff8b227c5c19a). Ce profil permet de vérifier la configuration et la comptabilité du budget **sans ouvrir de portefeuille ni envoyer de transaction**. Il ne constitue pas un sandwich bot prêt à utiliser avec des fonds.

## Résultats vérifiés le 2 novembre 2026

- [Tests Linux réussis](https://github.com/karedott40-pixel/MEMES-CLUB-SANDY-TESTS/actions/runs/41391958421) : 38 tests du client, 29 du programme, 8 essais de l’exécutable et 4 vérifications JavaScript. Les 12 cas du budget commun sont exécutés dans chaque composant Rust.
- [Compilation Solana SBF réussie](https://github.com/karedott40-pixel/MEMES-CLUB-SANDY-TESTS/actions/runs/41392758113) : Agave 2.0.8 / platform-tools v1.42, programme eBPF de 136 240 octets, SHA256 `770710308d1ddd37e7708acf9249e0b35c3cbac1138d3d5a5da345382449237f`. Aucun déploiement ni RPC Solana.
- Les sources corrigées se trouvent dans `SANDY_SAFETY_V2.zip` (SHA256 `70b1a84561cb578451df322fe7643e0be4213cd45aa8b954e36b5edab4256f42`). Les workflows manuels vérifient cette empreinte avant compilation. L’ancienne archive mathématique reste conservée pour historique.

Ces résultats couvrent la construction et les contrôles hors ligne. Les échanges complets, les frais réels et la sortie en cas d’échec ne sont pas validés. Aucun essai avec fonds n’est prêt. Le README à l’intérieur de l’archive est le texte antérieur aux tests ; la présente page donne les résultats obtenus ensuite.

## Protections ajoutées

- Configuration JSON stricte : champs inconnus, valeurs négatives, nombres décimaux et adresses provisoires refusés. Seul le mode `inspect` est accepté ; les services externes sont refusés.
- Plafonds distincts pour montant engagé, frais réseau, pourboires, création de comptes et nombre d'essais. Les montants sont des entiers en lamports : 1 SOL = 1 000 000 000 lamports.
- Journal persistant : une réservation inclut le capital et tous les frais déclarés. Elle reste consommée après un redémarrage, un résultat inconnu ou un bénéfice annoncé. Deux processus ne peuvent pas réserver simultanément le même budget.
- Arrêt d'urgence : la présence de `risk.json.STOP` empêche une nouvelle réservation. Un journal illisible, une configuration modifiée, un verrou ou une écriture interrompue provoque un refus ; aucun effacement automatique.
- Instructions versionnées, état propre à chaque payeur, contrôle de signature et de propriétaire, refus de réinitialiser les compteurs ; montants et minimums de réception bornés dans le code du programme.
- Le code de sortie ne doit vendre que les jetons achetés pendant l'entrée, et non des positions déjà détenues. Les tests actuels ne prouvent pas encore cette propriété lors d'un échange réel.
- Les anciens scripts JavaScript de lancement et le relais réseau sont désactivés. Aucun drapeau ni variable d'environnement ne réactive le trading dans cet exécutable.

## Vérification reproductible

L'exemple `config.example.json` comporte un budget nul et aucune adresse personnelle.

```sh
cargo +1.82.0 test --locked --workspace --all-targets
cargo +1.82.0 build --locked --workspace
python3 scripts/test_entrypoints.py target/debug/sandy-bot
node --test scripts/safety.test.js
target/debug/sandy-bot --check-config config.example.json
```

`--assess CONFIG DEMANDE JOURNAL` ne fait que réserver un budget fictif sur disque. Il n'autorise pas une transaction. Les tests utilisent des adresses fictives et, pour tester la sérialisation des instructions, des clés temporaires générées en mémoire, sans fonds ni sauvegarde.

## Limites importantes

Le budget est une borne conservatrice sur **l'exposition déclarée**, pas un calcul des pertes réellement encaissées. Sa somme de contrôle détecte une corruption accidentelle ; elle ne protège pas contre un administrateur qui modifie ou supprime tous les fichiers. Les coûts déclarés ne sont pas encore rapprochés des frais d'une transaction signée et exécutée.

Le programme a changé de format (`SNY2`, état version 2) : il n'est pas compatible avec les anciens comptes et instructions. Aucun déploiement, conversion de compte ou essai avec des fonds n'est prévu dans cette version. La construction Linux et la compilation au format SBF ont réussi sur GitHub. Elles ne prouvent ni l'intégration Raydium, ni l'ordre des transactions, ni l'exécution atomique, ni la rentabilité. Les tables d'adresses Solana et les tokens Token-2022 ne sont pas pris en charge.

Avant tout essai réel, il reste à vérifier les échanges complets sur un réseau local, les comptes et frais réels du pool choisi, la liaison entre frais signés et plafonds, et un mécanisme de sortie si seule l'entrée réussit. L'arrêt d'urgence ne peut pas annuler une transaction déjà envoyée. Les tests du journal ne certifient pas une résistance à toute panne matérielle ou modification hostile du système de fichiers.

Les dépendances historiques restent figées par `Cargo.lock` ; une compilation réussie n'est pas un audit complet de leurs vulnérabilités. Ne fournir aucune clé réelle à ce projet.

Licence MIT d'origine conservée dans `LICENSE`.

Un échec sur Solana peut coûter des frais même si les modifications du compteur du programme sont annulées. Le journal client doit rester obligatoire avant toute future signature ; le programme seul ne borne pas ces frais.
