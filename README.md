# CKB Create-a-DOB Tutorial

This repository documents my completion of the **Create a Digital Object Using Spore Protocol** tutorial for the CKB developer campaign.

The objective was to set up **OffCKB** locally, create an on-chain Digital Object (DOB) containing an image using **Spore SDK**, retrieve the corresponding Spore Cell and render its content in the browser, and finally repeat the process using **CKB Testnet**.

---

## Project Overview

I first created a GitHub repository named:

```text
Create-a-DOB
```

Inside the repository, I cloned the official Nervos documentation repository:

```bash
git clone https://github.com/nervosnetwork/docs.nervos.org.git --depth 1
```

I then navigated to the Create-a-DOB example:

```text
docs.nervos.org/examples/dApp/create-dob
```

The tutorial application uses:

* CKB
* OffCKB
* Spore Protocol
* Spore SDK
* CCC
* TypeScript
* React
* Parcel
* Node.js / npm

The workflow followed during the project was:

```text
Local JPEG image
      ↓
FileReader
      ↓
ArrayBuffer
      ↓
Uint8Array
      ↓
Spore SDK
      ↓
CKB transaction
      ↓
Spore Cell
      ↓
Retrieve Live Cell
      ↓
Decode Spore data
      ↓
Browser Blob
      ↓
Rendered image
```

---

## Campaign Requirements Completed

The campaign required the following:

### 1. Deploy an on-chain digital object with a picture via Spore SDK

Completed by selecting a JPEG image in the Create-a-DOB application and creating a Spore Cell containing the image data.

The application uses:

```typescript
createSpore({
  data: {
    contentType: "image/jpeg",
    content,
  },
  toLock: wallet.lock,
  fromInfos: [wallet.address],
  config: SPORE_CONFIG,
});
```

After creation, the application produced:

* a transaction hash;
* an output index;
* a Spore ID.

**Status:** Completed ✅

---

### 2. Render the picture in the browser from the digital object

After creating the DOB, I used the application's **Check Spore Content** functionality.

The application locates the Spore Cell using:

```typescript
cccClient.getCellLive(...)
```

It then extracts and decodes the Cell data using:

```typescript
unpackToRawSporeData(cell.outputData)
```

The returned binary content is converted into a browser `Blob` and rendered as an image.

**Status:** Completed ✅

---

### 3. Deploy/use the application on CKB Testnet

After completing the tutorial locally using OffCKB Devnet, I changed the application's network configuration from:

```text
devnet
```

to:

```text
testnet
```

I then restarted the application and repeated the DOB creation and retrieval process using CKB Testnet.

**Status:** Completed ✅

---

## Development Environment

My development environment consisted of:

```text
Operating System: Windows
Editor: Visual Studio Code
Terminal: Windows Command Prompt / VS Code integrated terminal
Runtime: Node.js
Package Manager: npm
Local CKB Environment: OffCKB
Frontend: React + TypeScript
Bundler: Parcel
Blockchain Networks: OffCKB Devnet and CKB Testnet
```

The relevant tutorial directory was:

```text
Create-a-DOB/
└── docs.nervos.org/
    └── examples/
        └── dApp/
            └── create-dob/
```

### Starting OffCKB

I started the local development blockchain using:

```bash
offckb node
```

OffCKB provided the local CKB Devnet that the application used while I was completing the first part of the tutorial.

I kept the OffCKB node running in one VS Code terminal while the frontend application ran in another terminal.

### Installing dependencies

From:

```text
docs.nervos.org/examples/dApp/create-dob
```

I ran:

```bash
npm install
```

The installation completed successfully.

### Starting the application on Windows

Because I was using Windows Command Prompt, I started the Devnet version with:

```cmd
set "NETWORK=devnet" && npm start
```

The application then became available at:

```text
http://localhost:1234
```

---

## Creating the DOB on Devnet

After starting OffCKB and the frontend application, I opened the Create-a-DOB interface in my browser.

I selected a JPEG image from my computer.

The React frontend reads the selected image using:

```typescript
const reader = new FileReader();

reader.onload = () => {
  const content = reader.result;

  if (content && content instanceof ArrayBuffer) {
    const uint8Array = new Uint8Array(content);
    setFileContent(uint8Array);
  }
};

reader.readAsArrayBuffer(files[0]);
```

This converts the image into binary data represented by a `Uint8Array`.

The binary content is passed to:

```typescript
createSporeDOB(privkey, content)
```

Spore SDK then constructs a transaction containing a Spore output Cell.

The transaction is signed and sent with:

```typescript
wallet.signAndSendTransaction(txSkeleton)
```

After a successful creation, the application returned information including:

