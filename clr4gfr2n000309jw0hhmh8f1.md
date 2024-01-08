---
title: "Course Code Scheme"
datePublished: Mon Jan 08 2024 05:00:42 GMT+0000 (Coordinated Universal Time)
cuid: clr4gfr2n000309jw0hhmh8f1
slug: course-code-scheme
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/rH8O0FHFpfw/upload/e3d55555fc7b6e476e5c084724b6a305.jpeg
tags: rust, education, planning

---

The goal of this article is to go through the current plan of our curriculum arm at MoKa Reads and discuss the course code scheme.

Let's break down the scheme:

* Length: 6 Characters
    
    * Language Identifier: 2 characters
        
    * Difficulty Identifier: 1 character
        
        * Beginner: `B`
            
        * Intermediate: `I`
            
        * Advanced: `A`
            
    * Character Identifier: 3 characters (can be padded by zeros)
        

Consider the course code `PYB001`, this would break down to:

* `PY`: Python course
    
* `B`: Beginner difficulty
    
* `001`: First course made for it
    

We will use semantic versioning for our course code scheme to track all changes made during its development, and for users to know any breaking changes.

In terms of Language Identifiers, for `v0.1` we have the current table:

| Language | Identifier |
| --- | --- |
| Python | `PY` |
| Java | `JA` |
| C++ | `C+` |
| JavaScript | `JS` |
| Ruby | `RB` |
| C# | `C#` |
| Swift | `SW` |
| PHP | `PH` |
| TypeScript | `TS` |
| Go | `GO` |
| Kotlin | `KT` |
| Rust | `RS` |
| HTML/CSS | `HC` |
| Zig | `ZG` |
| C | `CC` |

We will also provide an official library that will handle parsing the course codes and each `Major.minor` version will directly reflect the course code scheme version, while patches will be reserved for fixes or bugs towards the actual library.

A look at the prototype parser is the following:

```rust
use std::str::FromStr;

#[derive(Debug)]
enum Language {
    Python,
    Java,
    CPlusPlus,
    JavaScript,
    Ruby,
    CSharp,
    Swift,
    PHP,
    TypeScript,
    Go,
    Kotlin,
    Rust,
    HTMLCSS,
    Zig,
    C,
}

impl FromStr for Language {
    type Err = &'static str;

    fn from_str(s: &str) -> Result<Self, Self::Err> {
        match s {
            "PY" => Ok(Language::Python),
            "JA" => Ok(Language::Java),
            "C+" => Ok(Language::CPlusPlus),
            "JS" => Ok(Language::JavaScript),
            "RB" => Ok(Language::Ruby),
            "C#" => Ok(Language::CSharp),
            "SW" => Ok(Language::Swift),
            "PH" => Ok(Language::PHP),
            "TS" => Ok(Language::TypeScript),
            "GO" => Ok(Language::Go),
            "KT" => Ok(Language::Kotlin),
            "RS" => Ok(Language::Rust),
            "HC" => Ok(Language::HTMLCSS),
            "ZG" => Ok(Language::Zig),
            "CC" => Ok(Language::C),
            _ => Err("Invalid language identifier"),
        }
    }
}

#[derive(Debug)]
enum Difficulty {
    Beginner,
    Intermediate,
    Advanced,
}

impl FromStr for Difficulty {
    type Err = &'static str;

    fn from_str(s: &str) -> Result<Self, Self::Err> {
        match s {
            "B" => Ok(Difficulty::Beginner),
            "I" => Ok(Difficulty::Intermediate),
            "A" => Ok(Difficulty::Advanced),
            _ => Err("Invalid difficulty identifier"),
        }
    }
}

#[derive(Debug)]
struct CourseCode {
    language_identifier: Language,
    difficulty: Difficulty,
    character_identifier: String,
}

impl FromStr for CourseCode {
    type Err = &'static str;

    fn from_str(s: &str) -> Result<Self, Self::Err> {
        if s.len() != 6 {
            return Err("Invalid course code length");
        }

        let language_identifier = Language::from_str(&s[..2])?;
        let difficulty = Difficulty::from_str(&s.chars().nth(2).unwrap().to_string())?;
        let character_identifier = s[3..].to_string();

        Ok(CourseCode {
            language_identifier,
            difficulty,
            character_identifier,
        })
    }
}

fn main() {
    let input = "pyb001".to_uppercase();

    match CourseCode::from_str(&input) {
        Ok(course_code) => {
            println!("Language Identifier: {:?}", course_code.language_identifier);
            println!("Difficulty: {:?}", course_code.difficulty);
            println!("Character Identifier: {}", course_code.character_identifier);
        }
        Err(err) => println!("Error: {}", err),
    }
}
```

There is still the following considerations towards course codes:

* How to store them
    
* What api to create one
    
* Process towards creating/publishing a course
    

While this article only covers the course code scheme, we hope to answer other questions such as curriculum format, standards, CEI (our criteria board) and others.