### CommissionCalculator
  #<Order id: 240, recurly_uid: "31807911d80cbc9804c5343766e673b8", product_id: 7, email: "mail@zaidrahman.org", affiliate_code: nil, created_at: "2015-03-17 03:40:31", updated_at: "2015-03-17 03:40:31">
  This was pushed up at 1:46 at March 18, 2015.   Everthing before this will need to be adjusted.

### Steps to create a new offer and product.
  Create a new Product - url will be how it is accessed from the sales form.  See below for all fields in database
  Create a new Offer


### Views

  All items have 3 possible forms
    Order Form - Associated with a specific product.  Accessible from products/:product_url
    Sales Page - Associated with an offer, located in app/views/offers/new_sale/offer_name.  Is what they see when they access it and have not baught anything in the pipeline.
    Upselling Page - Associated with an offer, located in app/views/offers/up_sell/offer_name.  What they see if they are in the pipeline and adding on a product or promotion.




### Traffic Flow

There are products and offers.

Offers display 1..* many products.

For each offer there is either a new sale page or an upgrade page.

All pages have a direct sale page except the 2 year plan, however only a few have upgrades (for instance you can't upgrade the 4 strategies page).

Access the offers by visiting
  stockstotrade/:offer_url
    powerhouse
    signals
    signals_2year
    underground

Access products to enter credit cards by visiting
  products/:product_url
    powerhouse
    fxannual
    fx2year
    fxmonth
    underground

Keep in mind that these values for the url can change overtime and can be accessed from the database


There is a helper controller that returns the users proper spot in the pipeline.  The default is /offers/powerhouse.
  /progress?email=email@example.com&recurly_uid=randomjibberish

  Right now we only have the email portion implemented of the progress data.

Models

Product
  recurly_plan_code: how the product is referenced in recurly as plan code
  offer_id: the next offer that this should go to after it is purchased
  addon_or_upgrade: string that is either 'addon' or 'upgrade'.  Determines if this offer will add on to another or if it will replace it
  name: is displayed in the product summary of sales page in bold
  line_description: right below the name in product summary
  price_description: right below the price in the product summary
  url: the direct way to access this.  /products/:url

Offer
  url: the /:brandname/:url that is looked for.  For instance /stockstotrade/

Controllers
  Upgrades
    handles upgrades through a post.  Post must have the recurly_uid that they want to upgrade


Upgrade links are a little tricky because the text is all static in the database for the offer but is also dynamic for the users recurly uid.
  This is handed in the offers controller through rendering an html document with the same *.html.erb as the url of the offer.  So underground will render teh
  underground.html.erb.

  The upgrade is triggered if an email is passed in through the URL


 ### CLoseout dialogue
   signals_promo and signals have a close out dialogue that allows them to display a longer sales page to users.  That logic is stored in assets/javascript/closeout_splashpage.js.  To use it

   1. include javascript at the BOTTOM of any file you are wanting to add a splash page to
    <%= javascript_include_tag "closeout_splashpage" %>
   2. create a div on your page that is hidden like this.
     <div id="signals-closeout" style="display:none;">
       HIDDEN SALES PAGE
     </div>
