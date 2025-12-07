---
description: >-
  Withdrawal is how you turn your Hako vault position back into stablecoins in
  your wallet.  You burn part of your vault LP tokens and receive stablecoins on
  a network of your choice.
icon: money-from-bracket
---

# Withdrawal

#### 1. Open _Withdrawal_ Form

<figure><img src="../.gitbook/assets/Screenshot 2025-12-02 at 16.07.31.png" alt="" width="375"><figcaption></figcaption></figure>

#### 2. Choose amount, network, and token

Enter the amount you want to withdraw. Then choose the destination network and the token you want to receive there.

#### 3. Sign the withdrawal request

Click `Withdraw`

Your wallet will open a message-signing dialog. This signed message authorizes the vault to burn the corresponding LP tokens and send funds to you later.

> After that the request is added to the on-chain withdrawal queue and the relevant portion of your LP tokens is locked until the request is processed. In the app you’ll see the request marked as `Pending` .

{% hint style="info" %}
Processing time depends on market and network conditions. It is usually relatively short, but it is not instant and not guaranteed.
{% endhint %}

<figure><img src="../.gitbook/assets/Screenshot 2025-12-02 at 16.15.06.png" alt="" width="375"><figcaption></figcaption></figure>

{% hint style="info" %}
You **do not spend any ETH or other gas** for this step.
{% endhint %}





