# Mellat Ckeckout:

Unofficial [Behpardakht Mellat Gateway](http://www.behpardakht.com/resources/Vpos.html) implementation in Node.JS

- [Installation](#installation)
  - [NPM](#npm)
  - [Yarn](#yarn)

- [Usage](#usage)
  - [Create An Instance](#create-an-instance)
  - [API](#api)
  - [Callbacks Instead Of Promises](#callbacks-instead-of-promises)

- [TODO](#todo)
  - [API](#api)
  - [Helper Methods](#helper-methods)
  - [Code](#code)

- [Contributing](#contributing)

## Installation

Install the package from 'npm' or 'yarn'.
### NPM

```bash
npm install mellat-checkout
```
### Yarn

```bash
yarn add mellat-checkout
```
## Usage:

### Create An Instance

Import the package:
```javascript
const MellatCheckout = require('mellat-checkout');
// or (ES6):
import MellatCheckout from 'mellat-checkout';
```
Then create an instance:
```javascript
const mellat = new MellatCheckout({
  terminalId: 'xxxxxxx',
  username: 'xxxxxxx',
  password: 'xxxxxxx',
  timeout: 10000, // Optional, number in millisecond (defaults to 10 sec)
  apiUrl: 'https://bpm.shaparak.ir/pgwchannel/services/pgw?wsdl', // Optional, exists (and may updated) in bank documentation (defaults to this)
});

// Initialize the client, this step is oprional
// but gives you more control over your flow
// and speeds up the first (and just first) request.
mellat.initialize.then(function () {
  console.log("Mellat client ready")
})
.catch(function (error) => {
  // you can retry here
  console.log("Mellat client encountered error:", error)
});
```

### API

#### Payment Request:

```javascript
mellat.paymentRequest({
  amount: 1000, // Payment Amount In Rials
  orderId: '12345678912', // OrderID Generated By You
  callbackUrl: 'https://call.back/mellat', // Payment Callback URL
  payerId: '0' // Optional
}).then(function (response) {
  if (response.resCode === '0') {
    console.log(response.refId);
  } else {
    console.warn('Gateway Error: ', response.resCode);
  }
}).catch(function (error) {
  console.error(error);
});
```

#### Payment Verification:

```javascript
mellat.verifyPayment({
  orderId: '12345678912', // OrderID Used In Payment Request
  saleOrderId: '12345678912', // Get From Payment Callback Post Params
  saleReferenceId: '5142510', // Get From Payment Callback Post Params
}).then(function (response) {
  if (response.resCode === '0') {
    console.log("Verified, Call settlePayment");
  } else {
    console.warn('Gateway Error: ', response.resCode);
  }
}).catch(function (error) {
  console.error(error);
});
```

#### Payment Settlement (Payment Finalization):

```javascript
mellat.settlePayment({
  orderId: '12345678912', // OrderID Used In Payment Request
  saleOrderId: '12345678912', // Get From Payment Callback Post Params
  saleReferenceId: '5142510', // Get From Payment Callback Post Params
}).then(function (response) {
  if (response.resCode === '0') {
    console.log("Payment Is Done.");
  } else if (response.resCode === '45') {
    console.log("Payment Already Done(Settled Before).");    
  } else {
    console.warn('Gateway Error: ', response.resCode);
  }
}).catch(function (error) {
  console.error(error);
});
```

### Callbacks Instead Of Promises

All methods can be called by using Callbacks instead of Promises, lets take `paymentRequest` as an example:

```javascript
mellat.paymentRequest({
  amount: 1000, // Payment Amount In Rials
  orderId: '12345678912', // OrderID Generated By You
  callbackUrl: 'https://call.back/mellat', // Payment Callback URL
  payerId: '0' // Optional
}, function (error, response) {
  if (error) {
    console.error(error);
  } else if (response.resCode === '0') {
    console.log(response.refId);
  } else {
    console.warn('Gateway Error: ', response.resCode);
  }
});
```

## TODOS

### API-TODO

- [x] bpPayRequest
- [x] bpVerifyRequest
- [x] bpSettleRequest
- [ ] bpInquiryRequest
- [ ] bpReversalRequest
- [ ] bpDynamicPayRequest
- [ ] bpCumulativeDynamicPayRequest

### Helper Methods

- [ ] verifyAndSettle (Verify the payment and settle/reverse it)

### Code

- [ ] JSDocs and code comments
- [ ] Unit tests using `mocha`

## Contributing

Contributions are welcome. Please submit PRs or just file an Issue if you see something broken or in need of improving.
