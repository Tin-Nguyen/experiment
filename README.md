=== Order error redirect to link
  This happens on upgrades_controller only.  If their is an error we present them with the option to update their account in recurly.
  <%= flash[:error].html_safe if flash[:error] %>

=== Order Upgrading and commissions
  All orders are sent to Steamads.  However the amount for a 2 year upgrade from a 1 year needs to be reduced so that they only get
  the difference between the two.  Otherwise a user could get paid for a 1 year and then again for a 2 year.

  This is handled via the CommissionCalculator class.  You pass it in current order and it determines if they have a 1 year purchase previously.


=== Requirements
Route
  /:brand_index/:product_url

A user will visit a form on the site.  Once they successfully sign up they will be redirected to a page that is next in the offer.

Really a user could put whatever in the brand unless we want to limit that, the product URL is next.

=== Layouts
  Order forms are rendered using the layouts/order_form partial and passing in the relevant info

=== Initial Design

  Models

    order < AR
    recurly_ui# unique id inside of recurly
    , product_id, email, referral_code

    brand < AR
    name logo

    product < AR
      name
      brand_id
      recurly_plan_code
      upsell_id  #id of the product that is next in the offer


    offer_trail # MAYBE replace cross_sell_id
    (current_step) product_id, next_step(product_id)
      - each product can only have one offer_trail as a current_step
      - index this on current_step

  Services
    path_finder < PORO
    finds the next path for a given offer, probably defaults to thank you or something

=== Considerations

  Affiliate
    206-508-1318

  Recurly
  normal user stuff
  plan stuff associated with a product

  account_code, username, email, first_name, last_name, company_name, vat_number, tax_exempt,
    address: address1, address2, city, state, zip, country, phone


=== Basic Order Workflow

  Person visits /product/url, which is looked up by the products controller
    - Products controller finds by product.url => url
    - compiled_form is rendered, they enter info

  On submit and recurly data entered successfully, we pause the form and go to
    - Post to /orders
    - Order controller create action

  Order create makes an new OrderBroker with the params (recurly_token, recurly_plan and email)
    - OrderBroker receives message conduct_order_process
    - That response is rendered in js from create.js.erb
    - The order broker walks through
      - billing recurly
      - creating an order

  The JS is sent back to the client

=== Second Design
  OffersProducts holds the items that will be referenced so that an offer can have multiple products.
  Product will then know the next offer to go to after something it is baught through offer_id

  Offer will maintain the brand_id

  Relations
    OfferProducts belongs_to products and offers


=== Questions/improvements

Question - Do you want to be able to send someone to a link and that AUTOMATICALLY create a recurly recuring subscription by the name of the link, if so how much would cost be?
  - How do we handle people signing up that already have baught from you...  ie someone buys one thing and then comes back later and buys somehing new.
  - Upsell vs addon

=== API Documentation

  Basic Auth -
    name: "mvsemawldpmfxg"
    password: "DSrWu2crtJll2EZuOmZEP-zOvj"


  List the orders for a particular email address

  REQUEST: GET /api/orders/:email
  RETURNS:

    If email not found, 404

    If found
      [
        {
          "product_id":1,
          "email":"Raj@j.com",
          "product":
            {
              "name":"Powerhouse Product",
              "line_description":"Lifetime Membership",
              "price_description":"one time",
              "recurly_plan_code":"4pwr",
              "investimonials_id":5
            }
        }
      ]


  List all emails of users in system

  REQUEST: GET /api/users
  RETURNS: ['a@b.com', 'a@c.com']

  List all emails of users that have baught a specific product

  REQUEST: GET /api/users?recurly_plan_code=something
  RETURNS: ['a@b.com']

  Here are all the current recurly plan codes
    "4pwr", "fxcoreoffer", "fxmo", "uta", "fxcoreofferpromo", "fxmopromo", "fxupgrade2yr"

  Recurly Webhook:

  - Webhook url: domain/recurly/recurly_notification
  - Username: dustinlumsden
  - Password: Ultor2015
