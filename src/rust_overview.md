# Bienvenue dans l'univers de Rust ! 🦀

Salut futur **Rustacéen** ! Si tu es ici, c'est que tu as entendu parler de ce langage de programmation qui fait de plus en plus de bruit. Rust est réputé pour être performant, sûr et... un peu difficile à aborder au début. Mais n'aie pas peur ! Ce guide est là pour te donner une vue d'ensemble complète et (on l'espère) amusante.

## Pourquoi Rust est-il si spécial ?

Rust a été créé par Mozilla avec un objectif ambitieux : offrir la performance du C++ sans les maux de tête liés à la gestion de la mémoire. En gros, Rust te promet :

- **Performance :** Une vitesse comparable au C et C++, sans *garbage collector* pour ralentir les choses.
- **Sécurité :** Le compilateur de Rust est ton meilleur ami (un ami très strict). Il t'empêchera de faire des erreurs courantes qui mènent à des bugs ou des failles de sécurité (coucou les *null pointer exceptions* et les *data races*).
- **Concurrence :** Écrire du code qui s'exécute sur plusieurs cœurs de processeur est un jeu d'enfant (ou presque) grâce à son modèle de propriété.

> **Anecdote amusante :** Le nom "Rust" ne vient pas de la rouille ! Il vient d'un type de champignon, les *rust fungi*, qui sont robustes et très répandus. Un peu comme le langage, non ?

## Les concepts clés qui font grincer des dents (au début)

Rust introduit quelques concepts uniques. Une fois que tu les auras compris, tu coderas avec une confiance renouvelée.

### 1. L'Ownership (la propriété)

C'est LE concept central de Rust. Imagine que chaque valeur dans ton code a un **propriétaire** unique.

- Quand le propriétaire sort du *scope* (la portée, en gros les `{}`), la valeur est automatiquement détruite.
- Tu peux "emprunter" (borrow) une valeur, mais tu ne peux pas la posséder à plusieurs.

```rust
fn main() {
    let s1 = String::from("hello"); // s1 est propriétaire de "hello"
    let s2 = s1; // s1 n'est plus valide, la propriété a été déplacée à s2
    // println!("{}", s1); // Erreur ! s1 n'existe plus.
}
```

C'est comme si tu donnais un livre à un ami. Tu ne l'as plus !

### 2. Le Borrowing (l'emprunt)

Tu ne veux pas donner ton livre, juste le prêter ? C'est le *borrowing*. Tu peux créer des **références** à une valeur.

- Tu peux avoir autant de références immuables (`&T`) que tu veux.
- Tu ne peux avoir qu'une seule référence mutable (`&mut T`) à la fois.

```rust
fn main() {
    let mut s = String::from("hello");
    let r1 = &s; // OK
    let r2 = &s; // OK
    // let r3 = &mut s; // ERREUR ! On ne peut pas emprunter en mutable quand il y a des emprunts immuables.
    println!("{} et {}", r1, r2);
}
```
> **Analogie :** C'est comme un document Google. Soit tout le monde peut le lire (références immuables), soit une seule personne peut l'éditer (référence mutable). Pas les deux en même temps !

### 3. Les Lifetimes (durées de vie)

C'est souvent le concept qui fait le plus peur. Les *lifetimes* sont la façon dont le compilateur s'assure que tes références sont toujours valides. En gros, le compilateur vérifie que tu n'essaies pas d'utiliser une référence à une donnée qui a été détruite.

La plupart du temps, tu n'auras pas à les écrire toi-même, le compilateur est assez malin. Mais parfois, dans les fonctions, tu devras l'aider un peu avec une syntaxe comme `'a`.

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```
Ici, on dit au compilateur : "Cette fonction retourne une référence qui vivra au moins aussi longtemps que la plus courte des deux références qu'on lui a passées."

---

## Cheatsheet du Rustacéen Débutant

Voici les bases pour commencer à écrire du code Rust.

### Variables

```rust
let x = 5; // Immuable par défaut
let mut y = 10; // Mutable
y = 12;

