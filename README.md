# Skymod Launcher - Releases

Ce depot est **genere automatiquement**. Ne l'edite pas a la main.

Il heberge les artefacts de mise a jour du launcher, servies en HTTP brut
depuis la branche `main` :

| Fichier | Role |
| --- | --- |
| `latest.json` | Manifeste lu par l'updater Tauri a chaque verification de mise a jour |
| `<Product>_v<version>_x64_en-US.msi` | Installeur Windows |
| `<Product>_v<version>_x64_en-US.msi.sig` | Signature minisign de l'installeur, verifiee par la cle publique embarquee dans l'app |

## Publication

La publication est declenchee par le workflow `Publish updater artifacts`
(`.github/workflows/release.yml`) du depot `Skymod-Launcher`, sur un push de tag
`v*` :

1. le tag est compare a `package.json`, `src-tauri/tauri.conf.json` et
   `src-tauri/Cargo.toml` — le build echoue si les versions divergent ;
2. l'application est build en MSI et signee avec la cleConfiguree dans le
   secret `TAURI_SIGNING_PRIVATE_KEY` du depot source ;
3. le MSI, sa signature et `latest.json` sont pousses ici sur `main`.

## Endpoint

L'updater lit ce manifeste, configure dans `src-tauri/tauri.conf.json` :

```
https://raw.githubusercontent.com/MaxenceBailly/Skymod-Launcher-Releases/main/latest.json
```

Une `404` sur cette URL signifie qu'aucune release n'a encore ete publiee : dans
ce cas l'application affiche « aucune mise a jour disponible », ce qui est le
comportement attendu tant que le premier tag n'est pas pousse.
