# Paying with Paidy or PayPal on the web

<p>
<img src="https://www.paypalobjects.com/images/checkout/latinum/Altpay_logo_paidy.svg" alt="Paidy Logo">
<img src="https://upload.wikimedia.org/wikipedia/commons/b/b5/PayPal.svg" width="90px" alt="PayPal Logo">
</p>

This integration uses the JavaScript SDK to accept Paidy payments


See a [hosted version](https://paypal-paidy-js-sdk.herokuapp.com/) of the sample

## How to run locally

1. Clone the repo  `git clone git@github.com:paypal-examples/paidy.git`


2. Copy the .env.example file into a file named .env
```
cp .env.example .env
```

3. Run `npm install`


4. configure your `.env` config file with your Paypal (Sandbox)
`CLIENT_ID` and `CLIENT_SECRET`

these can be obtained [here](https://developer.paypal.com/docs/api-basics/sandbox/credentials/)

5. Update `client/index.html` script src `clientId` param

   `https://www.paypal.com/sdk/js?client-id=<CLIENT_ID>&...`
   
6. `npm start`.

#### Listen to Webhooks

1.  Run `npm run webhook-proxy` take note of the `webhookId`.


2. Update your `.env` file `WEBHOOK_ID` value.


3. Restart the server `npm start`



&nbsp;


**Loading the JavaScript SDK**


The sdk requires the following query params to be configured on the script src to accept paidy payments.

| **Param**   |       **Value**     |
|----------|:-------------:|
| client-id |   PayPal ClientId  |
| components |  buttons,fields,marks,funding-eligibility |
| enable-funding |   paidy   |
| currency | JPY |

Example:

```
<script src="https://www.paypal.com/sdk/js?client-id=<CLIENT_ID>&components=buttons,fields,marks,funding-eligibility&enable-funding=paidy&currency=JPY"></script>
```

##### Order Payload

Please note Paidy orders are required to be created in `JPY`
```
{
  purchase_units: [
    {
      amount: {
        currency_code: "JPY",
        value: "49"
      }
    }
  ],
  payment_source: {
    paidy: {
      name": "山田　太郎",
      country_code: "JP",
      experience_context: {
        logo_url: "https://example.com/logo.jpg",
        locale: "ja-JP",
        return_url: `${window.location.origin}/success`,
        cancel_url: `${window.location.origin}/cancel`
      }
    }
  }
}
```

#### Components

**Mark**

 <img src="./assets/mark.png" width="60px" alt="Paidy Mark">
  
```
paypal
  .Marks({
    fundingSource: paypal.FUNDING.PAIDY
  })
  .render("#paidy-mark");
```

**PaymentFields**

Render the fields to capture required customer information.

It gives the option to prefil the customer name field if this is already obtained via `fields.name.value` property.

<img src="./assets/fields.png" width="540px" alt="Paidy Fields">

```
paypal
  .PaymentFields({
    fundingSource: paypal.FUNDING.PAIDY,
    style: {},
    fields: {
      name: {
        value: "",
      },
    },
  })
  .render("#paidy-fields");

```
**Style object:**
```
const style = {
  base: {
    backgroundColor: "white",
    color: "black",
    fontSize: "16px",
    fontFamily: '"Helvetica Neue", Helvetica, sans-serif',
    lineHeight: "1.4",
    letterSpacing: "0.3"
  },
  input: {
    backgroundColor: "white",
    fontSize: "16px",
    color: "#333",
    borderColor: "#dbdbdb",
    borderRadius: "4px",
    borderWidth: "1px",
    padding: "1rem"
  },
  invalid: {
    color: "red"
  },
  active: {
    color: "black"
  }
}
```

**Button**


 <img src="./assets/button.png"  width="540px" alt="Paidy Button">

```
paypal
  .Buttons({
    fundingSource: paypal.FUNDING.PAIDY,
    style: {
      label: "pay"
    },
    createOrder(data, actions) {
      /* see order payload info */
      return actions.order.create(order);
    },
    onApprove(data, actions) {
       console.log("order approved")
    },
    onCancel(data, actions) {
      console.log("onCancel called");
    },
    onError(err) {
      console.error(err);
    }
  })
  .render("#paidy-btn");
  ```



&nbsp;
