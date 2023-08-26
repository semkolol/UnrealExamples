---
title: PlayMontage
slug: PlayMontage
docs: https://docs.unrealengine.com/5.2/en-US/API/Runtime/Engine/GameFramework/ACharacter/PlayAnimMontage/
---

<br />

_Description from [Documentation](https://docs.unrealengine.com/5.2/en-US/API/Runtime/Engine/GameFramework/ACharacter/PlayAnimMontage/):_
Play Animation Montage on the character mesh. Returns the length of the animation montage in seconds, or 0.f if failed to play.

<br />


**YourCharacter.h**

```cpp
class AYourCharacter : public ACharacter
{
...
  // Some Function to Play the Montage from
  // In this example we're playing a Attack Animation
  // this is bind to the Left Mouse Button, please check out EnhancedInput
  // to implement Input
  void Attack(const FInputActionValue& Value);

  // after compiling: go into unreal in your Character Blueprint
  // under YourGame|Animation set as many Animation Montages as you want
  // in my case there will be four.
  // You can also just use a single AnimMontage: UAnimMontage* YourMontage;
  UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "YourGame|Animation")
  TArray<UAnimMontage*> YourAnimations;

  // Optional: if you also want to have multiple Animations
  // we need these variables to cycle through them
  UPROPERTY(VisibleAnywhere, BlueprintReadWrite, Category = "YourGame|Animation")
  int32 ComboCount = 0; // current animation playing
  UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "YourGame|Animation")
  int32 MaxCombo = 3; // the number of animations in your array - 1
...
}
```

<br />



**YourCharacter.cpp**

```cpp

void AYourCharacter::Attack(const FInputActionValue& Value)
{
  // call PlayAnimMontage, which is inherited from Character.h
  // In the Arguments either add the Single AnimMontage of yours
  // or the Array with multiple Animations as I did here:
  PlayAnimMontage(YourAnimations[ComboCount]);

  // after playing the animation increase the ComboCount
  ComboCount++;
  // if ComboCount is bigger than MaxCombo, then reset ComboCount to 0
  if (ComboCount > MaxCombo)
  {
    ComboCount = 0;
  }
}

```

<br />


Optional, Animation Notifies in C++ and minimal BP:

First Create a Animation Montage from your Animation Sequence, if you didn't already.

<br/>
Then add Notifiers, by right-clicking your Notifies Track:

![AnimMontage1](/AnimMontage1.png)

<br/>
In my case I added "Hit" and "HitEnd". You can call them however you want, but keep the different naming in mind for later.

![AnimMontage0](/AnimMontage0.png)

<br/>
In your Characters Animation Blueprint you can now add the Notifiy Events.
Also, Target is MyCharacter Class, you can just Cast to your Character Class.

![AnimMontage2](/AnimMontage2.png)

<br/>

Where is this Function "OnNotifyBegin" coming from and whats it for?

<br/>

**YourCharacter.h**

```cpp

class AYourCharacter : public ACharacter
{
  // define the function that should execute when the Notifies hit
	UFUNCTION(BlueprintCallable)
	void OnNotifyBegin(FName NotifyName);
}

```

<br />

**YourCharacter.cpp**

```cpp

void AYourCharacter::OnNotifyBegin(FName NotifyName)
{
	if (NotifyName == "Hit")
	{
      // do something when the Notify "Hit" hits
      // in my case I enable and disable the collision of my characters sword
      if (EquippedSword)
      {
        EquippedSword->bIsCollisionEnabled = true;
      }
	}
	if (NotifyName == "HitEnd")
	{
      // do something when the Notify "HitEnd" hits
      // and disable collision on "HitEnd"
      if (EquippedSword)
      {
        EquippedSword->bIsCollisionEnabled = false;
      }
	}
}

```

<br />