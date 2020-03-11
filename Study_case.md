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
