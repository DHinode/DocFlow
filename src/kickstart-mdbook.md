# Kickstart : mdBook 

## Créer/modifier un fichier

1. Créer ou modifier le fichier markdown souhaité (possibilité de l'imbriquer dans un dossier pour faire des sous-chapitres)
2. Ajouter les modifications au fichier `src/SUMMARY.md`, un exemple ci-dessous.
```markdown
# Summary

[Introduction](README.md)

- [My First Chapter](my-first-chapter.md)
- [Nested example](nested/README.md)
    - [Sub-chapter](nested/sub-chapter.md)
```

Et en supposant que l'arborescence de vos sources est comme ceci : 

```bash
src/
├── README.md
├── SUMMARY.md
├── my-first-chapter.md
└── nested
    ├── README.md
    └── sub-chapter.md

2 directories, 5 files
```

Source : [documentation](https://rust-lang.github.io/mdBook/guide/creating.html)


## Command Line Tool

`mdbook init <répertoire>` : créer un nouveau livre  

### Preview du livre en local 

1. Placez-vous dans le répertoire où se trouve le fichier `book.toml`
2. Contruire le livre : `mdbook build` (optionnel je crois)
3. Tapez `mdbook serve --open` 
4. Si le livre ne s'ouvre pas automatiquement, tapez `http://localhost:3000/` dans votre navigateur. 

### Déploiement 

Nous avons déployé ce livre à l'aide de Github Pages. 

Pour déployer vos modifications sur le livre, il suffit simplement de pousser tout le nouveau contenu sur le repo.  
