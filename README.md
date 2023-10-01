<p align="center"><a href="https://slick-pay.com" target="_blank"><img src="https://devapi.slick-pay.com/public/assets/images/dark_logo.png" width="380" height="auto" alt="Slick-Pay Logo"></a></p>

## Description

[Slick-Pay](https://slick-pay.com) payment gateway within [Shopify](https://www.shopify.com) payment methods.

* [Creating the payment method on the store](#creating-the-payment-system-on-the-store)
* [Setting up the Shopify integration from your Slick-Pay account](#setting-the-shopify-integration-from-your-slick-pay-account)
* [More help](#more-help)

## Creating the payment method on the store

### Creating payment method

After being logge-in your store dashboard, please go to **Payments** using your side menu : **Settings** >> **Payments**

From here, scroll down until **Manual payment methods** section, then click **Add manual payment method** then **Create custom payment method**.

<img src="https://devapi.slick-pay.com/public/assets/images/intergration-shopify/01.png" width="820" height="auto" alt="Slick-Pay Shopify Create custom payment method settings">

A window will appear with three fields, fill them out as follows:
* **Custom payment method name** : *Slick-Pay payment gateway*
* **Additional details** : *On thank you page you will find the Pay now button, use it to proceed to your order payment.*
* **Payment instructions** : *It's pretty simple ! All you have to do is to enter your payment card information, then proceed the payment.*

Then click on the **Activate** button to confirm and save your new payment system.

<img src="https://devapi.slick-pay.com/public/assets/images/intergration-shopify/01.png" width="626" height="auto" alt="Slick-Pay Shopify Create custom payment method modal">

### Add the online payment script

Once your new payment method has been created, please go to **Settings** >> **Checkout**, then scroll to the **Additional scripts** section and paste the script provided below which will display the button to redirect your customers to the payment gateway.

```
<script src="https://code.jquery.com/jquery-3.4.1.min.js" integrity="sha256CSXorXvZcTkaix6Yvo6HppcZGetbYMGWSFlBw8HfCJo=" crossorigin="anonymous">
</script>
<script>
    /**
     * Config vars
     */
    const publicToken = ""; // Put your Slick-Pay public key gotten from your Slick-Pay website account
    const successUrl = ""; // Create static page from your Shopify dashboard
    const failureUrl = ""; // Create static page from your Shopify dashboard


    /**
     * Slickpay doing the job
     */
    var buttonText = "Pay Now";
    const locale = "{{ checkout.locale }}";

    if (locale == 'fr') {
        buttonText = "Payer Maintenant";
    } else if (locale == 'ar') {
        buttonText = "إدفع الآن";
    }

    $("#btn-slickpay").html(buttonText);

    function slickpayTransfer() {

        var data = {
            "amount": {{ checkout.total_price }} / 100,
            "firstname": "{{ checkout.customer.first_name }}",
            "lastname": "{{ checkout.customer.last_name }}",
            "phone": "{{ checkout.customer.phone }}",
            "address": "{{ checkout.customer.default_address.summary }}",
            "email": "{{ checkout.customer.email }}",
            "items": [],
            "url": successUrl != '' ? successUrl : "{{ shop.url }}",
            "note": "shopifyID:{{ order_id }}"
        };

        {% for line in checkout.line_items %}
        data.items.push({
            name: "{{ line.title }}",
            price: "{{ line.final_line_price }}",
            quantity: "{{ line.quantity }}"
        });
        {% endfor %}

        $.ajax({
            url: 'https://prodapi.slick-pay.com/api/v2/users/invoices',
            data: JSON.stringify(data),
            type: "POST",
            headers: {
                "Content-Type": "application/json",
                "Authorization": `Bearer ${publicToken}`
            }
        })
        .done((response) => {

            if (response.success) {
                window.location.href = response.url;
            } else {
                window.location.href = failureUrl;
            }
        })
        .fail((error) => {
            window.location.href = failureUrl + "?error_code=" + error.status + "&description=" + error.responseJSON.message;
        });

    };
</script>
<div style="padding: 30px 0 10px; text-align: center;">
    <button style="padding: 15px 100px; background-color: #00a2ff; border-radius: 5px; color: #fff; font-size: 22px; font-weight: 600;" id="btn-slickpay" onclick="slickpayTransfer()" class="button__text">Pay Now</button>
</div>
```

<img src="https://devapi.slick-pay.com/public/assets/images/intergration-shopify/03.png" width="820" height="auto" alt="Slick-Pay Shopify Additional scripts">

**NOTE:** Don't forget to fulfil **publicToken**, **successUrl** and **failureUrl** variables values.
* **publicToken** : *Your public key availible from your Slick-Pay website account* ;
* **successUrl** : *A static page url, that you have to create to redirect your customers to once payment successfully completed* ;
* **failureUrl** : *A static page to redirect your customers to when an error occurs during the checkout process*.

### Creating custom Slick-Pay app

You have to create a custom application to allow Slick-Pay to update your store orders statuses once your customer payment ends successfully.

To do so, go to **Settings** >> **Apps and sales channels**.

At your right top screen, click on the **Develop apps** button then on the **Create an app** button.

You will see a window appear with three fields to fill in, fill them out as follows :
* **App name** : *Slick-Pay Payment Gateway*
* **App developer** : *contact@slick-pay.com*

<img src="https://devapi.slick-pay.com/public/assets/images/intergration-shopify/04.png" width="620" height="auto" alt="Slick-Pay Shopify Create an app">

Then click on the **Create app** button to create a new application.

Now, we will configure the **Admin API access scopes**.

To do so, first, click on the recently created application **Slick-Pay Payment Gateway**, then go to the **Configuration** tab.

On the **Admin API integration** section of the admin interface click the **Edit** button.

<img src="https://devapi.slick-pay.com/public/assets/images/intergration-shopify/05.png" width="820" height="auto" alt="Slick-Pay Shopify Admin API integration">

Scroll down to the **Orders** section and then check both checkboxes: **write_orders** and **read_orders**.

Don't forget to click on the **Save** button at the right top screen.

Last step relating to your Shopify store settings, you have to copy the **Admin API access token** from the **Slick-Pay Payment Gateway** app administrator interface.

This option is available from the **API Credentials** tab of the **Slick-Pay Payment Gateway** app.

Click on the **Show Once** link to display the token, make a copy in a safe place, you will need it later.

<img src="https://devapi.slick-pay.com/public/assets/images/intergration-shopify/06.png" width="820" height="auto" alt="Slick-Pay Shopify API Credentials">

## Setting up the Shopify integration from your Slick-Pay account

After logged-in to your [Slick-Pay](https://slick-pay.com) account, please enter your store url and the **Admin API access token** token as well.

<img src="https://devapi.slick-pay.com/public/assets/images/intergration-shopify/07.jpg" width="820" height="auto" alt="Slick-Pay Shopify settings">

## More help
* [Slick-Pay website](https://slick-pay.com)
* [Reporting Issues / Feature Requests](https://github.com/Slick-Pay-Algeria/quick-transfer/issues)