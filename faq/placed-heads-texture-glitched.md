---
description: Some placed player heads have glitched/bugged texture
icon: head-side
---

# Placed heads texture glitched

{% hint style="info" %}
This is caused by the emotes feature of ItemsAdder and currently there is no fix other than disabling them.
{% endhint %}

This bug happens if you have more than 1 player head of the same player in front of you.\
This doesn't happen if you have different player heads in front of you or if you have only 1 head in front of you.

This bug is also reproducible in **OriginRealms** server and will be reproducible with any other custom emotes system that will be released by other developers.\
I probably can't fix this bug in any way because it's how the game rendering system works.

I'll do my best to find a workaround but I doubt this game allows me to identify placed heads in order to fix that.

## How to fix

The only way to fix this is to disable emotes system in `config.yml` of **ItemsAdder**.\
Don't forget to regenerate the resourcepack using `/iazip`.

![](<../.gitbook/assets/image (52).png>)

{% embed url="https://youtu.be/Gn7uzcSmjmU" %}
