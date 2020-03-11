# Ruby_on_Rails Cheat Sheet:

Pour utiliser le projet d’un repo rails de qlqn d’autre :
``` Ruby
bundle update --bundler
bundle install
rake db:migrate
```
Puis ```rails server``` et tester ```http://localhost:3000/books```
***
## Introduction
```rails server``` pour lancer le serveur

```rails g controller pages home``` generer du code :  
    - un dossier pages dans __app/views__.  
    - un fichier __app/views/home.html.erb__.  
    - un fichier controlleur __app/controller/pages_controller.rb__.  
    - ajoute une route ```get 'pages/home'``` dans __config/routes__.  
    - crée __app/helpers/pages_helper.rb__.  
    - crée __app/assets/stylesheets/pages.scss__.  

Une variable créée dans le controller comme ceci :
``` Ruby
class PagesController < ApplicationController
  def home
    @variable = 4
  end
end
```
(avec un ```@```) sera récupérable dans le html avec ```<%= @variable %>```.

Configurer __config/routes__ pour choisir le path de nos pages :  
``` Ruby
  get 'books' => 'books#index'
  get 'books/:id' => 'books#show'
  patch 'books/:id' => 'books#update'
  delete 'books/:id' => 'books#destroy'
  post 'books' => 'books#create'
  root 'pages#home'
  ```
  - ```get``` est la méthode de base, s'utilise avec les links (balises ```<a>```)  
  - ```patch``` avec les form (```form_tag```), pour __modifier__  
  - ```delete``` avec les form (```form_tag```), pour __supprimer__  
  - ```post``` avec les form (```form_tag```), pour __ajouter__  
  - ```root``` pour definir le chemin de ```http://localhost:3000/```  
  
__app/views/layouts/application.html.erb__ :  
Les lignes de Ruby dans le head permettent d’inclure tous vos fichiers CSS et JavaScript dans votre site sans avoir à rajouter une ligne par fichier.  
- Pour ajouter du CSS dans votre site, modifiez le fichier app/assets/stylesheets/application.css, ou les autres fichiers de ce même répertoire ;  
- Pour ajouter du JS dans votre site, modifiez le fichier app/assets/javascripts/application.js, ou les autres fichiers de ce même répertoire.
***
## La gestion des données  
``` Ruby
rails generate migration TableBooks
```  
Ce qui va générer dans le dossier __db/migrate/__ le fichier __*_table_books.rb__, avec la date et l’heure à la place de l’astérisque.  
``` Ruby
class TableBooks < ActiveRecord::Migration
  def change
    create_table :books
    add_column :books, :title, :string
  end
end
```
Comme les variables en ruby, les colonnes d’une base de données peuvent être de différentes natures, que l’on appelle types. En voici une liste incomplète, à titre indicatif :  
- __string__ pour les textes de moins de 255 caractères ;  
- __text__ pour les textes pouvant faire plus de 255 caractères ;  
- __integer__ pour un nombre sans virgule ;  
- __float__ pour un nombre à virgule ;  
- __datetime__ pour une date et heure.  

``` Ruby
rake db:migrate
```
Pour prendre en compte la migration.  
Pour exploiter notre base de données avec Rails, pour ajouter et supprimer des lignes, il nous manque un fichier qui fait la liaison entre notre base et notre code. Créez le fichier __app/models/book.rb__ avec ce contenu :  
``` Ruby
class Book < ActiveRecord::Base
end
```

Pour supprimer des données :
``` Ruby
mon_livre = Book.find(1)
mon_livre.destroy
```

- Créer un nouveau model Category et donc une nouvelle migration pour le stocke en bdd
``` Ruby
rails generate migration TableCategory
```
- Modifier le fichier créé  
- ``` Ruby
  rake db:migrate
  ```
- Créer un nouveau model (au singulier): __app/models/category.rb__
  ``` Ruby
  class Category < ActiveRecord::Base
    has_many :books
  end
  ```
  ***
  ``` Ruby
  b.errors.to_hash
  ```
  
  ``` Ruby
  render :show
  ```
  => redirige vers la page html de show quand bien même on ne se trouve pas dans la fonction show du controller
***
### Les scopes
- first  
- last
- where() => attention renvoie un tableau
##### creer son propre scope :
modifier le modele __app/models/book.rb__ :
``` Ruby
scope :french, -> { where(category_id: 1) }
```
***
Pour les applications Rails, après le ```bundle install```, il ne faut pas oublier le ```rake db:create``` (parfois facultatif) puis le ```rake db:migrate```.