const MAX_POINTS: u32 = 100_000; // Constante, doit être typée
```

### Types de données scalaires

```rust
// Entiers (i8, u8, i32, u32, etc.)
let a: i32 = -10;
let b: u32 = 10;

// Nombres à virgule flottante
let f: f64 = 2.0;

// Booléen
let is_true: bool = true;

// Caractère
let c: char = '🦀';
```

### Types de données composés

```rust
// Tuple
let tup: (i32, f64, u8) = (500, 6.4, 1);
let (x, y, z) = tup; // Déstructuration

// Tableau (taille fixe)
let a = [1, 2, 3, 4, 5];
let first = a[0];
```

### Fonctions

```rust
fn main() {
    another_function(5);
}

fn another_function(x: i32) {
    println!("La valeur de x est : {}", x);
}

// Avec une valeur de retour
fn five() -> i32 {
    5 // Pas de point-virgule = expression de retour
}
```

### Contrôle de flux

```rust
// if/else
let number = 3;
if number < 5 {
    println!("condition was true");
} else {
    println!("condition was false");
}

// Boucle `loop`
let mut counter = 0;
let result = loop {
    counter += 1;
    if counter == 10 {
        break counter * 2; // `break` peut retourner une valeur
    }
};

// Boucle `while`
let mut number = 3;
while number != 0 {
    println!("{}!", number);
    number -= 1;
}

// Boucle `for` (la plus courante)
let a = [10, 20, 30, 40, 50];
for element in a.iter() {
    println!("la valeur est : {}", element);
}
```

### Structures (`struct`)

```rust
struct User {
    username: String,
    email: String,
    active: bool,
}

let mut user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
};

user1.email = String::from("anotheremail@example.com");
```

### Énumérations (`enum`)

`enum` est incroyablement puissant en Rust, surtout avec `match`.

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

fn process_message(msg: Message) {
    match msg {
        Message::Quit => println!("Quit"),
        Message::Move { x, y } => println!("Move to x: {}, y: {}", x, y),
        Message::Write(text) => println!("Text: {}", text),
        Message::ChangeColor(r, g, b) => println!("Color: {}, {}, {}", r, g, b),
    }
}
```

### `Option` et gestion des erreurs

Rust n'a pas de `null`. À la place, on utilise `Option<T>`, qui peut être `Some(valeur)` ou `None`.

```rust
fn find_user(username: &str) -> Option<String> {
    if username == "admin" {
        Some(String::from("admin_user"))
    } else {
        None
    }
}

match find_user("admin") {
    Some(user) => println!("Utilisateur trouvé : {}", user),
    None => println!("Utilisateur non trouvé."),
}
```

Pour les erreurs plus complexes, on utilise `Result<T, E>`.

```rust
use std::fs::File;

fn open_file() -> Result<File, std::io::Error> {
    let f = File::open("hello.txt")?; // Le `?` propage l'erreur
    Ok(f) // Si tout va bien, on retourne Ok(valeur)
}
```
---

## Liens pour aller plus loin

- **[The Rust Book](https://doc.rust-lang.org/book/) :** La bible. C'est le point de départ officiel et il est excellent.
- **[Rust by Example](https://doc.rust-lang.org/stable/rust-by-example/) :** Apprendre en faisant, avec des tonnes d'exemples de code.
- **[The Cargo Book](https://doc.rust-lang.org/cargo/) :** Tout sur Cargo, le gestionnaire de paquets et outil de build de Rust.
- **[Exercism Rust Track](https://exercism.org/tracks/rust) :** Des exercices pratiques avec des mentors pour t'aider.

## Le mot de la fin

Rust peut sembler intimidant, mais c'est un investissement qui en vaut la peine. La rigueur de son compilateur te force à devenir un meilleur développeur. Une fois que tu auras passé le cap de l'ownership, tu te sentiras invincible !

Alors, prêt à devenir un Rustacéen ? 🦀

<!-- Placeholder pour une image amusante sur Rust -->
![Ferris the crab](assets/ferris.png)