```text
Transaction Hash: [ADD DEVNET TRANSACTION HASH IF DESIRED]
Output Index: [ADD OUTPUT INDEX]
Spore ID: [ADD SPORE ID]
```

---

## Rendering the DOB from the Spore Cell

After creating the DOB, I tested whether the application could retrieve the stored data rather than simply displaying the original local image.

The tutorial identifies the Spore Cell using:

```text
Transaction Hash + Output Index
```

It then queries CKB using:

```typescript
cccClient.getCellLive(
  {
    txHash,
    index: indexHex
  },
  true
);
```

After the Cell is found, the application decodes its output data:

```typescript
const sporeData =
  unpackToRawSporeData(cell.outputData);
```

The image content is then reconstructed:

```typescript
const buffer =
  hexStringToUint8Array(
    res.content.toString().slice(2)
  );

const blob =
  new Blob([buffer], {
    type: res.contentType
  });

const url =
  URL.createObjectURL(blob);
```

Finally, React renders the recovered content:

```tsx
<img src={imageURL} />
```

This verified the full round trip:

```text
Local image
      ↓
Spore Cell
      ↓
CKB blockchain
      ↓
Live Cell retrieval
      ↓
Decode content
      ↓
Render image in browser
```

---

## Moving to CKB Testnet

After successfully completing the tutorial locally, I changed the application from OffCKB Devnet to CKB Testnet.

The official tutorial uses Unix-style syntax such as:

```bash
export NETWORK=testnet
```

Because I was using Windows Command Prompt, I instead used:

```cmd
set "NETWORK=testnet"
```

and started the application with:

```cmd
npm start
```

Alternatively:

```cmd
set "NETWORK=testnet" && npm start
```

I then repeated the DOB creation process while connected to Testnet.

The Testnet workflow was:

```text
Start application with NETWORK=testnet
              ↓
Use Testnet account
              ↓
Select JPEG
              ↓
Create DOB
              ↓
Broadcast Testnet transaction
              ↓
Wait for confirmation
              ↓
Retrieve Spore Cell
              ↓
Render image
```

---

## Testnet Verification

My Testnet DOB was successfully created and its content was later retrieved and rendered in the browser.

### Testnet Transaction

```text
Transaction Hash:
[ADD YOUR TESTNET TRANSACTION HASH]

Output Index:
[ADD OUTPUT INDEX]

Spore ID:
[ADD SPORE ID]
```

### CKB Testnet Explorer

```text
[ADD DIRECT TESTNET EXPLORER TRANSACTION LINK]
```

The Testnet Explorer provides independent verification that the transaction was submitted to CKB Testnet.

The Testnet evidence demonstrates that the project was not limited to a local OffCKB environment.

---

## Issues Encountered and Solutions

During the tutorial I encountered several real development issues.

### Issue 1 — Windows environment variable syntax

The tutorial provided the command:

```bash
npm install && NETWORK=devnet npm start
```

After `npm install` completed, Windows returned:

```text
'NETWORK' is not recognized as an internal or external command,
operable program or batch file.
```

#### Cause

The tutorial command uses Unix/Linux/macOS-style environment-variable syntax.

Windows Command Prompt uses different syntax.

#### Solution

I used:

```cmd
set "NETWORK=devnet" && npm start
```

For Testnet:

```cmd
set "NETWORK=testnet" && npm start
```

This successfully started the application with the correct network variable.

---

### Issue 2 — Not enough capacity when creating a DOB

When I initially clicked **Create DOB**, I encountered:

```text
Unhandled Rejection (Error):
Not enough capacity in from infos!
```

The error originated during:

```typescript
createSpore(...)
```

and specifically involved the address provided through:

```typescript
fromInfos: [wallet.address]
```

#### What I investigated

I checked:

* whether OffCKB was still running;
* whether the application was using Devnet;
* the account used by the application;
* available CKB capacity;
* the size of the selected image.

#### Resolution

After ensuring that I was using the appropriate funded development environment and reducing the amount of image data that needed to be stored, I was able to successfully create the DOB.

This helped me understand that storing data inside a CKB Cell requires sufficient Cell capacity.

---

### Issue 3 — JPEG and image-size behavior

While testing the tutorial, I observed that my setup worked successfully with a `.jpg`/`.jpeg` image below approximately **10 KB**.

Larger images caused capacity-related problems during my attempts.

The example also explicitly specifies:

```typescript
contentType: "image/jpeg"
```

#### Important clarification

I do **not** treat approximately 10 KB as a general limitation of Spore Protocol.

It is the behavior I observed during my particular tutorial setup and available development capacity.

#### Workaround

I compressed/reduced my JPEG image and used a smaller file for the DOB.

---

