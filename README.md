# Sandy — réparation expérimentale et essais locaux

**Essais locaux réussis le 3 novembre 2026. Aucun essai avec fonds réels prêt.**

Réparation du projet MIT [iq/sandy](https://github.com/iq/sandy/tree/b3dacdff5ec851c4bcc98af89ceff8b227c5c19a), avec limites de configuration, journal du budget et sortie de secours bornée. Le logiciel de production reste hors ligne ; ses anciens lanceurs et son relais sont désactivés.

## Résultats vérifiés

[Exécution complète réussie](https://github.com/karedott40-pixel/MEMES-CLUB-SANDY-TESTS/actions/runs/41567387491), commit `03eeea12fc971d93541fc65ac16d3c6bea94a38a`.

- 41 tests du client, 32 du programme, 8 vérifications de l’exécutable et 4 vérifications JavaScript. Les 14 cas communs du budget sont exécutés dans chacun des deux composants Rust.
- Sandy V3 et le programme historique officiel Raydium AMM v4 compilent dans le format Solana. Raydium : `d8e88250a648cff370d588acb91db7c4ff72b52b`, SwapOnly avec réserves inventées.
- 11 scénarios et 18 transactions confirmées sur une nouvelle chaîne locale, dans un réseau isolé avec seulement l’interface locale `lo`. Aucun accès à un réseau public Solana, aucun wallet personnel, aucune clé réelle.
- Achat, échange du deuxième wallet contrôlé et revente exécutés. Les jetons déjà détenus sont conservés. Les budgets dépassés et les mauvaises signatures sont refusés.
- L’achat séparé peut réussir et sa revente échouer : la position reste alors ouverte et des frais sont débités. La pause seule ne revend pas les jetons.
- La sortie de secours V3 ferme cette position sous un plafond explicite : perte et tous les frais du scénario **0,005029237 SOL fictif**, plafond **0,01 SOL fictif**. Aucun pourboire supplémentaire ; la pause et la réservation du budget restent actives après la sortie.
- Une transaction regroupée échouée annule les échanges et l’état du programme, mais garde ses frais débités. Le journal client conserve le budget réservé.

Les 18 transactions ont coûté **0,000095 SOL fictif** en frais, dont 0,000020 pour les quatre initialisations. Ces comptes immobilisent séparément 0,010105920 SOL fictif. Tous les soldes proviennent du démarrage inventé de la chaîne locale ; aucun argent n’a été utilisé.

## Sources reproductibles

`SANDY_SAFETY_V3.zip` : SHA256 `85a427a40aa16083145a122fa8075a6c4c8bd947c2bd308b9105e91b4f98d5b9`.

`SANDY_RUNTIME_V2.zip` : SHA256 `d2245c49ee43546c5a690d57096870bb1988a8418945aad8a3d2d42bcd97b3ac`. Le workflow manuel vérifie cette empreinte, les fichiers internes et les empreintes Git des sources officielles avant compilation.

Programme Sandy : SHA256 `430b5c78ce5ce476c9608e267139443813120e385cb1676b2e03a502a1865899`.

Programme Raydium : SHA256 `4d3db6903a22a46e4cc8577717b4ad0da9afe75a12e797021f179ea9bb745fc9`.

Agave 2.0.8 ; compilateur v1.42 pour Sandy, v1.37 pour Raydium afin de correspondre à son ancien SDK Solana 1.16.24. Aucune erreur de dépassement de pile acceptée. Les premières archives et exécutions restent conservées pour l’historique.

## Limites restantes

Ces essais ne prouvent ni la rentabilité, ni l’ordre des transactions en production, ni le fonctionnement des lots Jito. La transaction regroupée positive exige les signatures des deux wallets contrôlés ; elle ne démontre pas un lot de transactions distinctes. Les résultats du wallet du bot ne sont pas un bénéfice global du propriétaire du deuxième wallet.

Les comptes et frais d’un pool réel, le suivi permanent des frais et la transmission de production restent à vérifier. Le logiciel CLI n’envoie aucune transaction. Les réservations couvrent le capital et les frais déclarés et ne sont jamais rétablies automatiquement ; un échec sur Solana peut annuler les compteurs du programme tout en coûtant des frais, d’où le journal client obligatoire.

Une sortie au prix minimum peut échouer si la liquidité manque. Son plafond ne garantit donc pas la liquidation ni la valeur des jetons conservés. Les comptes de carnet d’ordres, Token-2022 et les tables d’adresses ne sont pas couverts. Cette ancienne version Raydium n’est pas prouvée identique au programme aujourd’hui utilisé sur le réseau public. Les dépendances historiques restent à auditer ; une compilation réussie ne les rend pas sûres.

Ne fournir aucune clé réelle à ce projet. Aucun déploiement public ni consommation d’un service payant. Licence MIT d’origine conservée dans `LICENSE` ; les sources officielles de Raydium conservent leur licence Apache-2.0.
