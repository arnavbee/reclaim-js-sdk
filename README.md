# Reclaim JavaScript SDK

This README provides a step-by-step guide on integrating the Reclaim Protocol JavaScript SDK into application

## Pre-requisites

- An application ID from Reclaim Protocol. You can get one from the [Reclaim Developer Protocol](https://dev.reclaimprotocol.org/)

## Create a new React application

```bash
npx create-react-app reclaim-app
cd reclaim-app
```

## Install the Reclaim Protocol JS-SDK

```bash
npm install @reclaimprotocol/js-sdk
```

## Install other dependencies

```bash
npm i react-qr-code
```

## Import dependencies

In your `src/App.js` file, import the Reclaim SDK and the QR code generator

```javascript
import { useState, useEffect } from 'react'
import { Reclaim } from '@reclaimprotocol/js-sdk'
import QRCode from 'react-qr-code'
```

## Initialize the Reclaim SDK

Declare your `application ID` and initialize the Reclaim Protocol client. Replace `YOUR_APPLICATION_ID_HERE` with the actual application ID provided by Reclaim Protocol.

File: `src/App.js`

```javascript
import './App.css'
import { useState, useEffect } from 'react'
import { Reclaim } from '@reclaimprotocol/js-sdk'
import QRCode from 'react-qr-code'

function App() {
  const APP_ID = 'YOUR_APPLICATION_ID_HERE'
  const reclaimProofRequest = new Reclaim.ProofRequest(APP_ID)

  return (
    <div className='App'>
      <header className='App-header'>
        <p>Reclaim App</p>
      </header>
    </div>
  )
}

export default App
```

## Implement Verification Request Function

Create functions to handle the verification request. You'll need separate functions for prototype and production modes due to the different handling of the application secret and signature.

### Prototype Mode

For testing purposes, use the prototype mode. Note that in production, you should handle the application secret securely on your server.

File: `src/App.js`

```javascript
import './App.css'
import { useState, useEffect } from 'react'
import { Reclaim } from '@reclaimprotocol/js-sdk'
import QRCode from 'react-qr-code'

function App() {
  const [requestUrl, setRequestUrl] = useState('')
  const [proofs, setProofs] = useState([])

  const APP_ID = 'YOUR_APPLICATION_ID_HERE'

  const reclaimProofRequest = new Reclaim.ProofRequest(APP_ID)

  async function createVerificationRequest() {
    // id of the provider you want to generate the proof for
    await reclaimProofRequest.buildProofRequest('PROVIDER_ID')

    reclaimProofRequest.setSignature(
      await reclaimProofRequest.generateSignature(
        'YOUR_APPLICATION_SECRET' // Handle securely for production
      )
    )

    const { requestUrl, statusUrl } =
      await reclaimProofRequest.createVerificationRequest()

    await reclaimProofRequest.startSession({
      onSuccessCallback: proofs => {
        console.log('Verification success', proofs)
        setProofs(proofs)
        // Your business logic here
      },
      onFailureCallback: error => {
        console.error('Verification failed', error)
        // Your business logic here to handle the error
      }
    })

    setRequestUrl(requestUrl)
  }

  return (
    <div className='App'>
      <header className='App-header'>
        <p>Reclaim App</p>
        <button
          onClick={createVerificationRequest}
          style={{ marginBottom: '20px', padding: '10px', cursor: 'pointer' }}
        >
          Create Verification Request
        </button>
        <div style={{ backgroundColor: 'white', padding: '10px' }}>
          {requestUrl && (
            <div style={{ backgroundColor: 'white', padding: '10px' }}>
              <QRCode value={requestUrl} />
            </div>
          )}
          {proofs.length > 0 && (
            <div>
             <h3 style={{color: 'black'}}>Proofs</h3>
              <ul>
                {proofs.map((proof, index) => {
                  return (
                    <div key={index}>
                    <p  style={{color: 'black'}}>
                      Identifier: {JSON.stringify(proof.identifier)}
                    </p>
                    <p style={{color: 'black'}}>
                    Signature: {JSON.stringify(proof.signatures)}
                    </p>
                    </div>
                  )
                })}
              </ul>
            </div>
          )}
        </div>
      </header>
    </div>
  )
}

export default App
```

### Production Mode

In production mode, securely fetch and set the signature from your backend instead of using the application secret directly in the client.

Similar to the prototype mode but ensure to fetch and set the signature securely

```javascript
async function createVerificationRequestProductionMode() {
  // id of the provider you want to generate the proof for
  await reclaimProofRequest.buildProofRequest('PROVIDER_ID')

  reclaim
    .setSignature
    // TODO: fetch signature from your backend
    // On the backend, generate signature using:
    // await Reclaim.getSignature(requestedProofs, APP_SECRET)
    ()

  const { requestUrl, statusUrl } =
    await reclaimProofRequest.createVerificationRequest()

  await reclaimProofRequest.startSession({
    onSuccessCallback: proofs => {
      console.log('Verification success', proofs)
      setProofs(proofs)
      // Your business logic here
    },
    onFailureCallback: error => {
      console.error('Verification failed', error)
      // Your business logic here to handle the error
    }
  })

  setRequestUrl(requestUrl)
}
```

## Run the application

```bash
npm start
```

## Advanced Configuration

You can configure Reclaim SDK to receive proofs in your preferref backend by setting up a callback URL. This is useful if you want to handle the proofs in your backend.

- **Set Callback Url** - `reclaim.setCallbackUrl('https://your-backend.com/receive-proofs')`
- **Set Status URL** - `reclaim.setStatusUrl('https://your-backend.com/receive-status')`

## Contributing to Our Project

We're excited that you're interested in contributing to our project! Before you get started, please take a moment to review the following guidelines.

## Code of Conduct

Please read and follow our [Code of Conduct](https://github.com/reclaimprotocol/.github/blob/main/Code-of-Conduct.md) to ensure a positive and inclusive environment for all contributors.

## Security

If you discover any security-related issues, please refer to our [Security Policy](https://github.com/reclaimprotocol/.github/blob/main/SECURITY.md) for information on how to responsibly disclose vulnerabilities.

## Contributor License Agreement

Before contributing to this project, please read and sign our [Contributor License Agreement (CLA)](https://github.com/reclaimprotocol/.github/blob/main/CLA.md).

## Indie Hackers

For Indie Hackers: [Check out our guidelines and potential grant opportunities](https://github.com/reclaimprotocol/.github/blob/main/Indie-Hackers.md)

## License

This project is licensed under a [custom license](https://github.com/reclaimprotocol/.github/blob/main/LICENSE). By contributing to this project, you agree that your contributions will be licensed under its terms.

Thank you for your contributions!