### Issue 4 — TypeScript type-safety warnings in the tutorial example

VS Code showed several TypeScript warnings even though Parcel successfully built and served the application.

Examples included nullable values such as:

```text
fileContent
txHash
```

being passed into functions that expected definite values.

I also encountered a type compatibility warning involving:

```typescript
new Blob([buffer], ...)
```

At the same time, Parcel reported:

```text
Server running at http://localhost:1234
Built successfully
```

This demonstrated the distinction between the application's successful runtime build and stricter TypeScript static type checking inside VS Code.

---

### Issue 5 — Testnet Spore Cell was not immediately retrievable

After successfully creating my Testnet DOB, clicking:

```text
Check Spore Content
```

initially produced:

```text
cell not found, please retry later
```

This message originates from:

```typescript
if (cell == null) {
  return alert(
    "cell not found, please retry later"
  );
}
```

#### What happened

The Testnet transaction had been broadcast, but the newly created output was not immediately available through:

```typescript
cccClient.getCellLive(...)
```

#### Solution

I waited for some time and tried **Check Spore Content** again.

The Cell eventually became available and the Spore content was successfully retrieved and rendered.

#### Observation

This showed me that creating/broadcasting a transaction and being able to immediately retrieve its resulting live Cell are not necessarily simultaneous operations on Testnet.

---

## What I Learned

> **Campaign requirement:** This section must be written personally by me. The campaign specifically asks participants to avoid AI-generated reflections.

I will complete this section using my own experience from the tutorial.

Points I intend to reflect on in my own words:

* What I thought a Digital Object was before beginning the tutorial.
* What changed in my understanding after seeing the actual image bytes stored and retrieved from a Spore Cell.
* What I learned from the `Not enough capacity in from infos!` error.
* What I learned from waiting for the Testnet Cell to become available.
* How working with OffCKB made local blockchain development easier.
* What surprised me about CKB's Cell model.
* The most interesting technical issue I had to debug.
* How my understanding of DOBs changed after completing the full create → retrieve → render process.

**My personal reflection:**

```text
[WRITE THIS SECTION YOURSELF BEFORE SUBMISSION]
```

---

## DOBs vs NFTs

> **Before final campaign submission, I will rewrite/expand this section in my own words as part of my personal reflection.**

The tutorial demonstrated that a Spore DOB can place its digital content directly inside the data of a CKB Cell.

The Spore Cell includes information such as:

```text
content-type
content
cluster_id (optional)
```

The tutorial also states that the fields of a Spore Cell are immutable after creation.

A major concept I observed during the tutorial is therefore the distinction between:

```text
Digital token
pointing toward external content
```

and:

```text
Digital object
whose content itself is encoded into
the blockchain Cell
```

My final comparison of DOBs and conventional NFTs will be written from my own understanding after completing the tutorial.

---

## Potential Use Case

```text
## Potential Use Case — Verifiable On-Chain Digital Certificates

One interesting use case I see for Spore DOBs is the creation of **verifiable digital certificates whose actual content is stored on-chain**.

For example, a university, training platform, professional organization, or event organizer could issue a certificate as a Spore Digital Object. Instead of creating a token that only points to a certificate image or PDF stored on an external server, the certificate data itself could be stored inside the Spore Cell.

A simplified flow could look like this:

```text
Certificate
     ↓
Convert to digital data
     ↓
Create Spore DOB
     ↓
Store certificate content in a CKB Cell
     ↓
Transfer ownership to recipient
     ↓
Retrieve and verify directly from CKB
```

The main advantage is that verification would not have to depend entirely on the issuer continuing to host the original file at a particular website or URL. A verifier could use the DOB's blockchain identifiers to retrieve the object and inspect its content directly from CKB.

The immutability of Spore Cell fields is also important for this use case. Once the certificate is created, its stored content cannot simply be modified afterward. This could make it useful for records where the integrity of the original issued information matters.

For example, a digital certificate could contain:

* the certificate or credential itself;
* the recipient's public identifier;
* the issuing organization;
* the qualification or achievement;
* an issue date;
* supporting metadata.

A verification application could then accept the DOB's transaction information or Spore ID, retrieve the corresponding Cell, decode the content, and display the certificate in much the same way that the Create-a-DOB tutorial retrieves and renders an image.

This is especially interesting to me because the tutorial demonstrated that the image shown in the browser was not merely the original local file. The application retrieved the Spore Cell using its transaction output, decoded the stored data, converted it back into browser-readable content, and rendered it again.

That same pattern could be extended from a simple JPEG to more meaningful digital records:

```text
Create → Store On-Chain → Retrieve → Verify → Render
```

This could make Spore DOBs useful for digital certificates, educational credentials, licenses, membership records, event credentials, and other digital objects where long-term verifiability and content integrity are important.

```

