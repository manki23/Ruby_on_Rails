# Steps to do the study case

## Step 1
1. __Créer un modèle Contact avec les attributs suivants :__
- Name
- Phone
- Email
- Interest
``` Ruby
rails generate migration TableContacts

cat db/migrate/20200311191326_table_contacts.rb 
class TableContacts < ActiveRecord::Migration[6.0]
  def change
    create_table :contacts do |t|
      t.string :name
      t.string :phone
      t.string :email
      t.text :interest
    end
  end
end

rails g controller pages index

rake db:create  

rake db:migrate

cat app/models/contact.rb
class Contact < ActiveRecord::Base

  validates :name, presence: {
    message: "Name must be filled"
  }

  validates :phone, uniqueness: {
    message: "A contact already exist with this phone number"
  }

  validates :email, uniqueness: {
    message: "A contact already exist with this mail address"
  }
end
```
***
## Step 2
2. __Créer trois routes :__  
- __Index__ : pour voir la liste des inscrits
- __New__ : pour s'inscrire sur la newsletter
- __Create__ : pour créer le contact
``` Ruby
cat config/routes.rb
Rails.application.routes.draw do
  root 'pages#home'
  get 'index' => 'pages#index'
  get 'new' => 'pages#new'
  get 'create' => 'pages#create'
  # For details on the DSL available within this file, see https://guides.rubyonrails.org/routing.html
end
```
***
## Step 3
*__créer les controllers et les views correspondant.__*
***
## Step 4
4. __Mettre en place les vues__
- Index : afficher sous forme de liste l'ensemble des gens inscrits  
- New : afficher le formulaire pour pouvoir s'inscrire  
  - Utiliser le form_with tag: https://guides.rubyonrails.org/form_helpers.html (rajouter l'option local: true au tag form_with pour rester en HTML et non en AJAX)
  - Mettre en place les bons champs pour chaque attribut du contact et mettre un champ select pour l'attribut interest avec les options suivantes ["science fiction", "comedie", "drame"]

``` Ruby
rails generate migration TableInterests
class TableInterest < ActiveRecord::Migration[6.0]
  def change
    create_table :interests
    add_column :interests, :name, :string
  end
end
rails generate migration TableNewsletter_subscribers
class TableInterest < ActiveRecord::Migration[6.0]
  def change
    create_table :newsletter_subscribers
    add_column :newsletter_subscribers, :subscriber_id, :integer
    add_index :newsletter_subscribers, :subscriber_id
  end
end
rails generate migration ModifyTableContact_InterestToInterestId
class TableInterest < ActiveRecord::Migration[6.0]
  def change
    remove_column :contacts, :interest, :text
    add_column :contacts, :interest_id, :integer
    add_index :contacts, :interest_id
  end
end
rake db:migrate
```
***
## Step 5
5. Rajouter la bibliothèque javascript SlimSelect (http://slimselectjs.com) sur le select pour permettre une meilleure saisie   

__PROBLEM__ : je n'ai pas trouvé comment inclure proprement le javascript pour Slim Select, donc je l'ai mis en dur dans la view de new.html.erb, c'est à changer car cela crée ponctuellement des erreurs.
``` Ruby
rails generate migration TableContact_Interests
class TableContact_Interests < ActiveRecord::Migration[6.0]
  def change
    remove_column :contacts, :interest_id, :integer
    create_table :contact_interests
    add_column :contact_interests, :contact_id, :integer
    add_index :contact_interests, :contact_id
    add_column :contact_interests, :interest_id, :integer
    add_index :contact_interests, :interest_id
  end
  rake db:migrate
```
=> changer les modeles, les controller et les views en conséquence.  
``` Ruby
In the controller :
params[:multi].each do |one|
          ContactInterest.create(contact_id: @contact.id, interest_id: one)
In the view :
<%= select_tag "multi", options_from_collection_for_select(@interests, 'id', 'name'), :multiple => true, :required => true %>
In the model :
class ContactInterest < ActiveRecord::Base
  validates :contact_id, presence: {
    message: "contact_id must be filled"
  }

  validates :interest_id, presence: {
    message: "interest_id must be filled"
  }
end
```
***
## Step 6
6. Mettre en place un autocomplete sur le champ select. Donc à chaque fois que je tape une lettre dans le champ select, un call AJAX s'opère et renvoie dans la liste des options du select les choix possibles, comme lorsqu'on tape sur la barre de recherche google  

Est-ce que ```:autocomplete => true``` ajouté au *select_tag* suffit ?
j'en doute mais je ne trouve pas comment faire autrement avec Ajax.
***
## Step 7
done (avec une page admin en bonus)
