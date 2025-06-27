# Play NFC: HarmonyOS's tips to make your phone change and stick to

> As a patient with cancer who can use NFC to brush the door at any time, today I must talk to you about how delicious HarmonyOS's NFC is!From swiping the bus card to the simulated gate ban, to my own DIY tag, I can see these gameplays that are very practical, so I can remember them with a tight pen~

## 1. What can NFC do?Little life help is done

The first time I discovered the wonderful use of NFC was that once I didn’t have a transportation card in the ground railway, I ended up past the gate after I posted the phone!In fact, HarmonyOS's NFC has two absolutes:

- **Reading and writing tags**: Like recharge bus cards, bookstore borrowing books, you can read information by sticking your phone in a close-to-response area, and even write data by yourself. For example, you can post an NFC tag on your desk, and automatically connect to the company Wi-Fi as soon as you touch your phone~
- **Simulation Card**: The most commonly used one is to ban cards at the door, and you no longer have to worry about forgetting to bring a key when you go out; you can also simulate a bank card, and you can pay when buying something, which is much faster than paying for a money bag!

## 2. DIY NFC tag: Make life more clear

### 1. Common tag types

Different tags are suitable for different scenes, and a form has been compiled to facilitate everyone to take a seat:

| Tag type | Applicable scene | How do I use it |
|----------------|--------------------------|---------------------------|  
| NfcA | Bus cards, door bans | Simulated door bans, no more keys |
| NDEF | Save text, URL | Post it in the notebook and open the working file with one touch |
| MifareUltralight | Simple deposit (door tickets, coupons) | Save movie tickets into your mobile phone, don’t be afraid of losing your tickets |

### 2. Front desk reading: Brush it after opening the application

If you want to write the data to the tag yourself, you will actually have a few lines of code.For example, post a tag to your desk and open a work email as soon as you touch it:

```typescript
// Check that the equipment does not support NFC
if (!canIUse("SystemCapability.Communication.NFC.Core")) {
console.log('The device does not support NFC, crying');
    return;
}

// Enable the front desk reading mode, and you can only read it after opening the application.
tag.enableForegroundDispatch(() => {
console.log('Be ready to read and write NFC tag');
});

// Read the tag data (see NfcA type as an example here)
tag.getNfcA(tagInfo).then((nfcATag) => {
    nfcATag.read().then((data) => {
console.log('Readed data:', data);
// For example, when you read the URL, you will automatically open the web page.
        if (data.includes('workEmail.com')) {
            openUrl('workEmail.com');
        }
    });
});
```  

### 3. Backstage identification: You can feel it without opening the application

What's even more amazing is the background mode, such as putting your phone in your bag, and it will automatically feel when it is blocked.This requires that the type of card that can be processed in the configuration file:

```json
{
  "abilities": [
    {
      "skills": [
        {
          "actions": ["ohos.nfc.tag.action.TAG_FOUND"]
        }
      ],
      "metadata": [
        {
          "name": "payment-aid",
"value": "A0000000031010" // This is the ID of the simulated bank card
        }
      ]
    }
  ]
}
```  

## 3. Simulation card: Mobile phone transforming energy card

The most favorite thing is the HCE card simulation function, "reset" the ban card to the mobile phone:

```typescript
// Check the device first to support HCE
if (!cardEmulation.hasHceCapability()) {
console.log('The device does not support card simulation, it is difficult');
    return;
}

// Start HCE service and indicate the type of card that can be processed
let hceService = new cardEmulation.HceService();
let aidList = ["A000000031010"]; // This is the ID of the simulated door ban card
hceService.start(elementName, aidList);

// When the card reader issues a command, return the response data
hceService.on('hceCmd', (cmd) => {
// The data is returned here according to the agreement on the door-ban card
let response = [0x90, 0x00]; // Example response, it actually needs to be modified according to the card agreement
    hceService.transmit(response);
});
```  

## The last murmur

I remember that when I started learning NFC, I studied it all night to simulate the company's door ban, and later I found that the key is to configure AID and agreement.Now I have posted several custom NFC tags on my phone: I open the public software when I touch the tag on the desk, and close all home appliances when I touch the tag on the bed. It is really super convenient!
