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
Transaction Hash: 0xf4863f809dfbaa462829d05eff06767bcfc09cea42f2b42dc506e589f7350c10
Output Index: 0
Spore ID: 0xf516e888a7d0c9da3b9adb97931c2c535a17e1bce05e54da8f5ef53b0e917349
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
0x4505e527bfd907c52dc9e27336c64c58c5be3765977d32d761763e3de7d1b0e1

Output Index:
0
```

### CKB Testnet Explorer

```text
[View my Testnet transaction on CKB Explorer](https://testnet.explorer.nervos.org/transaction/0x4505e527bfd907c52dc9e27336c64c58c5be3765977d32d761763e3de7d1b0e1)
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

During my tests with the available account capacity, larger images triggered capacity-related problems

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

**My personal reflection:**

Completing the Create-a-DOB tutorial gave me a much clearer understanding of how CKB and Spore Digital Objects work in practice. Before starting, I understood the general idea of putting digital assets on a blockchain, but actually creating a DOB and later retrieving the same image from its Spore Cell made the idea of “on-chain content” much more concrete to me.

One of the first problems I encountered was related to my Windows development environment. The tutorial used the command NETWORK=devnet npm start, but Windows Command Prompt did not recognize the NETWORK assignment. I initially thought there might be something wrong with the project, but I later discovered that the command uses Unix-style environment-variable syntax. I solved it by using set "NETWORK=devnet" && npm start instead. I encountered the same distinction again when moving to Testnet, which taught me to pay closer attention to the shell being used when following development documentation.

Another issue I encountered was the Not enough capacity in from infos! error while trying to create the Spore DOB. This made me investigate the account being used, the available CKB capacity, and the size of the image I was trying to store. This was one of the parts of the tutorial that helped me understand that a Spore DOB is not simply a token containing a link to a file. The actual content occupies space inside a CKB Cell, so sufficient capacity is required to create the object.

The part I found most interesting was seeing how the image moved through the application. The picture was first read as an ArrayBuffer, converted into a Uint8Array, and then passed to Spore SDK as the content of the new Digital Object. After the DOB was created, the application used the transaction hash and output index to find the live Spore Cell, decode its stored data, turn the retrieved bytes back into a browser-readable Blob, and render the image again. Seeing the image reappear from data retrieved from the Cell helped me understand what it means for the content itself to be stored on-chain.

Moving from the local OffCKB Devnet to CKB Testnet also taught me something important. Immediately after creating the Testnet DOB, I tried to check the Spore content and received the message cell not found, please retry later. At first I thought something had failed, but after waiting for some time and trying again, the Cell became available and the image rendered successfully. This showed me that broadcasting a blockchain transaction and being able to retrieve its newly created output are not necessarily immediate events, especially on a shared network such as Testnet.

Overall, the tutorial helped me understand CKB through actual interaction rather than only reading about it. The most important thing I took away from the process is that a Spore DOB can contain the digital content itself inside a CKB Cell. Creating the image, waiting for the transaction, locating the Cell, decoding its data, and rendering the image again made the concept much easier for me to understand. I also learned that debugging blockchain applications can involve several layers at once, including the operating system, development tools, wallet capacity, transaction confirmation, and the blockchain itself.

---

## DOBs vs NFTs

One of the main differences I noticed between Spore DOBs and conventional NFTs is how the digital content is handled.

Many traditional NFTs represent ownership of a token on the blockchain while the associated image or media may be stored somewhere else and referenced through metadata or a URL. With the Spore DOB used in this tutorial, the actual content is stored directly inside a CKB Cell.

A Spore Cell can contain information such as:

content-type
content
cluster_id (optional)

In my case, the JPEG image was converted into binary data and stored as the content of the Spore Cell. When I later clicked Check Spore Content, the application located the Cell using the transaction hash and output index, retrieved the stored data, decoded it, and reconstructed the image in the browser.

This made the difference much clearer to me. The image being displayed was not simply being loaded again from the original file on my computer or from an external image URL. It was reconstructed from the content retrieved from the Digital Object on CKB.

Another characteristic I found interesting is that the fields of a Spore Cell are immutable after the DOB is created. This gives the object a strong connection between its blockchain identity and the content stored inside it.

From this tutorial, I now understand a Spore DOB as more than just a token representing an asset. It can be a self-contained digital object whose actual content and ownership exist within CKB's Cell model.

The simplest way I would describe the difference is:

Typical NFT:
Blockchain token
      ↓
Metadata
      ↓
Often references external content

Spore DOB:
CKB Cell
      ↓
Digital object data
      ↓
Actual content stored on-chain

This was one of the most interesting concepts I learned from the tutorial because I was able to see the complete process myself: create the object, store the image content, retrieve the Cell, decode the content, and render the same image again.

---

## Potential Use Case

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

---

## Proof Screenshots

The proof files for this campaign are organized as follows:

```text
proof/
├── Screenshot (1).png
├── Screenshot (2).png
├── ...
└── Screenshot (43).png
```

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