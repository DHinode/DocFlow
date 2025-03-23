# Plugin
Pour deployer les plugins :  
- il faut les télécharger en local si on lance en local   
- changer le fichier `book.toml`  

## TOC (table of content)
Source : [https://github.com/badboy/mdbook-toc](https://github.com/badboy/mdbook-toc)  
Installation : `cargo install mdbook-toc`  
Lancement rien a faire
Exemple de l'utilisation : `{{#toc}}` 

{{#toc}}  

## Mermaid (Graphivz)  
Source : [https://github.com/badboy/mdbook-mermaid](https://github.com/badboy/mdbook-mermaid)
Installation : `cargo install mdbook-mermaid`  
Configuration: `mdbook-mermaid install <book_folder>` 
Exemple d'utilisation: voir doc avec Graphivz 

```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

## Admonish   
Source : [https://github.com/tommilligan/mdbook-admonish](https://github.com/tommilligan/mdbook-admonish)  
Installation : `cargo install mdbook-admonish`  
Lancement :` mdbook-admonish install`  
Exemple :  
```admonish info    
A beautifully styled message.    
```
