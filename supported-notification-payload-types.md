---
description: >-
  Explains the supported notification payload types and the payload data they
  carry.
---

# Notification Payload Types

## Payload Types

Ethereum Push Notification Service supports a variety of payload types and the list is ever expanding. 

{% hint style="info" %}
The JSON Payload can differ with payload types ensuring flexibility of the content, data, storage interpretation and delivery.
{% endhint %}

| Payload Variable | Description |
| :--- | :--- |
| **notification**  | Represents the notification typically delivered on the home screen of the platform \(mobile, tablet, web, etc\), the icon of the channel is automatically added to outline where the notification is coming from. |
| title | The title of the message displayed on the screen, this differs from the **data json** because the title while transforming the payload can be different than the title presented. For example, secret notification title are always transformed to say **Channel has sent you a secret notification** |
| body | The body of the message displayed on the screen, this differs from the **data json** because the title while transforming the payload can be different than the title presented. For example, secret notification body are always transformed to say **Please open the dApp / app to view your notification** |
| **data** | The data the notification will carry, this allows the notication to transform |

### Broadcast Notification

Broadcast notification goes to all subscriber of a channel, the notification payload in this case is not encrypted.

```text
{
  "notification": {
    "title": "The title of your message displayed on screen (50 Chars)",
    "body": "The intended message displayed on screen (180 Chars)"
  },
  "data": {
    "type": "1",
    "secret": "",
    "asub": "[Optional] The subject of the message displayed inside app (80 Chars)",
    "amsg": "The intended message displayed inside app (500 Chars)",
    "acta": "[Optional] The cta link parsed inside the app",
    "aimg": "[Optional] The image url or youtube url which is shown inside the app"
  }
}
```

### Secret Notification

Secret notifications are intended to be delivered to one subscriber of the channel, these are encrypted using ECIES\(Elliptic Curve Cryptography\) and AES\(Advanced Encryption Standard\). The secret which is generated by the channel using whatever means they prefer should be kept to 15 characters or less, this secret \(plain version\) uses AES to encrypt the fields: **asub, amsg, acta, aimg**.

The rationale behind using ECIES with AES is to ensure that the payload is not over bloated.

```text
{
  "notification": {
    "title": "The title of your message displayed on screen (50 Chars)",
    "body": "The intended message displayed on screen (180 Chars)"
  },
  "data": {
    "type": "2",
    "secret": "No more than 15 characters, encrypted using public key of the intended recipient",
    "asub": "encrypted by secret using AES | [Optional] The subject of the message displayed inside app (80 Chars)",
    "amsg": "encrypted by secret using AES | The intended message displayed inside app (500 Chars)",
    "acta": "encrypted by secret using AES | [Optional] The cta link parsed inside the app",
    "aimg": "encrypted by secret using AES | [Optional] The image url which is shown inside the app"
  }
}
```

{% hint style="info" %}
Why not just use ECIES? ECIES increases the length of the cipher text and hence the payload which will be delivered. Using ECIES with AES ensure the payload length is kept at a manageable level and allows channels to send more information in the notification while still keeping the best encryption practices.
{% endhint %}

#### Example Notification Payload \(Plain\)

```text
{
  "notification": {
    "title": "The title of your message displayed on screen (50 Chars)",
    "body": "The intended message displayed on screen (180 Chars)"
  },
  "data": {
    "type": "2",
    "secret": "vBGK71PFl7mzWob",
    "asub": "The Great Renewal: Your ENS Domain has expired and someone is about to get them",
    "amsg": "[d:ENS] domains from 2017 that have expired.\n\nGo check your [b:@ensdomains] right now and renew your accounts.",
    "acta": "https://ens.domains/",
    "aimg": "https://i.ibb.co/WKNVN9y/enssamplemsgimg.jpg"
  }
}
```

{% hint style="info" %}
**Recommended** to use the **EPNS** **JS Library** to handle the generation of encrypted payload easily, it can talk to our protocol to also fetch the public key required.
{% endhint %}

#### Example Notification Payload \(Encrypted\)

```text
{
  "notification": {
    "title": "The title of your message displayed on screen (50 Chars)",
    "body": "The intended message displayed on screen (180 Chars)"
  },
  "data": {
    "type": "2",
    "secret": "e291826a995ab03b0dd69c360f3deb3803e130dc7d3bd94f1016494bc1fad4f4b816524f8cbdd068f0caf94a1cec682ab75755327e4410267721e44f83d73a56e88b911051eb0a2f2ee1ffef5f2cf5419cbc81895cd7d290b70060ef80b727fd52",
    "asub": "U2FsdGVkX181J09umWprAgmLOaDyZXojQjLPlJ31G0LDgXHBgnNsFEOKgjqhKJ2vWaPP5Xmt8sIQLmB3YYkjQO1LhrV7sr0FDlwqjLhSimxmI1EnjOdEHyiE1RO7LV0O",
    "amsg": "U2FsdGVkX18J7Myet9yljBLtNMpqz86qWgmjrK/9WyP+LD9OVerohkl5jc791UOlU6cV4UFVhdwJHyQSMYNNDPOaJMhxlLF2tL7LIBDeGqPA2AlgWqe2qbF1JC+zjIgBR/+IUfbr0+gz4JUBydK3d1dJGPFYliQTqD7EOjv38No=",
    "acta": "U2FsdGVkX188zXRR3URQR2xedjftDOHD5E3k+ggKe+8F6MxW86464rl6y1ZhX3jY",
    "aimg": "U2FsdGVkX188AaU187LFzqaibpfoOXb+XkCNbsLpV29CrQOVjC9BfWpxwGXE9Er7OdJ63yblqFYCaqNoGAHCOg=="
  }
}
```