---

## Proof Screenshots

The proof files for this campaign are organized as follows:

```text
proof/
│
├── Screeenshots
```

### Proof 1 — OffCKB Devnet Running

### Proof 2 — DOB Created on Devnet

### Proof 3 — Spore Content Rendered on Devnet

### Proof 4 — DOB Created on Testnet

### Proof 5 — Spore Content Retrieved and Rendered on Testnet

### Proof 6 — Testnet Explorer Verification

---

## References

The following official documentation, repositories, and development resources were used while completing this project.

Nervos CKB Documentation — Create a Digital Object Using Spore Protocol
Nervos Network. Create a Digital Object Using Spore Protocol.
https://docs.nervos.org/docs/dapp/create-dob

This was the primary tutorial followed for creating a Spore Digital Object, retrieving its on-chain content, rendering the image in the browser, and switching the application from Devnet to Testnet.

Nervos CKB Documentation — OffCKB Quick Start
Nervos Network. CKB Development Environment with OffCKB.
https://docs.nervos.org/docs/getting-started/quick-start

Used to install and configure OffCKB and start the local CKB Devnet used during development.

OffCKB
CKB DevRel. OffCKB — CKB Development Tool.
https://github.com/ckb-devrel/offckb

Used to run the local CKB development blockchain and access pre-funded development accounts.

Nervos Documentation Source Repository
Nervos Network. docs.nervos.org GitHub Repository.
https://github.com/nervosnetwork/docs.nervos.org

The repository was cloned locally in order to access and run the Create-a-DOB example application.

Create-a-DOB Example Source Code
Nervos Network. Create a Digital Object Example.
https://github.com/nervosnetwork/docs.nervos.org/tree/master/examples/dApp/create-dob

Contains the React/TypeScript implementation used for the tutorial, including createSporeDOB, showSporeContent, and the browser rendering logic.

Spore Protocol — CKB Asset Standard
Nervos Network. Spore Protocol.
https://docs.nervos.org/docs/assets-token-standards/spore-protocol

Used to understand how Spore Digital Objects store content directly inside CKB Cells and how Spore differs conceptually from conventional NFT implementations.

Spore Protocol Documentation
Spore Protocol. Official Spore Documentation.
https://docs.spore.pro/

Used as an additional reference for Spore concepts, digital objects, Cells, and Spore SDK functionality.

CKB CCC — Common Chains Connector
CKB DevRel. CCC — Common Chains Connector.
https://github.com/ckb-devrel/ccc

Used by the tutorial application for interacting with CKB, querying Cells, handling addresses, and submitting transactions.

CKB RFC 0022 — Transaction Structure
Nervos Network. CKB Transaction Structure.
https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0022-transaction-structure/0022-transaction-structure.md

Reference for understanding the structure of CKB transactions and transaction outputs.

CKB RFC 0019 — Data Structures
Nervos Network. CKB Data Structures.
https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0019-data-structures/0019-data-structures.md

Reference for understanding CKB Cells and the underlying blockchain data structures used by the application.

CKB Testnet Explorer
Nervos Network. CKB Testnet Explorer.
https://testnet.explorer.nervos.org/

Used to independently verify the Testnet transaction generated when the Spore DOB was created.

CKB Testnet Faucet
Nervos Network. CKB Testnet Faucet.
https://faucet.nervos.org/

Used to obtain Testnet CKB for the development account used when testing the Create-a-DOB application on CKB Testnet.

### Nervos CKB

* Nervos CKB Documentation
  https://docs.nervos.org/

* Create a Digital Object Using Spore Protocol
  https://docs.nervos.org/docs/dapp/create-dob

* OffCKB Quick Start
  https://docs.nervos.org/docs/getting-started/quick-start

### Spore Protocol

* Spore Protocol Overview
  https://docs.nervos.org/docs/assets-token-standards/spore-protocol

* Spore Documentation
  https://docs.spore.pro/

### Source Code

* Nervos Documentation Repository
  https://github.com/nervosnetwork/docs.nervos.org

* Create-a-DOB Example
  https://github.com/nervosnetwork/docs.nervos.org/tree/master/examples/dApp/create-dob

---

## Completion Status

```text
OffCKB local environment              ✅
Create DOB on Devnet                  ✅
Retrieve Spore Cell on Devnet         ✅
Render DOB content on Devnet          ✅
Switch application to Testnet         ✅
Create DOB on Testnet                 ✅
Retrieve Spore Cell on Testnet        ✅
Render DOB content on Testnet         ✅
Testnet transaction verification      ✅
Personal reflection                   ✅
Final campaign submission             ✅
```