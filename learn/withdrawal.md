---
description: >-
  Withdrawal is how you turn your Hako vault position back into assets in your
  wallet. You burn part of your vault shares and receive assets on a network of
  your choice.
icon: money-from-bracket
---

# Withdrawal

#### 1. Switch _Withdrawal_ Form

<figure><img src="../.gitbook/assets/Screenshot 2026-03-06 at 12.28.51.png" alt="" width="375"><figcaption></figcaption></figure>

#### 2. Choose amount and token

Enter the amount you want to withdraw. Then choose the destination network and the token you want to receive there.

#### 3. Sign the withdrawal request

Click `Withdraw`

Your wallet will open a message-signing dialog, not a normal gas-paying transaction. By signing, you authorize Hako to create and process the withdrawal request for the amount, network, and token you selected.

> After you sign, the request is submitted on your behalf, added to the withdrawal queue, and shown as `Pending` in the app. The relevant portion of your vault shares stays locked until the request is processed.

{% hint style="info" %}
Processing time depends on market and network conditions. It is usually relatively short, but it is not instant and not guaranteed.
{% endhint %}

<figure><img src="../.gitbook/assets/Screenshot 2025-12-02 at 16.15.06.png" alt="" width="375"><figcaption></figcaption></figure>

{% hint style="info" %}
Hako covers the gas for this request-submission step. In your wallet, you are authorizing the withdrawal request, not sending a gas-paying transaction yourself.
{% endhint %}

#### 4. Wait for processing

After you sign the withdrawal request, it will be processed based on market and network conditions.

**What happens next**

1. The request stays visible as `Pending` in the app while Hako processes it.
2. You can track progress in the app while waiting for completion.
3. Withdrawals are usually completed in under 30 minutes, but can take longer during network congestion, route constraints, or unusual market conditions.

{% hint style="info" %}
Withdrawals are usually completed in under 10-30 minutes, but timing is not guaranteed.
{% endhint %}

#### 5. Confirm funds in your wallet

Once completed, assets arrive in your wallet on the destination network and token you selected.
