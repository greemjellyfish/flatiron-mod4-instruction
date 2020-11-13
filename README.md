# flatiron-mod4-instruction
mod 4 react/rails project step by step instruction and other info 

# https://learn.co/tracks/module-4-web-development-immersive-2-1/react-project-mode/react-project/react-project-requirements
id: orangeNewYorkCreativeAgency@gmail.com pw: haveFun1

# Rei's MEDIUM blog rails setup
https://levelup.gitconnected.com/building-a-small-rails-api-with-serializers-32e3e69a078

# Mod3 vanila JS and Rails(setting up backend is same as mod 3)
https://github.com/learn-co-curriculum/mod3-project-week-setup-example

# active_model_serializer
https://github.com/rails-api/active_model_serializers/blob/0-10-stable/docs/general/getting_started.md



# backend rails setup step by step
1. rails new <my-project> --api -T --database=postgresql (run `rails db:create`)
2. install rack-cors (https://github.com/cyu/rack-cors)
(config/initializers/cors.rb)

3. may be use active-model-serializers(https://github.com/rails-api/active_model_serializers/tree/0-10-stable)
`gem 'active_model_serializers', '~> 0.10.0'`
4. rails s -p <some_number_thats_not_3000>
`rails s -p 3001`

5. `rails g model Wine name:string producer:string ingredient:string type:string region:string country:string price:float rating:integer note:string`

`rails g model Review wine_id:integer date:string occasion:string aroma:string color:string acidity_level:string taste:string`


6. (app/models/wine.rb)
add `has_many :reviews
    validates_presence_of :name`

7. (app/models/review.rb)
add `belongs_to :wine`

8. generate nested paths under controllers.
 `rails g controller api/v1/Wines`
 `rails g controller api/v1/Reviews`

9. (app/controllers/api/v1/wines_controller.rb)
    class Api::V1::WinesController < ApplicationController
    def index
        @wines = Wine.all
        render json: @wines
    end

    def show
        @wine = Wine.find(params[:id])
        render json: @wine
    end

    def new
        @wine = Wine.new
    end
    
    def create
        @wine = Wine.create(check_params)
        if @wine.valid?
            render json: @wine
        else
            render json: {errors: @wine.errors.fullmessages}
        end
    end

    def update
        @wine = Wine.find(params[:id])
        @wine.update(check_params) 
        # check_params represent the data from the front-end
        if @wine.valid?
            render json: @wine
        else
            render json: {errors: @wine.erros.full_messages}
        end
    end

    def destroy
        wine = Wine.find(params[:id])
        wine.destory

        render json: quote
    end
    
    private

    def check_params
        params.permit(:name, :producer, :ingredient, :type, :region, :country, :price, :rating, :note)
        # The check_params method is permitting the keys ':name, :producer, :ingredient, :type, :region, :country, :price, :rating,  and  :note' whenever a PATCH request is made.
    end
end

10. (app/controllers/api/v1/reviews_controller.rb)
class Api::V1::ReviewsController < ApplicationController
    def index
        @reviews = Review.all
        render json: @reviews
    end

    def show
        @review = Review.find(params[:id])
        render json: @review
    end

    def new
        @review = Review.new
    end

    def create
        @review = Review.create(check_params)
        
        if @review.valid?
            render json: @review
        else
            render json: {errors: @review.errors.full_messages}
        end
        
    end

    def update
        @review = Review.find(params[:id])
        @review.update(check_params)
              # check_params represent the data from the front-end
        if @review.valid?
            render json: @review
        else
            render json: {errors: @review.errors.full_messages}
        end
    end

    def destroy
        review = Review.find(params[:id])
        review.destroy

        render json: review
    end


    private

    def check_params
        params.permit(:time, :location, :wine_id, :aroma)
            # The check_params method is permitting the keys ':name, :producer, :ingredient, :type, :region, :country, :price, :rating,  and  :note' whenever a PATCH request is made.
    end
end

11. (config/routes.rb)

  namespace :api do
    namespace :v1 do
      resources :wines, only: [:index, :show, :new, :create, :update, :destroy] do
        resources :reviews, only: [:index, :show, :new, :create, :update, :destroy]
      end
    end
  end

12. The models, controllers, and routes are all set and ready to go. Now run the following commands
`rails db:create && rails db:migrate`

13. (db/seeds.rb)
 movies = Movie.create([{ name: 'Star Wars' }, { name: 'Lord of the Rings' }])
`rails db:seed`

14. check rails c 
Wine.all 
or
check postman

15. rails routes
or
(postman)(preview) http://localhost:3001/rails/info/routes

16. add serializer
serializer: Interweb for Frontend<->backend
It converts our data into a format that can be easily transferred across a network as a str and reconstructed later. 
`rails g serializer wine`
(app/serializers/wine_seriazlier.rb)

```rb
class WineSerializer < ActiveModel::Serializer
  has_many :reviews
  attributes :id, :name, :producer, :ingredient, :wine_type, :region, :country, :price, :rating, :note
end
```

`rails g serializer review`
(app/serializer/review_serializer.rb)

```rb
class ReviewSerializer < ActiveModel::Serializer
  belongs_to :wine
  attributes :id, :wine_id, :date, :occasion, :aroma, :color, :acidity_level, :taste
end
```


# tips to destroy models
rails destroy model Review
rails destroy model Wine


# frontend react setup command
1. `npm install -g create-react-app`
2. `create-react-app my-project-client`
<!-- 3. follow pokemon GREEM-NOTE.md
4. react image upload made easy(https://www.youtube.com/watch?v=XeiOnkEI7XI)
`yarn add axios`
5. install firebase(cloud service)
`npm install firebase-tools -g`
`firebase init hosting` -->




# file uploading to cloud
https://medium.com/better-programming/how-to-upload-files-in-a-react-and-rails-app-69c31a9cf9b7


1. signup on cloudinary

Cloud name:	
devzsgipb

API Key:	
336247349277763

API Secret:	
4igWOJ_gyARHRJR6PZIsRgl-bmk

2. (backend)
gem 'cloudinary'

3. add img & vid columns
`rails generate migration add_img_vid_to_wines image:string video:string`
-check controller, serializer and add img attr

(migration/file) `add_colum :wines, image, string`
`rake db:migrate`
4. config/initializers/cors
5. (config/initializers) add cloudinary.rb
6. (controller/wines) add below 
```rb

        byebug
        image = Cloudinary::Uploader.upload(params[:image])
        video = Cloudinary::Uploader.upload(params[:video], :resource_type => :video)
        wine = Wine.create(image: image["url"], video: video["url"])


in byebug check below
1. params[:image]
2. image
3. image["url"] 
    (image["url"] = "http://res.cloudinary.com/devzsgipb/image/upload/v1596061128/awug3z8aa7w1uuutmqui.jpg")
```
7. ???? bonus : just added to delete method. check with rei

8. ??? React from Setup


7/30/2020
added img in data, Wine.destoy_all, added img links, db:seed, changed controller and serializer

11:20am 
list of reviews 
pokemon note

