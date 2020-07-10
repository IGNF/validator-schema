# IGNF/validator-schema

## Description

Cette documentation décrit schématiquement les modèles JSON de validation du validateur `IGNF/validator` pour ses versions `4.0` et supérieures.

## Concept pour la modélisation des données

### Table

Le concept de [Table.json](Table.json) est utilisé pour modéliser une table et ses colonnes.

| Propriété           | Type              | Description                                  | Obligatoire |
| ------------------- | ----------------- | -------------------------------------------- | :---------: |
| `table.name`        | `string`          | Le nom de la table au niveau système         |      O      |
| `table.title`       | `string`          | Le nom de la table pour affichage            |      N      |
| `table.description` | `string`          | La description de la table                   |      N      |
| `table.columns`     | `Column[]`        | La description des colonnes de la table      |      O      |

_Propriété supportée à partir de la version 4.1 du validateur_

| Propriété           | Type              | Description                                  | Obligatoire |
| ------------------- | ----------------- | -------------------------------------------- | :---------: |
| `table.primaryKey`  | `string[]`        | Colonne(s) correspondant à la clé primaire   |      N      |

La clé primaire `PrimaryKey` est définie sous forme d'une chaîne de caractère ("ID" ou "C1,C2")


### Column

Le concept de [Column.json](Column.json) est utilisé pour modéliser une table et ses colonnes.

| Propriété            | Type                   | Description                          | Obligatoire |
| -------------------- | ---------------------- | ------------------------------------ | :---------: |
| `column.name`        | `string`               | Le nom de la table au niveau système |      O      |
| `column.title`       | `string`               | Le nom de la table pour affichage    |      N      |
| `column.description` | `string`               | La description de la table           |      N      |
| `column.type`        | [Type](types/index.md)          | Le type de la colonne                |      O      |
| `column.constraints` | `ColumnConstraints`    | Les contraintes sur la colonne       |      N      |

### ColumnConstraints

| Propriété   | Type              | Description                                                                  |
| ----------- | ----------------- | ---------------------------------------------------------------------------- |
| `required`  | `boolean`         | Contrainte interdisant une valeur nulle ou vide                              |
| `unique`    | `boolean`         | Contrainte d'unicité au sein de la table                                     |
| `enum`      | `any[]`           | Contrainte d'appartenance à une liste de valeur                              |
| `pattern`   | `string`          | Contrainte sous forme d'une expression régulière (syntaxe JAVA)              |
| `maxLength` | `integer`         | Contrainte sur la longueur maximale d'une chaîne de caractère                |
| `reference` | `ReferenceTarget` | Contrainte de correspondance entre la valeur et celle d'une table référencée |

 _Contraintes supportées à partir de la version 4.1 du validateur_

| Propriété            | Type                   | Description                          |
| -------------------- | ---------------------- | ------------------------------------ |
| `minimum`   | `any`             | Contrainte sur la valeur minimale autorisée (incluse)                        |
| `maximum`   | `any`             | Contrainte sur la valeur maximale autorisée (incluse)                        |
| `minLength` | `integer`         | Contrainte sur la longueur minimale d'une chaîne de caractère                |


La cible d'une référence (`ReferenceTarget`) est définie sous forme d'une chaîne de caractère :

`{TABLE_CIBLE}.{COLONNE_CIBLE}`

Remarques :

* Moyennant des références à des tables statiques, les références permettront de gérer plus proprement l'appartenance à des listes de valeur codifiée (`reference: doc_urba_type(code)`) que l'utilisation des énumérés.
* Dans un premier temps, les clés étrangères ne sont pas supportées mais il sera possible d'étendre le modèle pour être en mesure de définir la clé étrangère `(TYPEPSC,STYPEPSC) REFERENCES prescription_urba_type(code,sous_code)` au niveau de `ZONE_URBA` dans les standards CNIG.


### Document

Le concept de [Document.json](Document.json) est utilisé pour modéliser un dossier contenant des fichiers.

Ces fichiers peuvent être des tables, des sous-dossiers, des PDF ou des fichiers de métadonnées.

| Propriété                | Type                  | Description                                              | Obligatoire |
| ------------------------ | --------------------- | -------------------------------------------------------- | :---------: |
| `document.name`          | `string`              | Le nom du modèle au niveau système (ex : cnig_PLU_2013)  |      O      |
| `document.title`         | `string`              | Le nom du modèle pour l'affichage                        |      N      |
| `document.description`   | `string`              | La description du modèle                                 |      N      |
| `document.files`         | `File[]`              | La liste des fichiers attendus dans le dossier           |      O      |
| `document.constraints`   | `DocumentConstraints` | Constraintes au niveau du document                       |      N      |

### DocumentConstraints

| Propriété                        | Type     | Description                                              | Obligatoire |
| -------------------------------- | -------- | ---------------------------------------------------------| :---------: |
| `document.folderName`            | `RegExp` | Nom de dossier attendu pour le document                  |      N      |
| `document.metadataSpecification` | `string` | Valeur attendue dans la fiche de métadonnée              |      N      |

### File

| Propriété          | Type       | Description                                        | Obligatoire |
| ------------------ | ---------- | -------------------------------------------------- | :---------: |
| `file.name`        | `string`   | Le nom identifiant le fichier                      |      O      |
| `file.title`       | `string`   | Le nom du fichier pour affichage                   |      N      |
| `file.description` | `string`   | La description du fichier                          |      N      |
| `file.type`        | `FileType` | Le type de fichier                                 |      O      |
| `file.path`        | `RegExp`   | Chemin relatif par rapport à la racine du document |      N      |

### FileType

| Type        | Description                                                                               |
| ----------- | ----------------------------------------------------------------------------------------- |
| `directory` | Dossier permettant d'en valider l'existence                                               |
| `metadata`  | Fiche de métadonnées XML au format ISO 19115 (validation profile INSPIRE & CNIG pour GpU) |
| `pdf`       | Fichier `.pdf`                                                                            |
| `table`     | Table de données géographique ou non (`.csv`, `.dbf`, `.shp`, `.geojson`, `.gml`)         |
